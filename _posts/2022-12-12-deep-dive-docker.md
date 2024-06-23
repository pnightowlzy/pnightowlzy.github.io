---
layout: post
title: 深入浅出Docker
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
支持多种选项，以下是一些常用的：

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


## Docker Run
`docker run`命令用于从Docker镜像创建一个新的容器并运行一个命令。

以下是其基本语法：

```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

它支持多种选项，以下是一些常用的：

- `-d, --detach`: 以后台模式运行容器。

- `-i, --interactive`: 以交互模式运行容器。

- `-t, --tty`: 为容器分配一个伪终端。

- `--name`: 为容器指定一个名称。

- `-p, --publish`: 发布一个或多个容器的端口到主机。

- `-v, --volume`: 挂载一个或多个卷, host_dir:local_dir, 冒号":"前面的目录是宿主机目录，后面的目录是容器内目录。

- `-e, --env`: 设置环境变量。

- `--rm`: 容器退出后自动删除。

例如，你可以使用以下命令从名为`my-app`的镜像中创建一个新的容器，分配伪终端和以交互模式运行并以后台模式运行，将容器的8080端口映射到主机的80端口，同时将文件挂载到宿主机：

```
docker run -itd -p 80:8080 -v /data/container/my-app:/app --name myRunningApp my-app
```

这将启动一个新的容器，容器名称为`myRunningApp`，并运行`my-app`镜像。然后，它将容器的8080端口映射到主机的80端口，使你能够通过访问主机的80端口来访问容器内的应用。

## Docker Attach
`docker attach`命令的基本语法形式如下：

```
docker attach [OPTIONS] CONTAINER
```

`docker attach`命令允许您附加到一个运行的Docker容器，您可以查看或参与容器的交互。

`docker attach`命令支持以下选项：

- `--detach-keys`: 为附加会话设置覆盖分离键序列。

- `--no-stdin`: 不附加 STDIN；

- `-sig-proxy`: 代理接收到的系统信号到进程（默认为true）

以下是一个使用示例，在此示例中，我们创建一个在控制台上输出 "hello world" 的容器，并附加到它：

```
# 创建并启动容器
docker run -d --name my_container ubuntu /bin/bash -c "while true; do echo hello world; sleep 1; done"

# 附加到容器
docker attach my_container
```

然后，我们应该可以在控制台上看到 "hello world" 的输出。我们可以使用 `Ctrl-p` `Ctrl-q` 来从容器中分离，而不会停止容器。如果我们再次连接到容器，我们将看到它仍在输出 "hello world"。


## 其他指令
Docker 还有许多其他指令，例如:
`docker ps` 查看容器
`docker image` 查看镜像
`docker inspect` 查看容器信息
...
这些指令集中在这篇文章中写道。

# docker-compose
`docker`命令启动似乎看起来虽然简单，但是当我们的一些参数设定比较多时，命令的长度就会变得很长，且不利于管理和理解，所以更为常见的方式是使用`docker-compose.yaml`.

`docker-compose.yml`是一个用于定义和运行多个Docker容器的服务配置文件。通过使用`docker-compose`命令，用户可以启动、停止和重建服务，甚至查看运行中服务的状态和日志。

以下是`docker-compose.yml`的基本结构：

```yaml
version: '3'
services:
  service1:
    image: image1
    ports:
      - "5000:5000"
    volumes:
      - /data
  service2:
    image: image2
    environment:
      - VAR=value
```
在上述配置中：
- `version`：定义了你正在使用的Docker Compose文件格式版本。
- `services`：定义了应用程序的各个服务，比如数据库、web服务器等。
- `serviceX`：你为应用程序所定义的服务名。
- `image`：定义了用来运行服务容器的Docker镜像。
- `ports`：定义了端口映射。格式为 `<host_port>:<container_port>`。
- `volumes`: 定义了挂载的数据卷。可以是匿名数据卷，或者是挂载主机上的特定目录或文件。
- `environment`: 定义了环境变量。

你可以通过`docker-compose up`命令使用该文件启动容器。在同一目录下运行这个命令会加载该配置文件，并建立并运行定义的服务。

## Docker Service
Docker service 是 Docker Swarm 集群模式下的一个概念。Service 是定义应用程序服务（例如 web 应用、数据库等）的方式，特点是可以进行规模化操作和进行更新。

使用 `docker service create` 命令 可以创建一个新的服务。例如：

```bash
docker service create --replicas 1 --name helloworld alpine ping docker.com
```

这个命令会在 Swarm 集群中创建一个名为 "helloworld" 的服务，它由一个运行 "alpine" 镜像并执行 "ping docker.com" 命令的容器组成。

Docker service 支持的一些主要命令包括：
- `docker service create`：创建一个新的服务
- `docker service inspect`：查看服务的详细信息
- `docker service ls`：列出所有服务
- `docker service rm`：删除一个或多个服务
- `docker service scale`：更改服务的副本数量
- `docker service ps`：列出服务中的任务（注意：这里的任务可以理解为特定服务的运行实例，即容器）
- `docker service update`：更新服务的配置

Docker service 主要用于应用的部署和扩展，并且它提供了强大的服务发现和负载均衡功能。通过 service，Docker 可以自动化完成服务的健康检查、失败恢复、扩容和缩容等操作，极大地方便了分布式应用的运维。

## 总结
Docker是一个强大的虚拟化技术，因其便捷性得到广泛的应用，想要深入了解docker的话，还需要花费需要多时间，但是非工程化人员主要掌握基本的命令即可，对于docker内部逻辑不需要太过深入。
