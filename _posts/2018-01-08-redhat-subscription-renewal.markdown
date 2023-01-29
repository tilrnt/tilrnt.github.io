---
layout: post
title:  "Redhat Subscription Renewal"
date:   2018-01-08 00:00:00 +0000
categories: redhat linux
---
Recently I had to update my Redhat subscription.

Afterwards, the __subscription-manager__ application kept showing as "No valid subscriptions found". This was caused by a mismatch between the type of RHEL system I was running and the actual subscription type itself.

To ensure that one renews to the right subscription type, simply use the following:

{% highlight shell %}
cat /etc/redhat-release
{% endhighlight %}
