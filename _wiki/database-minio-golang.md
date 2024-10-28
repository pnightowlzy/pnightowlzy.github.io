---
layout: wiki
title: Minio 
cate1: Go, Python
cate2: Database
description: Golang 连接minio
keywords: minio, GO
---

## 环境配置
安装MinIO的Go客户端库。可以使用go get命令来安装：
```bash
go get github.com/minio/minio-go/v7
```

## 备份minio，从 `Cluster A` 到 `Cluster B`
```python
from minio import Minio
import os
from collections import defaultdict
# 初始化 MinIO 客户端
client = Minio(
    "cluterA:portA",  # 替换为你的 MinIO 服务器地址
    access_key="api_id",  # 替换为你的 Access Key
    secret_key="api_key",  # 替换为你的 Secret Key
    secure=False  # 如果使用 HTTPS，请设置为 True
)

new_client = Minio(
    "clusterB:portB",  # 替换为你的 MinIO 服务器地址
    access_key="api_id",  # 替换为你的 Access Key
    secret_key="api_key",  # 替换为你的 Secret Key
    secure=False  # 如果使用 HTTPS，请设置为 True
)

# 列出所有的 buckets
buckets = client.list_buckets()

# 存下所有的路径
already_in = []
import time

with open("exists_objects.txt", "a+") as f:    
    f.seek(0)
    already_in = f.readlines()
    print("already in ",already_in)
    already_in = [file.strip() for file in already_in]

    for bucket in buckets:
        # 过滤bucket, filtered_*
        if bucket.name.startswith("filtered") or bucket.name in ["mp4", "pmu"]:  
            continue
        print(f"Bucket: {bucket.name}")

        success = False
        while not success:
            try:
                if not new_client.bucket_exists(bucket.name):
                    new_client.make_bucket(bucket.name)
                    print("create new bucket in new cluster")
                success = True
            except:
                time.sleep(5)
                print("connection err stop...")

    
        
        # 列出每个 bucket 中的所有对象
        objects = client.list_objects(bucket.name, recursive=True)
        
        for obj in objects:
            local_file_path = os.path.join("downloaded_files", bucket.name, obj.object_name)
            print("file already in : ", local_file_path in already_in)
            if local_file_path in already_in:
                print(f"object: {obj.object_name} already in, skip")
                continue
            
            if os.path.exists(local_file_path):
                print(f"{local_file_path} exist, skip!")
            else:
                print(f"Downloading: {obj.object_name}")
            
                # 创建本地目录结构                
                os.makedirs(os.path.dirname(local_file_path), exist_ok=True)
    
                # 下载对象
                try:
                    client.fget_object(bucket.name, obj.object_name, local_file_path)
                    print(f"Downloaded: {local_file_path}")
                except e:
                    print(f"Error downloading {obj.object_name}: {e}")
            success = False

            while  not success:
                try:
                    new_client.fput_object(bucket.name, obj.object_name, local_file_path, part_size=4 * 1024 * 1024 * 1024)
                    success = True
                except:                
                    time.sleep(5)    
                    print("connection err, stop")
                    
            print(f"uploaded to new cluster: {obj.object_name}")
            print("-"*50)
            f.write(f"{local_file_path}\n")
            os.remove(local_file_path)
            

```

