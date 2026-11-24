---
layout: page
title: Build Image
permalink: /docs/build-image
---

# Overview

ZooKage provides Dockerfiles and helper scripts to build ZooKage compatible Docker images from local repositories on your local machine.

These instructions refer to ZooKage v0.6.0. Run the commands from the ZooKage repository root.

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

`DOCKER_IMAGE_NAME_PREFIX` controls the image repository prefix and defaults to `zookage`. Shared base-image tags and digests are maintained in [docker/images.env](https://github.com/zookage/zookage/blob/v0.6.0/docker/images.env){:target="_blank"}. The build scripts load that file after `build.env`, so base-image settings in an older local `build.env` do not override the tracked pins.

# Build a Docker image

You can build a new image by running `./docker/build-{component name}.sh`. For example, if you want to build an image of Hadoop, you will run the following command and follow instructions.

The suggested image tag comes from the source repository's branch name, or its commit ID when no branch is checked out. In this example, the Hadoop branch is `my-hadoop-changes` and the chosen image tag is `local-hadoop`:

```console
$ ./docker/build-hadoop.sh
Docker image tag [my-hadoop-changes]: local-hadoop
Clean working directories? [Y/n]:
Build native libraries? [Y/n]:
Build YARN UI V2? [Y/n]:
...
```

# Use your own image

With the default image prefix, the example above builds `zookage/zookage-hadoop:local-hadoop`. Select it in [kubernetes/kustomization.yaml](https://github.com/zookage/zookage/blob/v0.6.0/kubernetes/kustomization.yaml){:target="_blank"}. If you changed `DOCKER_IMAGE_NAME_PREFIX`, update `newName` as well.

```diff
 - name: zookage-hadoop
   newName: zookage/zookage-hadoop
-  newTag: 3.4.3-zookage-0.6
+  newTag: local-hadoop
```
