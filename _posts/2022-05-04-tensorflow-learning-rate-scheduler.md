---
layout: post
title: Setting up custom learning rate schedulers in TF 2.0
date: 2022-05-08 00:00:00
categories: machine-learning tensorflow tf2.0
---


In ML training, it is essential to understand and utilize an approach to adjusting the learning rate of a model. It helps with applying regularization to the model to prevent overfitting.

`Learning rate decay` is an example of a regularization technique which dynamically adjusts the learning rate of a model during its training process. It reduces the learning rate of the model over epochs or steps.

There are 2 main approaches to using learning rate schedulers in TF 2.0:

* Using the callback `LearningRateSchduler` and applying your own function

* Creating a custom subclass of `tf.keras.optimizers.schedules.LearningRateSchedule`

What is the difference ? The main difference is that approach 1 is meant to be called from the `callbacks` kwargs in the `model.fit` call whereas the second approach allows you to pass it as an input to the optimizer `learning_rate` kwarg. 


### 1. Using the LearningRateScheduler callback

The callback class requires a function of the form:
{% highlight python %}
def my_lr_scheduler(epoch, lr):
  # custom code to adjust learning rate

  # return new learning rate
{% endhighlight %}

The custom function needs to handle 2 parameters: `epoch` and `lr` (learning rate). This callback will be invoked at the beginning of every epoch, passing in the current epoch and optimizer learning rate. The custom function will need to return the new learning rate value, which the callback uses to update the learning rate of the optimizer

To invoke the example callback above:
{% highlight python %}
from tensorflow.keras.callbacks import LearningRateScheduler
...

mymodel.fit(
	...
	callbacks=[LearningRateScheduler(my_lr_scheduler)]
)
{% endhighlight %}


### 2. Subclass the LearningRateSchedule base class


The `LearningRateSchedule` base class adjusts the learning rate per step / batch of training, rather than over an entire epoch. This is useful if you are training your model in steps rather than epochs. For example, in GAN training

Example of creating a custom LR scheduler class:
{% highlight python %}
from tensorflow.keras.optimizers.schedules import LearningRateSchedule

class LinearLRSchedule(LearningRateSchedule):
    def __init__(self, initial_learning_rate, max_iters, **kwargs):
        super(LinearLRSchedule, self).__init__(**kwargs)
        self.initial_learning_rate = initial_learning_rate
        self.max_iters = max_iters

    def __call__(self, step):
        new_lr = self.initial_learning_rate * (1 - (step / float(self.max_iters)))
        return new_lr

    def get_config(self):
        return {
            "initial_learning_rate": self.initial_learning_rate,
            "max_iters": self.max_iters
        }
{% endhighlight %}

During training, the subclass would be passed directly into the `learning_rate` kwargs of an optimizer object:
{% highlight python %}
import tensorflow as tf

optimizer = tf.keras.optimizers.SGD(learning_rate=LinearLRSchedule(1e-1, 100))
{% endhighlight %}


### Resources
[LearningRateScheduler Callback](https://www.tensorflow.org/api_docs/python/tf/keras/callbacks/LearningRateScheduler)

[LearningRateSchedule Class](https://www.tensorflow.org/api_docs/python/tf/keras/optimizers/schedules/LearningRateSchedule)

