---
layout: wiki
title: yum 镜像源
cate1: Linux
cate2: yum
categories: yum
description: yum 镜像源更换
keywords: yum, docker
---

```Dockerfile
FROM registry01.wezhuiyi.com/library/centos:7.4-gosu

COPY CentOS-Base.repo  /etc/yum.repos.d/CentOS-Base.repo

RUN yum makecache

RUN yum -y install  wget gettext
```

makecache的过程中会找镜像源，会出现connect timeout的err，直到找到合适的镜像源。