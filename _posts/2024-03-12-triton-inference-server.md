---
layout: post
title: Triton Inference Server 你需要了解的加速推理框架
categories: [知识学习, 推理框架]
description: triton，常用工程化加速推理框架
keywords: triton, 推理框架, 加速推理框架
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
# Triton Inference Server
triton-inference-server服务常用于加速模型推理，是模型部署的关键服务。
triton 在部署的过程中可以部署多个模型，同时可以将多个模型进行串联，类似之前pipeline的形式，在triton中的定义叫做ensemble。

这里的模型可以是直接通过制定platform加入的模型权重，也可以是用python脚本（platform: python）写的数据预处理方式，分词服务，后处理方式的自定义模型。
在部署模型推理服务时，我们需要将模型转换为triton-inference-server可加载的backend，目前可加载的backend可以在下面的链接中找到：
[点击链接](https://github.com/triton-inference-server/backend/blob/main/README.md#triton-backend-api)

# 模型配置
在定义一个 triton 推理模型时，我们需要定义以下内容，模型的平台（backend），单次能够请求的最大数目，入参以及出参。
```
  platform: "tensorrt_plan"
  max_batch_size: 8
  input [
    {
      name: "input0"
      data_type: TYPE_FP32
      dims: [ 16 ]
    },
    {
      name: "input1"
      data_type: TYPE_FP32
      dims: [ 16 ]
    }
  ]
  output [
    {
      name: "output0"
      data_type: TYPE_FP32
      dims: [ 16 ]
    }
  ]
```
在python脚本中出入参数的名称和类型都比较好理解，你可以通过triton所提供的api来获取到对应名称的具体内容，但如果是通过模型加载的话，出入参数的名称需要在导出推理模型时就定义好，如果没有办法对齐，推理会失败。

具体的名称解释可以在[这里](https://github.com/triton-inference-server/server/blob/main/docs/user_guide/model_configuration.md)查看.

可支持的[数据类型](https://github.com/triton-inference-server/server/blob/main/docs/user_guide/model_configuration.md#datatypes)。

# Ensemble配置
这里提供了具体的示例，[点击链接](https://github.com/triton-inference-server/server/blob/main/docs/user_guide/architecture.md#ensemble-models)
需要用户定义好，出入参数，使用到的模型和数据流, 同一个ensemble中的模型簇可以使用所有其他模型的输出和输入（依赖条件满足的情况下）。

# 模型转换
keras模型（tf模型）转 graphdef模型
```python
from keras.models import load_model
from keras import backend as K
 
def freeze_session(session, keep_var_names=None, output_names=None, clear_devices=True):
    from tensorflow.python.framework.graph_util import convert_variables_to_constants
    graph = session.graph
    with graph.as_default():
        freeze_var_names = list(set(v.op.name for v in tf.global_variables()).difference(keep_var_names or []))
        output_names = output_names or []
        output_names += [v.op.name for v in tf.global_variables()]
        input_graph_def = graph.as_graph_def()
        if clear_devices:
            for node in input_graph_def.node:
                node.device = ""

        frozen_graph = convert_variables_to_constants(
            session,
            input_graph_def,
            output_names,
            freeze_var_names
        )
        return frozen_graph
 
 
def save_model(save_path):
    K.set_learning_phase(0)
    sess = K.get_session()
    frozen_graph = freeze_session(
        K.get_session(),
        output_names = ["dense_19/Softmax"], # 这里需要你改为你自己模型的输出层的名称。
    )
 
    with open(save_path, 'wb') as f:
        print(f"save model to {save_path}")
        f.write(frozen_graph.SerializeToString())
 
 
# 使用时需要补上加载模型步骤
save_model(save_graphdef_path)
```