---
layout: post
title: Python 3.12 'pkgutil has no attribute ImpImporter' error
date: 2024-03-16 00:00:00
categories: python pip venv
---

While creating and using a virtual env created with python 3.12, I installed a package which resulted in `pip` throwing an error of:

{% highlight python %}
AttributeError: module 'pkgutil' has no attribute 'ImpImporter'. Did you mean: 'zipimporter'?
{% endhighlight %}

This occured after installing `setuptools` which was a dependency of another package. As a result, I was unable to use `pip` itself to remove setuptools.

Rather than re-create a new venv, the only way to to resolve this issue is to reinstall pip by downloading the pip install file from `https://bootstrap.pypa.io/get-pip.py` and running it again:

{% highlight python %}
curl https://bootstrap.pypa.io/get-pip.py -O get-pip.py

python get-pip.py
{% endhighlight %}

By doing so, I was able to get pip working again without rebuilding the entire venv.

This is highlighted in the python github repo:
https://github.com/python/cpython/issues/95299

