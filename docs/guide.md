---
layout: page
title: Guide
permalink: /docs/guide
---

Use this guide after the [Quick Start](/docs/quick-start) to run, access, inspect, and configure your ZooKage cluster. Unless stated otherwise, the file paths and examples below refer to ZooKage v0.6.0, the version used in the Quick Start.

- [Start and stop](#start-and-stop)
- [Connect and use](#connect-and-use)
- [Check the cluster](#check-the-cluster)
- [Configure the cluster](#configure-the-cluster)

## Start and stop

- `./bin/up` renders configuration templates in a local Docker container, then launches the cluster components.
- `./bin/down` deletes the resources created by ZooKage.

## Connect and use

Use `./bin/ssh [pod [container]]` to open a shell in a pod or container. Without arguments, it connects to `client-node-0`.

The following commands are available on client-node:

- `aws`
- `beeline`
- `gohdfs` ([colinmarc/hdfs](https://github.com/colinmarc/hdfs))
- `hbase`
- `hdfs`
- `kinit` and `klist` (for Kerberos authentication)
- `ozone`
- `spark-shell`
- `spark-sql`
- `trino`
- `yarn`

On the development version, additional clients must be [enabled separately](#choose-client-tools-development-version).

If Kerberos authentication is enabled, acquire a ticket in the client container before running client commands:

```console
zookage@client-node-0:~$ kinit -kt /run/secrets/zookage/client/client.keytab zookage@ZOOKAGE.LOCAL
```

## Check the cluster

- `./bin/logs [pod]` prints logs from the given pod, or from all pods when no pod is specified.
- `./bin/kubectl <args...>` runs `kubectl` in the `zookage` namespace.

For example, list the current pod statuses with `./bin/kubectl get pods`:

```console
$ ./bin/kubectl get pods
NAME                                READY   STATUS      RESTARTS   AGE
hdfs-datanode-0                     1/1     Running     0          7m19s
hdfs-datanode-1                     1/1     Running     0          7m19s
hdfs-datanode-2                     1/1     Running     0          7m19s
...
```

### Web UI

The following URLs apply when the corresponding components are enabled without the authentication patches. You can access them through a browser on your host machine. This feature is not stable and you may sometimes have to reboot Docker Desktop when the following ports are inaccessible.

- [ZooKage Console](http://localhost:3000/){:target="_blank"}
- [Keycloak Web UI (username=admin, password=admin)](http://localhost:8080/admin/master/console/)
- [Ranger Web UI (username=admin, password=admin)](http://localhost:6080/login.jsp)
- [Trino Web UI](http://localhost:8090/ui/){:target="_blank"}
- [YARN ResourceManager](http://localhost:8088/cluster){:target="_blank"}
- [YARN-UI V2](http://localhost:8088/ui2/){:target="_blank"}
- [YARN Timeline Server](http://localhost:8188/applicationhistory){:target="_blank"}
- [HDFS](http://localhost:9870/){:target="_blank"}
- [Ozone Manager](http://localhost:9874/#!/){:target="_blank"}
- [Ozone SCM](http://localhost:9876/#!/){:target="_blank"}
- [Ozone Recon](http://localhost:9888/#/Overview){:target="_blank"}
- [Tez UI](http://localhost:9999/tez-ui/){:target="_blank"}
- [HiveServer2](http://localhost:10002/){:target="_blank"}
- [HBase Master](http://localhost:16010/master-status){:target="_blank"}
- [Spark History Server](http://localhost:18080/){:target="_blank"}
- [MapReduce History Server](http://localhost:19888/jobhistory){:target="_blank"}

With authentication enabled, HDFS uses HTTPS on port 9871 and Trino uses HTTPS on port 8443. Kerberos-protected web UIs require a ticket and a hostname matching the service principal.

## Configure the cluster

Edit [kustomization.yaml](https://github.com/zookage/zookage/blob/v0.6.0/kubernetes/kustomization.yaml){:target="_blank"} to choose components and image versions, or change configuration files to tune individual services.

### Choose components

ZooKage includes HDFS, Hive, Tez, and YARN in its default sandbox cluster. Add or remove components by editing `resources` in `kustomization.yaml`. For example, to test Spark SQL on Ozone, enable both components:

```diff
 resources:
-# - base/ozone
-# - base/spark
+- base/ozone
+- base/spark
```

### Choose client tools (development version)

Client selection applies to the unreleased `main` branch. ZooKage v0.6.0 includes all clients.

Hadoop and the utility commands are always available on `client-node`; the default configuration also enables the Hive client (`beeline`). Select additional clients through `patches/client/<component>.yaml` entries in the `patches` section of [kustomization.yaml on main](https://github.com/zookage/zookage/blob/main/kubernetes/kustomization.yaml){:target="_blank"}. Client selection is independent of the server `resources`, so enable the clients you intend to use as well as the corresponding services.

For example, enable the Trino CLI by uncommenting its client patch:

```diff
 patches:
-# - path: patches/client/trino.yaml
+- path: patches/client/trino.yaml
```

Client patches are available for `hbase`, `hive`, `ozone`, `spark`, `tez`, `trino`, and `zookeeper`. Each adds the component image and its required configuration mounts. Spark also mounts the Hive configuration for metastore access. Apply the updated configuration with `./bin/up`; selecting only the clients you use reduces the images that must be downloaded before `client-node` starts.

Enable `patches/client/tez.yaml` to submit Tez jobs directly from `client-node`. Beeline sends queries to HiveServer2, which runs Tez on the server side, so Hive queries do not need the Tez client patch. Keep the server-side `base/tez` resource enabled for Hive on Tez.

### Choose versions

The Docker images used in ZooKage are interchangeable. Replace a `newTag` in `kustomization.yaml` to use a different version. For example:

```diff
 - name: zookage-spark
   newName: zookage/zookage-spark
-  newTag: 4.1.4-zookage-0.6
+  newTag: 4.0.1-zookage-0.4
```

#### Supported versions

ZooKage provides pre-built images for these components:

- [Hadoop](https://hub.docker.com/r/zookage/zookage-hadoop/tags){:target="_blank"}
- [HBase](https://hub.docker.com/r/zookage/zookage-hbase/tags){:target="_blank"}
- [Hive](https://hub.docker.com/r/zookage/zookage-hive/tags){:target="_blank"}
- [Ozone](https://hub.docker.com/r/zookage/zookage-ozone/tags){:target="_blank"}
- [Ranger](https://hub.docker.com/r/zookage/zookage-ranger/tags){:target="_blank"}
- [Spark](https://hub.docker.com/r/zookage/zookage-spark/tags){:target="_blank"}
- [Tez](https://hub.docker.com/r/zookage/zookage-tez/tags){:target="_blank"}
- [Trino](https://hub.docker.com/r/zookage/zookage-trino/tags){:target="_blank"}
- [ZooKeeper](https://hub.docker.com/r/zookage/zookage-zookeeper/tags){:target="_blank"}

The following list contains one valid combination. These image tags use the `-zookage-0.6` suffix, such as `3.4.3-zookage-0.6`.

| Hadoop | HBase | Hive | Ozone | Ranger | Spark | Tez | Trino | ZooKeeper |
| - | - | - | - | - | - | - | - | - |
| 3.4.3 | 2.6.7 | 4.2.1 | 2.2.2 | 2.9.0 | 4.1.4 | 0.10.5 | 484 | 3.9.7 |

You can also [build your own image](/docs/build-image) to test local changes.

### Change configurations

Service configuration files are in [kubernetes/base/common/config](https://github.com/zookage/zookage/tree/v0.6.0/kubernetes/base/common/config){:target="_blank"}. For example, tune HDFS parameters in [hdfs-site.xml](https://github.com/zookage/zookage/blob/v0.6.0/kubernetes/base/common/config/hdfs/hdfs-site.xml){:target="_blank"}.

Template values can be configured in [config-values.yaml](https://github.com/zookage/zookage/blob/v0.6.0/kubernetes/base/common/config/config-values.yaml){:target="_blank"}; `./bin/up` renders them automatically.

### Set up HA

You can enable High Availability for HDFS and YARN to test HA features locally. This is not designed to deploy a production-ready HA cluster.

Enable ZooKeeper in `resources` and the HA patches in `patches` in `kustomization.yaml`:

```diff
 resources:
-# - base/zookeeper
+- base/zookeeper
 patches:
-# - path: patches/ha/hdfs.yaml
-# - path: patches/ha/yarn.yaml
+- path: patches/ha/hdfs.yaml
+- path: patches/ha/yarn.yaml
```

The [HDFS](https://github.com/zookage/zookage/blob/v0.6.0/kubernetes/patches/ha/hdfs.yaml){:target="_blank"} and [YARN](https://github.com/zookage/zookage/blob/v0.6.0/kubernetes/patches/ha/yarn.yaml){:target="_blank"} patches configure replicas and template values for HA.
