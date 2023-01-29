---
layout: post
title:  "Using journalctl to check hardware / bootup errors"
date:   2018-05-23 00:00:00 +0000
categories: redhat linux
---

While trying to figure out a hardware issue during startup, I discovered that on systemd systems, the `journald` daemon collects logs from early in the boot process.

One can use `journalctl` to view systemd logs for issues:
```
sudo journalctl -b -p err
```

One can then page through the errors list, if any, to resolve any issue.

## Resources
[Wikipedia](https://en.wikipedia.org/wiki/Systemd)

[DigitalOcean tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs)
