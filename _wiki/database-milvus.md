---
layout: wiki
title: Milvus Script
cate1: Python
cate2: Milvus
description: Python Milvus 库脚本
keywords: milvus
---

```python
import pymysql
from milvus import Milvus, IndexType, MetricType, Status

# 连接Milvus服务器
class MilvusClient:
    def __init__(self, host, port):
        slef.milvus_address = f"{host}:{port}"
        self.client = Milvus(uri='tcp://{}'.format(milvus_address))

    def create_collection(self, collection_name, dimension, index_file_size, metric_type):
        status = self.client.create_collection(collection_name, dimension, index_file_size, metric_type)
        return status

    def insert_vectors(self, collection_name, records):
        status = self.client.insert(collection_name, records)

    def get_all_vectors(self, collection_name， dim): # 使用search的方式来获取所有的results
        status, vector_cnt = client.count_entities(collection_name)
        print("{} {} vector count: {}".format(collection_name, status, vector_cnt))
        status, results = self.client.search(collection_name,  query_records=[[0]*dim], top_k=vector_cnt, params={"nprobe":16, "ef":vector_cnt+100})
        return results

    def delete_entity_by_id(self, collection_name, vector_ids):
        status = self.client.delete_entity_by_id(collection_name, vector_ids)
        return status    
```
