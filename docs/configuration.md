---
layout: page
title: Configuration
permalink: /docs/configuration
---

## Choose components

ZooKage creates a sandbox cluster with HDFS, Hive, Tez, and YARN by default. You can add any components you want, and you can remove any ones you don't need by editing `resources` in [kustomization.yaml](https://github.com/zookage/zookage/blob/main/kubernetes/kustomization.yaml){:target="_blank"}.

For example, if you want to additionally test Spark SQL on Ozone, you will update `resources` as below.

```diff
 resources:
 - base/client
 - base/common
 # - base/hbase
 - base/hdfs
 - base/hive
 # - base/mapreduce
-# - base/ozone
-# - base/spark
+- base/ozone
+- base/spark
 - base/tez
 # - base/trino
 - base/yarn
 # - base/zookeeper
```

## Choose versions

The Docker images used in ZooKage is interchangeable. You can replace tag names in [kustomization.yaml](https://github.com/zookage/zookage/blob/main/kubernetes/kustomization.yaml){:target="_blank"}.

```diff
 - name: zookage-spark
   newName: zookage/zookage-spark
-  newTag: "3.5.1-zookage-0.2"
+  newTag: "3.4.1-zookage-0.2"
```

### Supported versions

ZooKage provides various pre-build images.

- [Hadoop](https://hub.docker.com/r/zookage/zookage-hadoop/tags){:target="_blank"}
- [HBase](https://hub.docker.com/r/zookage/zookage-hbase/tags){:target="_blank"}
- [Hive](https://hub.docker.com/r/zookage/zookage-hive/tags){:target="_blank"}
- [Ozone](https://hub.docker.com/r/zookage/zookage-ozone/tags){:target="_blank"}
- [Spark](https://hub.docker.com/r/zookage/zookage-spark/tags){:target="_blank"}
- [Tez](https://hub.docker.com/r/zookage/zookage-tez/tags){:target="_blank"}
- [Trino](https://hub.docker.com/r/zookage/zookage-trino/tags){:target="_blank"}
- [ZooKeeper](https://hub.docker.com/r/zookage/zookage-zookeeper/tags){:target="_blank"}

The following list contains some of valid combinations. Image tags have `-zookage-0.2` as a postfix like `3.3.1-zookage-0.2`.

| Hadoop | HBase| Hive | Ozone | Spark | Tez | Trino | ZooKeeper |
|-|-|-|-|-|-|-|-|
| 2.10.1 | 2.4.1 | 2.3.8 | 1.3.0 | 3.0.2, 3.1.1 | 0.9.2 | N/A | 3.6.2 |
| 3.2.2 | 2.4.1 | 3.1.2-guava-27.0-jre | 1.3.0 | 3.4.1 | 0.9.2-guava-27.0-jre-jersey-1.19-servlet-api-3.1.0-without-jetty | 413 | 3.6.2 |
| 3.3.6 | 2.5.3-hadoop-3.3.1 | 4.0.0 | 1.3.0 | 3.5.1 | 0.10.3 | 413 | 3.6.2 |

## Change configurations

You will find configuration files with which you are well familiar in [./config](https://github.com/zookage/zookage/tree/main/kubernetes/base/common/config){:target="_blank"}. For example, you can tune HDFS parameters via [./config/hadoop/hdfs-site.xml](https://github.com/zookage/zookage/blob/main/kubernetes/base/common/config/hadoop/hdfs-site.xml){:target="_blank"}.

## Set up HA

You can set up HDFS and YARN with High Availability enabled. Note that this feature is NOT designed to deploy a production-ready HA cluster. This is for a developer who wants to test HA features on their local machines.

First, you have to edit `patchesStrategicMerge` in [kustomization.yaml](https://github.com/zookage/zookage/blob/main/kubernetes/kustomization.yaml){:target="_blank"}.

```diff
 patchesStrategicMerge:
 ### High Availability ###
-# - patches/ha/hdfs.yaml
-# - patches/ha/yarn.yaml
+- patches/ha/hdfs.yaml
+- patches/ha/yarn.yaml
```

Then, you will modify `hdfs-site.xml` and `yarn-site.xml`. You should remove or comment out non-HA settings and enable HA settings.

```diff
-  <!-- ### For non-HA ### -->
+  <!-- ### For non-HA
   <property>
     <name>dfs.ha.namenodes.zookage</name>
     <value>namenode-0</value>
   </property>
-  <!-- ### For non-HA ### -->
+  For non-HA ### -->
 
-  <!-- ### For HA ###
+  <!-- ### For HA ### -->
   <property>
     <name>dfs.ha.namenodes.zookage</name>
     <value>namenode-0,namenode-1</value>
@@ -52,7 +52,7 @@
     <name>dfs.namenode.http-address.zookage.namenode-1</name>
     <value>hdfs-namenode-1.hdfs-namenode:9870</value>
   </property>
-  ### For HA ### -->
+  <!-- ### For HA ### -->
```
