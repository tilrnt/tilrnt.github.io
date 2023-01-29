---
layout: post
title:  "Kubectl and KUBECONFIG"
date:   2018-01-12 00:00:00 +0000
categories: kubernetes kubectl
---
While working on a kubernetes based project, I had to set the __$KUBECONFIG__ env variable in order to access a private cluster.

Later, I started __minikube__ and ran __kubectl config view__. This resulted in all my kubectl calls to the private cluster failing.

The reason is due to the way kubectl config behaves when it detects the __$KUBECONFIG__ env variable. According to the docs:

{% highlight shell %}
kubectl config -h

Modify kubeconfig files using subcommands like "kubectl config set current-context my-context"

The loading order follows these rules:

  1. If the --kubeconfig flag is set, then only that file is loaded.  The flag may only be set once
and no merging takes place.

  2. If $KUBECONFIG environment variable is set, then it is used a list of paths (normal path
delimitting rules for your system).  These paths are merged.  When a value is modified, it is
modified in the file that defines the stanza.  When a value is created, it is created in the first
file that exists.  If no files in the chain exist, then it creates the last file in the list.

  3. Otherwise, ${HOME}/.kube/config is used and no merging takes place.
{% endhighlight %}

As stated in point 2 above, since my __$KUEBCONFIG__ is still present, when I started minikube, it merges the minikube settings into file pointed to by __$KUBECONFIG__, updating it by merging the contents of minikube, and sets minikube as the current context. Which is why all the kubectl calls are going to the minikube cluster only.

As a note to self, I need to remember to

{% highlight shell %}
unset $KUBECONFIG
{% endhighlight %}
