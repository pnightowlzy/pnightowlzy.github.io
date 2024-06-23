---
layout: wiki
title: Docker
cate1: Tools
cate2: Cloud Computing
description: 常见Docker指令
keywords: docker
---

## Dockerfile
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

## docker build
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
## docker run
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

## docker image
```bash
docker images # 查看所有镜像
docker rmi image_name # 删除镜像名
docker rmi -f image_name # 删除镜像名
```

## docker networks
Docker network 是用来管理 Docker 容器的网络设置的。以下是一些主要的 Docker network 命令和他们的参数：

1. `docker network create`：创建一个新的网络。主要参数有：
   - `--driver` 或 `-d`：指定网络的类型。常用的有 bridge (默认), overlay, macvlan 等。
   - `--subnet`：定义网络的子网。
   - `--ip-range`：定义网络的IP地址范围。
   - `--gateway`：定义网络的网关。
   - `--label`：为网络定义标签。

2. `docker network connect`：把一个运行中的容器连接到一个网络。参数主要有：
   - `<network>`：网络的名字或者 ID。
   - `<container>`：需要连接的容器的名字或者 ID。

3. `docker network disconnect`：把一个运行中的容器从一个网络中断开。参数和 `docker network connect` 相同。

4. `docker network ls`：列出所有的网络。

5. `docker network inspect`：查看一个或者多个网络的详细信息。

6. `docker network rm`：删除一个或者多个网络。

以上命令可以帮助你管理 Docker 的网络，包括创建新网络，把容器连接或断开网络等操作。你可以根据实际需要使用不同的参数来配置网络。

## docker inspect
`docker inspect container` 查看容器基本信息

## docker logs 
```bash
docker logs -f --tail 200 container_name/id # 查看容器日志
```

## 打包镜像
```bash
docker save -o name.tar image_name:tag
```

## 加载镜像
```bash
docker load -i name.tar
```

## 制作镜像
`docker commit` 命令是用来创建新的镜像的。这个命令会从一个已经修改过的容器中创建一个新的 Docker 镜像。

基本语法如下：

```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

其中：
- `CONTAINER` 是你想要从中创建镜像的容器的 ID 或名字。
- `REPOSITORY[:TAG]` 是新镜像的名字和标签。如果你不提供标签，那么默认标签是 `latest`。

例如，如果你有一个名字为 `mycontainer` 的容器，并且你想从它创建一个名字为 `mynewimage` 的新镜像，你可以输入：

```
docker commit mycontainer mynewimage
```

你也可以加上版本标签，例如：

```
docker commit mycontainer mynewimage:1.0
```

可以使用的选项包括：
- `-a`, `--author`：设置提交的作者信息
- `-m`, `--message`：设置提交信息
- `-p`, `--pause`: 提交时暂停容器运行

这个命令会创建一个新的 Docker 镜像，包含了运行 `mycontainer` 的所有的文件系统的改变。