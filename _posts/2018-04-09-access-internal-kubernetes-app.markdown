---
layout: post
title:  "Using kube-proxy to access deployments"
date:   2018-04-09 00:00:00 +0000
categories: kubernetes kubectl proxy
---

When we create a service on k8 cluster, it is often initialized with type of ```ClusterIP```.

We can still access the service using ```kubectl proxy```.

```kubectl proxy``` allows one to interact with the API without the need for a Bearer token.

Assuming we have a service called ```guestbook```, we can access it as below:
```
kubectl proxy > /dev/null &

KC_PROXY_PID = $!

SERVICE_PREFIX=http://localhost:8001/api/v1/proxy

GUESTBOOK_URL = $SERVICE_PREFIX/namespaces/default/services/guestbook
```
