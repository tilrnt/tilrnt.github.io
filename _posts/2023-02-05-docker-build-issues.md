---
layout: post
title: Fix docker network build issues
date: 2023-01-31 00:00:00
categories: docker networking
---

When running a docker build after the docker daemon is updated, the build logs keep failing with:

```
Could not connect to archive.ubuntu.com:80 (185.125.190.36), connection timed out Could not connect to archive.ubuntu.com:80 (91.189.91.39), connection timed out Could not connect to archive.ubuntu.com:80 (185.125.190.39), connection timed out
...
```

It turns out that the docker daemon is unable to use the host networking to do a `apt-get update` within the ubuntu container during the build process and as such is unable to call out to the remote host.

To fix the issue system wide we can create a `/etc/docker/daemons.json` file with the right naemserver entries and restart the docker daemon:

Firstly, run the following to get the host DNS server ip
```
nmcli dev show | grep 'IP4.DNS'
```

Create a file at `/etc/docker/daemons.json` with the following entries:
```
{
	"dns": ["my-nameserver-ip-from-above", "8.8.8.8"]
}
```

Restart the docker daemon
```
sudo systemctl restart docker.service

sudo systemctl status docker.service
```

As a test we can run the following image to see if it can do a nslookup of google.com from within a container:
```
docker run busybox nslookup google.com
```

The response should include the DNS server address from above:
```
Server:		X.X.X.X
Address:	X.X.X.X:53

Non-authoritative answer:
Name:	google.com
Address: 172.217.16.238

Non-authoritative answer:
Name:	google.com
Address: 2a00:1450:4009:819::200e

```

Hope it helps someone!