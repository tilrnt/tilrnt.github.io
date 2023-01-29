---
layout: post
title:  "yum update Protected multilib versions"
date:   2018-04-12 00:00:00 +0000
categories: redhat linux
---

During a recent ```yum update```, it failed with multiple lines of

```
Error: Protected multilib versions:

iptables-1.4.21-24.el7.i686 != iptables-1.4.21-23.el7.x86_64

( more error lines like above )
....

```

From above, what the above error means is that for that specific package, version "X" of an RPM is installed for architecture `x86_64` while yum was also instructed to install version "Y" of that same rpm for architecture `i686`.

Rather than resolving each depedency manually, I enabled the Workstation Optional RPMs repo to locate the missing rpms and the problem was resolved:
```
sudo subscription-manager repos --enable=rhel-7-workstation-optional-rpms

sudo yum clean-all

sudo rm -rf /var/cache/clean

sudo yum update
```

## Additional Resources
[RedHat support ticket](https://access.redhat.com/solutions/2256321)

[Same Issue from HPE](https://support.hpe.com/hpsc/doc/public/display?docId=mmr_kc-0131971)
