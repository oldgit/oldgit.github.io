---
layout: post
title: A small Kubernetes cluster on mac minis
date: 2015-09-03 18:51:39
category: ops
tags: [ops, kubernetes, coreos, docker]
---
[Docker](https://www.docker.com) containers are an efficient way to run discrete software services.
[CoreOS](https://www.coreos.com) is a Container Linux OS to run docker containers.
[Kubernetes](https://www.kubernetes.io) can deploy and manage services on CoreOS nodes.
This post describes setting up a small CoreOS cluster of 2 mac minis with kubernetes and docker.

## Install CoreOS on a bare metal PC

Firstly, burn the latest CoreOS iso to a CDROM. Next boot the mac mini using the CDROM.

Use the cloud-config

Add kube-serviceaccount key to home directory

## DNS setup: Add kube-dns replication controller & service 

Do dns setup.

## Run example

Run example.

## Node CPU and memory usage

Kubernetes uses lots of memory and CPU polling the cluster.

## Conclusions

### September 2015 - Not ready for release - still in beta.

### 2020 Update from coreos.com

"On May 26, 2020, CoreOS Container Linux will reach its end of life and will
no longer receive updates. We strongly recommend that users begin migrating
their workloads to another operating system as soon as possible."
