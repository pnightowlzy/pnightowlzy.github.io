---
layout: post
title: minio 部署以及权限管理
categories: [minio, docker, 微服务]
description: 协同设计应用开发
keywords: minio部署及sts权限管理
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 环境配置
1. 镜像版本
minio/minio:RELEASE.2024-07-31T05-46-26Z
2. 启动命令
docker compose 启动：
```
version: '2.0'
services:
  minio:
    image: ${MINIO_IMAGE}
    container_name: minio
    ports:
      - "${MINIO_PORT}:9000"
      - 9001:9001
    restart: always
    command: server /data --console-address ":9001"
    environment:
      MINIO_ACCESS_KEY: ${MINIO_ACCESS_KEY_ID}
      MINIO_SECRET_KEY: ${MINIO_SECRET_ACCESS_KEY} #大于等于8位
    logging:
      options:
        max-size: "1024M" # 最大文件上传限制
        max-file: "1000"
      driver: json-file
    volumes:
      - ${DEPLOY_ROOT}/volumes/minio/data:/data
    networks:
      - default_bridge

networks:
  default_bridge:
    external: true
```
docker run 启动：
```bash
docker run -d \
  -p 9000:9000 \
  -p 9001:9001 \
  --name minio1 \
  -v /home/minio/data:/data \
  -e "MINIO_ROOT_USER=AKIAIOSFODNN7EXAMPLE" \
  -e "MINIO_ROOT_PASSWORD=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
  minio/minio server /data --console-address ":9001"
```