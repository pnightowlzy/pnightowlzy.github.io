---
layout: post
title: Dialogue Systems 理解
categories: [NLP, dialogue systems]
description: 对话系统理解和两种常见对话系统的设计与应用。
keywords: chatbots, dialogue systems, task-oriented dialogue system
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
对话系统分为两种，一种是开放领域的对话机器人（chatbots），这种对话系统根据目标分为许多下游任务分支，例如在情感、社交方面；具备特定领域知识的行业bot，例如法律，医疗等，大模型的出现让这种chatbots的构建变得更加简单和智能。另外一种任务型对话机器人(task-oriented dialogue systems)则更加服务于特定任务，有非常明确的意图集合(intent set), 这个集合是close domain的，因此无法进行闲聊等偏离特定任务的对话。

# 两种对话系统的对比
![dialog systems](/images/posts/dialog_systems/dialog%20systems.png)

## chatbots
聊天对话机器人主要是通过seq2seq模型，将用户的输入通过模型推理生成相应的对话，常见的方式有GPT类模型，训练方式有三个主要步骤：
1. Pretraining 语料预训练，掌握初始的自然语言理解能力。
2. Instruction Finetuning 指令微调，一般使用对话数据，让大模型养成人人对话习惯。
3. Reinforcement Learning From Human Feedback（RLHF） 强化学习，对齐人类价值观。

在模型训练完之后，再加入特定领域的知识可以让模型具备回答特定领域问题的能力，从而形成领域大模型。

常用于社交、情感、通用能力（代码、数学、语言类等）的开放领域交流。

## task-oriented dialogue systems
任务型对话机器人更偏向于执行特定的任务或者说回答预设的问题，例如常见的FAQ问答，预定、营销系统等。

任务型对话机器人一般需要具备以下能力：
1. 对话状态存储
2. 节点条件判断
3. 节点跳转
4. 