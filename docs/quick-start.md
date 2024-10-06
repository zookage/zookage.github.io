---
layout: page
title: Quick Start
permalink: /docs/quick-start
---

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/){:target="_blank"}
  - ZooKage officially supports macOS
  - Some components might not start on a machine with Apple silicon + Rosetta 2
- [8+GB RAM](https://docs.docker.com/desktop/settings/mac/#advanced){:target="_blank"}
  - You need more when you launch more components
- [Kubernetes is enabled](https://docs.docker.com/get-started/orchestration/){:target="_blank"}

## Instructions

### Set up a Hadoop cluster

```console
$ git clone --branch v0.2.6 git@github.com:zookage/zookage.git
$ cd zookage
$ ./bin/up
namespace/zookage created
job.batch/package-hadoop created
job.batch/package-hive created
job.batch/package-tez created
...
All resources are ready!
```

### Log in to a client container

With `./bin/ssh`, you can ssh into `client-node`. [Various command-line tools](/docs/tools#what-commands-are-available-on-client-node) such as `hdfs`, `yarn`, `beeline`, etc. are available.

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

You can access various web UIs such as [YARN ResourceManager](http://localhost:8088/cluster){:target="_blank"} through your browser.

The list of available endpoints is [here](/docs/tools#web-ui).
