---
layout: post
title: Docker 学习总结
categories: [知识学习]
description: docker 虚拟化学习总结
keywords: docker, 容器部署
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

# 何为Docker
Docker是一个开源的应用容器引擎，可以让开发人员打包其应用及依赖包到一个可移植的容器中，然后发布到任何可支持对应Docker引擎的系统（linux、windows等）中，可实现虚拟化，自动化部署、扩展和管理应用程序。 同时容器的使用实现了软件的隔离，微服务的发展，容器作为一种轻量级的虚拟化技术，相交传统的虚拟化技术更加轻量级和快速。

# Docker应用，容器化
可以显式的认为Docker容器就是一个操作系统，可以定制这个容器使用最大内存，磁盘容量等来合理地分配宿主机的资源。

Docker是容器化技术的一种实现。用Docker容器化应用程序时，你可以创建一个Dockerfile来定义应用程序的运行环境，包括操作系统、安装的软件和设置等。然后，你可以使用docker build命令根据Dockerfile打包应用程序和所有依赖项到一个Docker镜像中。这个镜像可以被分发并运行在任何安装了Docker的主机上，独立于主机操作系统和已安装的软件。

容器化的好处主要有：

1. 一致性：无论运行环境如何变化，容器内的应用程序都能保证一致性运行。
2. 轻量化：相比虚拟机，容器不需要包含完整的操作系统。 
3. 可移植性：可在不同的平台和云环境中部署和运行。
4. 隔离性：每个容器内的应用程序运行在自己的环境中，互不干扰。

# Docker 操作指令
## Dockerfile 编写
Dockerfile用来定义应用程序的运行环境，包括操作系统、安装的软件和设置等，是构建容器的第一步。

```Dockerfile
# 使用官方的 Python 运行时作为基础镜像
FROM python:3.8-slim-buster

# 设置工作目录为 /app
WORKDIR /app

# 将当前目录的内容复制到工作目录中
COPY . /app

# 使用 pip 安装必需的包
RUN pip install --no-cache-dir -r requirements.txt

# 使容器监听 8080 端口运行时
EXPOSE 8080

# 定义环境变量
ENV NAME World

# 在容器启动时运行 app.py
CMD ["python", "./app.py"]
```

这个Dockerfile文件是一个文本文件，你可以使用任何文本编辑器来创建和编辑。存储在主机的 "./app.py" 及其依赖列表"requirements.txt" 需要你自己准备。


复杂的应用在构建镜像的过程中可能使用多个基础镜像，编译镜像和运行镜像。一般编译镜像满足应用编译的所有依赖，在编译完成之后，将编译好的应用放入运行镜像，该镜像一般只支持一个应用运行的最少依赖。

## Docker Build
`docker build`命令用于从Dockerfile创建Docker镜像。以下是其基本语法：

```
docker build [OPTIONS] PATH | URL | -
```

它支持多种选项，以下是一些常用的：

- `-t, --tag`: 将一个名字和可选的标签应用到镜像中，常用格式为: `name:tag` 或 `name`。

- `--build-arg <varname>=<value>`: 设置构建时的环境变量。

- `--file, -f`: 指定要使用的Dockerfile。

- `--no-cache`: 构建过程中不使用缓存。在默认情况下，Docker在构建镜像时会使用缓存。这意味着如果你在构建相同的Dockerfile时，Docker会重用上一次构建的结果，这可以显著提高构建速度。然而，在某些情况下，你可能不希望使用缓存。例如，你可能在Dockerfile中有一个步骤是从远程服务器下载最新版本的应用。如果使用缓存，Docker会跳过这个步骤并使用上次构建的版本，这可能导致你的镜像中的应用版本过旧。

- `--rm`: 构建成功后删除中间容器。

- `--pull`: 尝试去更新基础镜像的新版本。

例如，你可以使用以下命令将`Dockerfile_v1`构建成一个新的镜像，并将其标记为`my-app`，版本为`v1`, , 不实用缓存执行每一步动作`--no-cache`：

```
docker build --no-cache -t my-app:v1 -f Dockerfile_v1
```

