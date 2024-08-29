---
layout: wiki
title: Commands 
cate1: Programming
cate2: Commands
description: 最常用指令
keywords: linux, programming, commands
---
## Python
### 查看是否有cuda
```python
import torch
print(torch.cuda.is_available()
```


### pip 源 + 代理
```
pip install package -i https://pypi.tuna.tsinghua.edu.cn/simple --proxy=http://127.0.0.1:1080
```

## Linux
### 替换
```bash
sed -i 's/old_string/new_string/g' file.txt
```

### 查看系统使用内存
```
top -m
```

### 跨服务器拷贝文件
```bash
scp -r username@remote_ip:/path/to/file /local/path
```

### 后台指令运行
```
nohup command > log.txt 2>&1 &
```

### 查看gpu使用情况
```
nvidia-smi
```
### 修改代理
```
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080
```
## docker
### docker 拉去指定镜像源
```
docker pull dockerproxy.cn/ubuntu:latest
```
