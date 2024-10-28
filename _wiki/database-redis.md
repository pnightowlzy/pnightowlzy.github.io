---
layout: wiki
title: Redis
cate1: Python
cate2: Database
description: Redis集群迁移
keywords: minio, GO
---


```python
from rediscluster import StrictRedisCluster, RedisCluster
import json

file_path = "redis.json"

# 连接集群
startup_nodes_A = [{
   "host":f"cluster_host_{i}", "port":"6379", "password":"**********"} for i in range(6)]

cluster_A = RedisCluster(startup_nodes=startup_nodes_A, decode_responses=False, password="**********")
print("Connect to cluster A successfully!")


data_keys_A = cluster_A.keys()
redis_data = {}
# print(len(data_keys_A))

import time
for i in range(len(data_keys_A)):
    key = data_keys_A[i].decode()
    print(type(key))
    # 这里主要考虑去掉 face 和 etcd的key，不需要处理
    if "face" in key or "etcd" in key: 
        continue
    key_type = cluster_A.type(key).decode('utf-8','replace')  # 判断key的类型
    value = None
    print(i, key, key_type)
    if key_type == 'string':
        value = cluster_A.get(key).decode('utf-8','replace')
        # cluster_B.set(key, value)
    elif key_type == 'list':
        value = cluster_A.lrange(key, 0, -1)
    #    print(type(value[0]))
        value = [v.decode('utf-8', 'replace') for v in value]
        
        # cluster_B.delete(key)  # 清空B中已有的list
        # for item in value:
        #    cluster_B.rpush(key, item)
    elif key_type == 'set':
        value = cluster_A.smembers(key)
        value = [v.decode('utf-8', 'replace') for v in value]
        print(key, value)
        # cluster_B.delete(key)  # 清空B中已有的set
        # for item in value:
            # cluster_B.sadd(key, item)
    elif key_type == 'hash':
        value = cluster_A.hgetall(key)
        new_value = {}
        for k,v in value.items():
            new_value[k.decode()] = v.decode()
        value = new_value
        # cluster_B.delete(key)  # 清空B中已有的hash
        # for item, item_value in value.items():
            # cluster_B.hset(key, item, item_value)
    else:
        value = None
        print(f"Unknown type for key {key}")
        continue
    #print(i, key, value)
    redis_data[key] = value
    #break
print("dayin")
#print(redis_data)
#print(json.dumps(redis_data))
f = open(file_path, "w") 
json.dump(redis_data, f)

```