---
layout: mindmap
title: Testcases
cate1: Mindmap
cate2:
description: 个人技能树思维导图
keywords: 技能树, 思维导图, mindmap, 脑图
---
# CoreFunctions
## ID
### R01
## Description
### 收费项描述
## SubFunctions
### ID
#### R01.1
### Description
#### 收费项规则类型
### RequirementPoints
#### ID
##### R01.1.1
#### Description
##### 固定金额类: 固定金额收费。例如：XXX费，每笔5元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.2
#### Description
##### 固定比例类: 按照交易金额的固定比例收费。例如：XXX费，交易金额的1％。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.3
#### Description
##### 固定比例并且有上、下限额: 按照交易金额的固定比例收费，并且有上、下限额。例如：XXX费，交易金额的1％，最低5元，最高50元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.4
#### Description
##### 全额累进固定金额类: 按照交易全额分段，每一段按照固定金额收费，并且有上、下限额。例如：XXX费，1－10万收10元，10－50万收20元，最低10元，最高20元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.5
#### Description
##### 全额累进固定比例类: 按照交易全额分段，每一段按照固定比例收费，并且有上、下限额。例如：XXX费，1－10万收交易金额的1％，10－50万收交易金额的0.5％, 有最低10元，最高200元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.6
#### Description
##### 全额累进固定金额、比例混合计费类: 按照交易全额分段，每一段按照或按照金额或按照比例收费，并且有上、下限额。例如：XXX费，1－10万收10元，10－50万收交易金额的10元，50万以上收交易金额的0.5％, 最低10元，最高200元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.7
#### Description
##### 超额累进固定金额类: 按照交易金额（累计交易金额）超额部分为基础，每超过一个级距时，分别以其超额的部分适用相应级距按照固定金额收费，并且有上、下限额。例如：XXX费，当日同一个人累计提现总额超过5万元以上的5－10万的收10元，10－20万的部分收20元，共收30元, 最低10元，最高200元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.8
#### Description
##### 超额累进固定比例类: 按照交易金额（累计交易金额）超额部分为基础，每超过一个级距时，分别以其超额的部分适用相应级距按照比例收费，并且有上、下限额。例如：XXX费，对当日同一个人累计提现总额10万元以上的，对超出10万元部分的按0.07%收取提现费，最低10元，最高50元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.9
#### Description
##### 超额累进固定金额、比例混合计费类: 按照交易金额（累计交易金额）超额部分为基础，每超过一个级距时，分别以其超额的部分适用相应级距或按照金额、或按照比例收费，并且有上、下限额。例如：XXX费，对当日同一个人累计提现总额5万元以上的，5－10万的部分收10元，10万以上的部分收交易金额的0.5％, 最低10元，最高200元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.10
#### Description
##### 笔数固定金额类: 根据交易笔数按照固定金额收费。例如XXX费，第4笔起每笔收费2元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.11
#### Description
##### 笔数固定比例类: 根据交易笔数按照比例收费。例如XXX费，每自然月前3笔免费，第4笔起每笔按照交易金额的1％，最低5元，最高20元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.12
#### Description
##### 笔数超额累进固定金额、固定比例混合类: 按照交易笔数（累计交易笔数）超额部分为基础，每超过一个级距时，分别以其超额的部分适用相应级距或按照金额、或按照比例收费，并且有上、下限额。例如：XXX费，每自然月前3笔免费，第4笔起交易金额在5－10万的部分收10元，10万以上的部分收交易金额的0.5％, 最低10元，最高200元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.13
#### Description
##### 限额固定金额类: 限额以下免费，限额以上每笔按照固定金额收取。例如XXX费，5万元（含）以下免费，5万元以上每笔收费2元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.14
#### Description
##### 限额固定比例类: 限额以下免费，限额以上每笔固定比例收费, 并且有上、下限额。例如XXX费，5万元（含）以下免费，5万元以上每笔收费按照交易金额的1％，最低5元，最高20元。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.15
#### Description
##### 人工定价: 和客户协商人工确定收费金额，或区域性定价类。只用于柜面等有人工干预的定价。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.16
#### Description
##### 周期性收费: 每隔一定周期（月、季、半年、年）进行收费。
#### Dependencies/Associations
##### None
#### ID
##### R01.1.17
#### Description
##### 外系统定价: 外系统自行确定收费金额。
#### Dependencies/Associations
##### None
### ID
#### R01.2
### Description
#### 折扣规则
### RequirementPoints
#### ID
##### R01.2.1
#### Description
##### 多维度折扣: 多维度的标准费用折扣：根据渠道、机构、客户等级、是否员工、卡类型（包括卡等级）、金额、客户签订产品协议等维度规则进行打折费用的计算。其中客户签订产品协议维度是针对客户签订某种产品收费协议，进行相应的折扣，但签订只能按客户定义到产品级。
#### Dependencies/Associations
##### None
#### ID
##### R01.2.2
#### Description
##### 前端系统折扣: 特殊交易由源系统直接输入折扣上送计费系统，扣费处理时直接按输入的折扣与计费系统维护的折扣进行比较，取低值或取高值进行计算。
#### Dependencies/Associations
##### None
#### ID
##### R01.2.3
#### Description
##### 前台手工输入折扣: 人工金额优惠/人工比例优惠。柜面业务都支持人工优惠，由人工可进行手工输入折扣（优惠金额或比例），柜面系统根据计费系统返回应收金额和人工输入的折扣计算实收金额（此功能由前台完成）。
#### Dependencies/Associations
##### None