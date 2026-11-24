---
layout: page
title: Quick Start
permalink: /docs/quick-start
---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/){:target="_blank"}
  - ZooKage officially supports macOS
- [8+GB RAM](https://docs.docker.com/desktop/settings/mac/#advanced){:target="_blank"}
  - You need more when you launch more components
- [Kubernetes is enabled](https://docs.docker.com/get-started/orchestration/){:target="_blank"}
  - Use Kubernetes 1.33 or later with [image volume support](https://kubernetes.io/docs/reference/storage/volume-types/#image) enabled in the cluster and supported by its container runtime; ZooKage shares component binaries through image volumes with `subPath` mounts

## Instructions

### Set up a Hadoop cluster

```console
$ git clone --branch v0.6.0 https://github.com/zookage/zookage.git
$ cd zookage
$ ./bin/up
namespace/zookage created
...
All resources are ready!
```

### Log in to a client container

With `./bin/ssh`, you can ssh into `client-node`. [Various command-line tools](/docs/guide#connect-and-use) such as `hdfs`, `yarn`, `beeline`, etc. are available.

```console
$ ./bin/ssh
zookage@client-node-0:~$
```

### Submit a job

You can run any commands on `client-node`.

```console
zookage@client-node-0:~$ beeline
...
0: jdbc:hive2://hive-hiveserver2:10000/defaul> SELECT 1;
...
+------+
| _c0  |
+------+
| 1    |
+------+
1 row selected (6.432 seconds)
```

### Access web UI

You can access [ZooKage Console](http://localhost:3000/){:target="_blank"} and web UIs such as [YARN ResourceManager](http://localhost:8088/cluster){:target="_blank"} through your browser.

The list of available endpoints is [here](/docs/guide#web-ui).
