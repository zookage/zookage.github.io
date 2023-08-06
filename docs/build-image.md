---
layout: page
title: Build Image
permalink: /docs/build-image
---

# Overview

ZooKage provides Dockerfiles and helper scripts to build ZooKage compatible Docker images from local repositories on your local machine.

# Configure locations of your local repositories

It is the first step to put `./docker/build.env` and configure it. The file allows ZooKage to locate your local repositories.

You would copy `./docker/sample-build.env` into `./docker/build.env` first. It is a template of `build.env`.

```console
$ cp ./docker/sample-build.env ./docker/build.env
```

Then, you will update the file. For example, if you develop Hadoop on `/Users/okumin/ghq/github.com/okumin/hadoop`, you will update `HADOOP_SOURCE_DIR` as below.

```diff
-HADOOP_SOURCE_DIR=~/Documents/zookage/hadoop
+HADOOP_SOURCE_DIR=/Users/okumin/ghq/github.com/okumin/hadoop
```

# Build a Docker image

You can build a new image by running `./docker/build-{component name}.sh`. For example, if you want to build an image of Hadoop, you will run the following command and follow instructions.

```console
$ ./docker/build-hadoop.sh
Docker image tag [zookage-3.3.6]:
Clean working directories? [Y/n]:
Build native libraries? [Y/n]:
Build YARN UI V2? [Y/n]:
[+] Building 5.6s (6/24)
```

# Use your own image

You can seamlessly use the new Docker image in ZooKage by updating [kustomization.yaml](https://github.com/zookage/zookage/blob/main/kubernetes/kustomization.yaml){:target="_blank"}.

```diff
 - name: zookage-hadoop
   newName: zookage/zookage-hadoop
-  newTag: "3.2.2-zookage-0.2"
+  newTag: "zookage-3.3.6"
```
