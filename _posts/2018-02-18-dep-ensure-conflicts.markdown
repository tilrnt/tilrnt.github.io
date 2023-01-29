---
layout: post
title:  "Resolving dep ensure conflicts"
date:   2018-02-14 00:00:00 +0000
categories: golang dep
---

Sometimes when collborating on a golang project, it is possible to get dependency conflicts after running```dep ensure```.

The following is an approach I take to resolve them:

* Run ```dep ensure -v``` with verbose to debug the issue.

* Delete the repo's Gopkg.lock

* Clear out the ~GOPATH/src/pkg directory

* Re-run ```dep ensure -v```
