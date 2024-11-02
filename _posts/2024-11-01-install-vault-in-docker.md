---
layout: post
title: Install Vault in docker image
date: 2024-11-01 00:00:00
categories: vault docker 
---

[Official Vault install documentation]: https://developer.hashicorp.com/vault/install
[Vault docker hub]: https://hub.docker.com/r/hashicorp/vault
[Reported Issue]: https://github.com/hashicorp/vault/issues/10048

To install the `vault` client in a docker image, we can follow the [Official Vault install documentation].

However, this throws an error of `operation not permitted` when trying to run the vault client. This is due to vault trying to lock memory to prevent sensitive values being swapped to disk. This is a [Reported Issue] and also mentioned on [Vault docker hub].

We can overcome this by running the container with `--cap-add` flag:

```
docker run --cap-add=IPC_LOCK -d --name=dev-vault mycustomimage vault
```

However, this is not required if using `Raft integrated storage`. 

To resolve the issue, we can instead use a multistage build to copy the vault binary into the image rather than installing via package manager.

An example Dockerfile:
```
FROM hashicorp/vault:1.18 as vaultsource

FROM ubuntu:22.04 as base

COPY --from=vaultsource /bin/vault /usr/local/bin/vault
```

Using the above, we can create a custom image with a vault CLI that doesn't throw an operation not permitted error. 