---
layout: page
title: Guide
permalink: /docs/guide
---

Use this guide after the [Quick Start](/docs/quick-start) to run, access, inspect, and configure your ZooKage cluster. The file paths and examples below refer to ZooKage v0.6.0, the version used in the Quick Start.

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

Service configuration files are in [kubernetes/base/common/config](https://github.com/zookage/zookage/tree/v0.6.0/kubernetes/base/common/config){:target="_blank"}. Hadoop settings are split by component:

- Shared settings: `hadoop/core-site.xml`
- HDFS: `hdfs/hdfs-site.xml`
- YARN: `yarn/yarn-site.xml`
- MapReduce: `mapreduce/mapred-site.xml`

Some files contain Go templates whose values come from the `config-values` ConfigMap in [config-values.yaml](https://github.com/zookage/zookage/blob/v0.6.0/kubernetes/base/common/config/config-values.yaml){:target="_blank"}. Change a fixed setting in its configuration file, or set a template's parameter in the values ConfigMap. Values must be strings; quote boolean and numeric values. The `hdfs-config-values` and `mapreduce-config-values` ConfigMaps provide component-specific overrides of the shared values.

The patches in `kubernetes/patches` set these values for features such as HA, Kerberos, and LLAP. Keep `render-config.yaml` in the `transformers` list in `kubernetes/kustomization.yaml`, and use `./bin/up` to evaluate templates before applying the manifests.

Secrets are defined under [kubernetes/base/common/secret](https://github.com/zookage/zookage/tree/v0.6.0/kubernetes/base/common/secret){:target="_blank"} and mounted under `/run/secrets/zookage` in containers. When changing a JCEKS credential store, edit its adjacent `.properties` file and regenerate the `.jceks` file with `./test/jceks create <jceks-file>` before deploying.

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

The [HDFS](https://github.com/zookage/zookage/blob/v0.6.0/kubernetes/patches/ha/hdfs.yaml){:target="_blank"} and [YARN](https://github.com/zookage/zookage/blob/v0.6.0/kubernetes/patches/ha/yarn.yaml){:target="_blank"} patches configure replicas and template values for HA. The [HA profile](https://github.com/zookage/zookage/blob/v0.6.0/test/kubernetes/ha.yaml){:target="_blank"} contains a complete example.

### Use Kerberos and Ranger

The [auth profile](https://github.com/zookage/zookage/blob/v0.6.0/test/kubernetes/auth.yaml){:target="_blank"} enables Kerberos authentication and Ranger authorization across the included services. It includes the KDC, LDAP, service dependencies, and authentication and authorization patches. Use the [llap-auth profile](https://github.com/zookage/zookage/blob/v0.6.0/test/kubernetes/llap-auth.yaml){:target="_blank"} to combine authentication with LLAP.

To try the auth profile, copy it over `kubernetes/kustomization.yaml` and restart the sandbox. Save any custom configuration first; `./bin/down` deletes the current namespace and its resources.

```console
$ cp test/kubernetes/auth.yaml kubernetes/kustomization.yaml
$ ./bin/down
$ ./bin/up
$ ./bin/ssh
zookage@client-node-0:~$ kinit -kt /run/secrets/zookage/client/client.keytab zookage@ZOOKAGE.LOCAL
zookage@client-node-0:~$ klist
```

Acquire a ticket before running clients such as `hdfs`, `beeline`, or `trino`, and run `kinit` again if the ticket expires. The included credentials and certificates are for the local sandbox.

#### Web access with authentication

Authentication changes some of the [Web UI](#web-ui) endpoints: HDFS uses HTTPS on port 9871 and Trino uses HTTPS on port 8443. Kerberos-protected web endpoints require a ticket and a hostname matching the service principal. A ticket acquired inside `client-node-0` is available to clients there; it does not authenticate your host browser.

For example, after the `kinit` command above, access HDFS from the client container:

```console
zookage@client-node-0:~$ curl --cacert /etc/zookage/shared/zookage-ca.crt --negotiate -u : https://hdfs-namenode-0.hdfs-namenode.zookage.svc.cluster.local:9871/dfshealth.html
```

Spark History Server uses a bearer token in the auth profile. See the [web integration check](https://github.com/zookage/zookage/blob/v0.6.0/test/integration/web.sh){:target="_blank"} for the authentication method and endpoint of each service.
