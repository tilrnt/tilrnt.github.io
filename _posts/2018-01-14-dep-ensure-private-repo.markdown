---
layout: post
title:  "Vendoring private github repos using dep"
date:   2018-01-14 00:00:00 +0000
categories: go golang dep
---

When using ```dep``` for vendoring dependencies in a go project, I came across the issue of pulling down a private github repo. ```dep ensure -v``` keeps reporting of an error with the repo.

To overcome this, you can create a ```~/.netrc``` with your credentials to access the private repo. For example, when using github, you first need to create an ```Personal Github Token``` within your ```Account Settings```. Then create a ```~/.netrc``` file with the following format:

```
machine github.com
    login [GITHUB USERNAME]
    password [GITHUB TOKEN]
```

This is also documented in the dep repo:

[Private git repos using github token](https://github.com/golang/dep/blob/master/docs/FAQ.md#how-do-i-get-dep-to-consume-private-git-repos-using-a-github-token)
