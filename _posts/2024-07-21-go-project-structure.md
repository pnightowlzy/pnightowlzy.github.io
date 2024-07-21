---
layout: post
title: Go Project Framework 项目框架
categories: [Go, 设计模式]
description: golang项目框架设计
keywords: Go, 设计模式
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

最近在看golang相关开发的框架，没有看到比较合适的，翻了翻相关的博客，找到了一些设计模式的内容，在这里记录一下，也谈一谈自己的看法。

# 参考信息
1. [一文带你了解 Java 中的 DAO、DTO、PO、SO、BO、VO](https://juejin.cn/post/7132750942973198350)
2. [Go项目的目录结构基本布局](https://blog.csdn.net/hguisu/article/details/129342554)

# 概念介绍
在开发时，不管项目的复杂程度，对于整体的开发逻辑其实大同小异。在设计上主要可以分为两个模式：
1. 基于Module模块化的项目架构
2. 基于Function功能化的项目架构

## 基于Module模块化的项目架构
基于module就是把整个系统模块化，比如用户模块，音乐模块，此后每增加一个功能就增加一个新的模块，对其他模块的影响比较小。每个模块都有自己的 实体结构、controller层、service层、dao层。
<img src="/images/posts/design-mode/module.png">

## 基于Function功能化的项目架构
基于function就是将所有的模块的实现都认为是一种能力的实现，比如在bean内定义所有的数据结构，在controller层定义所有与前端交互的接口等。
<img src="/images/posts/design-mode/function.png">

