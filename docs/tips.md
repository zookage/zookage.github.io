---
layout: page
title: Tips
permalink: /docs/tips
---

## How to use helper commands

* `./bin/up`
    * Launch components
* `./bin/down`
    * Delete resources created by ZooKage
* `./bin/ssh [pod [container]]`
    * Log in to the given pod or container
    * Without any arguments, you will ssh into client-node
* `./bin/logs [pod]`
    * View logs of the given pod
    * Without any arguments, you can fetch all logs of all containers
* `./bin/kubectl`
    * Alias of `kubectl --namespace zookage`

## Web UI

You can access various web UIs through a browser of your host machine. This feature is not stable and you may sometimes have to reboot Docker Desktop when the following ports are inaccessible.

* [Trino Web UI](http://localhost:8080/ui/){:target="_blank"}
* [YARN ResourceManager](http://localhost:8088/cluster){:target="_blank"}
* [YARN-UI V2](http://localhost:8088/ui2/){:target="_blank"}
* [YARN Timeline Server](http://localhost:8188/applicationhistory){:target="_blank"}
* [HDFS](http://localhost:9870/){:target="_blank"}
* [Ozone Manager](http://localhost:9874/#!/){:target="_blank"}
* [Ozone SCM](http://localhost:9876/#!/){:target="_blank"}
* [Ozone Recon](http://localhost:9888/#/Overview){:target="_blank"}
* [Tez UI](http://localhost:9999/tez-ui/){:target="_blank"}
* [HiveServer2](http://localhost:10002/){:target="_blank"}
* [HBase Master](http://localhost:16010/master-status){:target="_blank"}
* [Spark History Server](http://localhost:18080/){:target="_blank"}
* [MapReduce History Server](http://localhost:19888/jobhistory){:target="_blank"}

## Check current statuses of containers

You can list statuses of containers with `./bin/kubectl get pods`.

```console
$ ./bin/kubectl get pods
NAME                                READY   STATUS      RESTARTS   AGE
hdfs-datanode-0                     1/1     Running     0          7m19s
hdfs-datanode-1                     1/1     Running     0          7m19s
hdfs-datanode-2                     1/1     Running     0          7m19s
...
```

## Attatch a remote debugger to a container

You can attatch a debugger from your host machine to a container using `./bin/kubectl port-forward {pod name} {local port}:{container port}`.

For example, you can access port 8000 on `client-node-0` via port 5005 on your host machine.

```console
$ ./bin/kubectl port-forward client-node-0 5005:8000
Forwarding from 127.0.0.1:5005 -> 8000
Forwarding from [::1]:5005 -> 8000
```
