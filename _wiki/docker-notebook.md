---
layout: wiki
title: Notebook
cate1: Tools
cate2: Cloud Computing
description: 本地连接远程notebook
keywords: docker
---

## 构建docker容器：
```bash
nvidia-docker run -itd -v /data/pengying/llm-finetuning:/build  -p 8222:8888 -p 10086:22 -p 6000:6000  -e JUPYTER_ENABLE_LAB=yes  --workdir /build  --name py_torch_gpu  nvcr.io/nvidia/pytorch:21.10-py3 bash 
```
## 修改配置
```bash
docker exec -it py_torch_gpu bash

jupyter notebook --generate-config # 生成配置文件

vim ~/.jupyter/jupyter_notebook_config.py 

# shift+G 到文件最后； 加入以下配置 
c.NotebookApp.ip = '*'  # 允许从任何 IP 地址访问
c.NotebookApp.port = 8888  # Jupyter 默认端口是 8888
c.NotebookApp.open_browser = False  # 不在启动时打开浏览器
c.NotebookApp.allow_remote_access = True
c.NotebookApp.allow_root = True
c.NotebookApp.token = '123456' # 设置的token密码，后面需要使用
```

## 运行notebook
```bash
jupyter-notebook --no-browser --allow-root
```

## 本地服务器登录
```bash
ssh -L 8222:127.0.0.1:8222 root@11.11.11.11 -p 22
```

## notebook 安装pkg
```bash
import sys
!{sys.executable} -m pip install numpy
```