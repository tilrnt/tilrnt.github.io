---
layout: post
title:  "Fixing dep update reference not a tree error "
date:   2018-07-01 00:00:00 +0000
categories: golang dep
---

When running ```dep ensure -update <dependency> -v``` to update a depedency,
one might run into the following error:

```
Unable to update checked out version: fatal: reference is not a tree:
```

This is due to the cached version of the dependency in ```GOPATH/pkg/dep/sources/<depname>``` being in ```detached HEAD``` state.

To fix this, cd into the dep cache folder and update it manually:

```
cd GOPATH/pkg/dep/sources/<depname>

git checkout master # or branch specified in Gopkg.toml

git pull
```

Run ```dep ensure -update <dependency>``` again and it should work again.

This is an open issue on the golang [dep repository]{:target="_blank"}:

[dep repository]: https://github.com/golang/dep/issues/484
