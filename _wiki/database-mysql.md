---
layout: wiki
title: MySQL Script
cate1: Python
cate2: MySQL
description: Python MySQL 库脚本
keywords: MySQL
---

```python
def connect_to_mysql(host, user, db, password, port=3306):
    connection = pymysql.connect(host=host,user=user,password=password,db=db,port=port,autocommit=True)
    cur = connection.cursor()
    return cur

def execute_sql(cur, sql):
    cur.execute(sql)
    return cur.fetchall()

def foreach(results):
    # 二维数组   
    # 价钱 ｜ 年龄
    # [[1,2][2,3]]
    for result in results:
        for r in result:
            print(r)
        print("----")
    ## output:
    ## 1
    ## 2
    ## ----
    ## 2
    ## 3    
```