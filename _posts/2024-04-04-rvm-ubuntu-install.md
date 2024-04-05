---
layout: post
title: RVM on Ubuntu 22.04 Jellyfish
date: 2024-04-04 00:00:00
categories: rvm ubuntu
---

[RVM Ubuntu]: https://github.com/rvm/ubuntu_rvm
[Github issue]: https://github.com/rvm/rvm/issues/5216#issuecomment-1206488598

I had to setup RVM recently on my Ubuntu 22.04 desktop. However, the original installation instructions were plagued with issues, namely with openssl version on Ubuntu 22.04 which caused conflicts with the ruby installation.

To fix this issue we need to:
* Install the Ubuntu version of RVM
* Install an older openssl version as a package in rvm
* Reference the above openssl package during the rvm install

Firstly, I had to install [RVM Ubuntu]. I followed the original instructions in the README. Ensure that any existing RVM installations are removed first.

After RVM is installed properly, to install ruby 3.0.0, these were the steps I took:

{% highlight shell %}
rvm pkg install openssl

rvm install ruby-3.0.0 --with-openssl-dir=/usr/share/rvm/usr
{% endhighlight %}

Only the above steps allowed me to get RVM to work.

Hope it helps someone!