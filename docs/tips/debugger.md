---
layout: page
title: Attach a remote debugger
permalink: /docs/tips/debugger
---

You can connect a debugger on your host machine to a process in a container using `./bin/kubectl port-forward {pod name} {local port}:{container port}`.

For example, if a process in `client-node-0` listens for a debugger on port 8000, forward port 5005 on your host machine to it:

```console
$ ./bin/kubectl port-forward client-node-0 5005:8000
Forwarding from 127.0.0.1:5005 -> 8000
Forwarding from [::1]:5005 -> 8000
```
