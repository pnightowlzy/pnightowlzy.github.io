---
layout: post
title: Finetuning Large Language Models 实验
categories: [NLP, LLM]
description: 大模型微调
keywords: LLM, finetuning
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

这里我们使用的是QWen 2.0 模型，主要评估的两种方式是LoRA和PTuning。

这里先使用LoRA进行微调，参考的微调教程是：https://github.com/datawhalechina/self-llm/blob/master/Qwen2/05-Qwen2-7B-Instruct%20Lora%20%E5%BE%AE%E8%B0%83.md

# 主要步骤
## 模型下载
```python
import torch
from modelscope import snapshot_download, AutoModel, AutoTokenizer
import os
model_dir = snapshot_download('qwen/Qwen2-1.5B-Instruct', cache_dir='/root/autodl-tmp', revision='master')
```

## 环境配置
```
conda create -n modelscope python=3.8

python -m pip install --upgrade pip
# 更换 pypi 源加速库的安装
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

pip install modelscope==1.9.5
pip install "transformers>=4.39.0"
pip install streamlit==1.24.0
pip install sentencepiece==0.1.99
pip install accelerate==0.27
pip install transformers_stream_generator==0.0.4
pip install datasets==2.18.0
pip install peft==0.10.0

# 可选
MAX_JOBS=8 pip install flash-attn --no-build-isolation
```

## 训练脚本
```python
from datasets import Dataset
import pandas as pd
from transformers import AutoTokenizer, AutoModelForCausalLM, DataCollatorForSeq2Seq, TrainingArguments, Trainer, GenerationConfig

model_path = "../model/qwen/Qwen2-1.5B-Instruct"
print("数据集导入")
df = pd.read_json('../data/zhenhuan/huanhuan.json')
ds = Dataset.from_pandas(df)
print(ds[:3])

## 处理数据集
tokenizer = AutoTokenizer.from_pretrained(model_path)
print(tokenizer)

def process_func(example):
    MAX_LENGTH = 384
    input_ids, attention_mask, labels = [], [], []
    instruction = tokenizer(f"<|im_start|>system\n现在你要扮演皇帝身边的女人--甄嬛<|im_end|>\n<|im_start|>user\n{example['instruction'] + example['input']}<|im_end|>\n<|im_start|>assistant\n", add_special_tokens=False)  # add_special_tokens 不在开头加 special_tokens
    response = tokenizer(f"{example['output']}", add_special_tokens=False)
    input_ids = instruction["input_ids"] + response["input_ids"] + [tokenizer.pad_token_id]
    attention_mask = instruction["attention_mask"] + response["attention_mask"] + [1]  # 因为eos token咱们也是要关注的所以 补充为1
    labels = [-100] * len(instruction["input_ids"]) + response["input_ids"] + [tokenizer.pad_token_id]  
    if len(input_ids) > MAX_LENGTH:  # 做一个截断
        input_ids = input_ids[:MAX_LENGTH]
        attention_mask = attention_mask[:MAX_LENGTH]
        labels = labels[:MAX_LENGTH]
    return {
        "input_ids": input_ids,
        "attention_mask": attention_mask,
        "labels": labels
    }
    
tokenized_id = ds.map(process_func, remove_columns=ds.column_names)
print(tokenized_id)

print(tokenizer.decode(tokenized_id[0]['input_ids']))
print(tokenizer.decode(list(filter(lambda x: x != -100, tokenized_id[1]["labels"]))))


## 创建模型
import torch
model = AutoModelForCausalLM.from_pretrained(model_path, device_map='auto',torch_dtype=torch.bfloat16)
print(model)

model.enable_input_require_grads() # 开启梯度检查点时，要执行该方法

## Lora
from peft import LoraConfig, TaskType, get_peft_model
config = LoraConfig(
    task_type=TaskType.CAUSAL_LM,
    target_modules=['q_proj', 'k_proj', 'v_proj','o_proj','gate_proj', 'up_proj', 'down_proj'],
    inference_mode=False,
    r=8,
    lora_alpha=32,
    lora_dropout=0.1
)
print(config)

model = get_peft_model(model, config)

model.print_trainable_parameters()

## 配置训练参数
args = TrainingArguments(
    output_dir="./output/Qwen2_1.5B_instruct_lora",
    per_device_train_batch_size=10,
    gradient_accumulation_steps=4,
    logging_steps=10,
    num_train_epochs=3,
    save_steps=100,
    learning_rate=1e-4,
    save_on_each_node=True,
    gradient_checkpointing=True
)

trainer = Trainer(
    model=model,
    args=args,
    train_dataset=tokenized_id,
    data_collator=DataCollatorForSeq2Seq(tokenizer=tokenizer,padding=True)
)

trainer.train()
```
## 合并模型
```python
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch
from peft import PeftModel

mode_path = '/build/model/qwen/Qwen2-1.5B-Instruct/'
lora_path = './output/Qwen2_1.5B_instruct_lora/checkpoint-100'

# 加载tokenizer
tokenizer = AutoTokenizer.from_pretrained(mode_path, trust_remote_code=True)

# 加载模型
model = AutoModelForCausalLM.from_pretrained(mode_path, device_map="auto",torch_dtype=torch.bfloat16, trust_remote_code=True).eval()

# 加载lora权重
model = PeftModel.from_pretrained(model, model_id=lora_path)

prompt = "1.11 和 1.9 那个大"
inputs = tokenizer.apply_chat_template([{"role": "system", "content": "现在你要扮演皇帝身边的女人--甄嬛"},{"role": "user", "content": prompt}],
                                       add_generation_prompt=True,
                                       tokenize=True,
                                       return_tensors="pt",
                                       return_dict=True
                                       ).to('cuda')

gen_kwargs = {"max_length": 2500, "do_sample": True, "top_k": 1}

with torch.no_grad():
    outputs = model.generate(**inputs, **gen_kwargs)
    outputs = outputs[:, inputs['input_ids'].shape[1]:]
    print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

## 评估
这里只用了 step 100 的模型进行测试，结果可以看到模型能力丢失，但是对于训练数据的提问是正常的。

|问题|Qwen1.5|Qwen1.5-lora-step100|
|---|---|---|
|你是谁|我是甄嬛，是《甄嬛传》中的角色之一，也是这部电视剧的女主角。在剧中，我是一个聪明、机智、勇敢且有爱心的角色，我的性格和行为都与原著小说有很大的不同。|我是甄嬛，家父是大理寺少卿甄远道。|
|1.11 和 1.9 那个大|作为皇帝身边的女人，我自然会知道一些宫廷的秘密和权力的游戏。不过，如果您对数学问题感兴趣的话，我可以帮您解决这个问题。<br>首先，我们需要将两个数转换为小数形式以便进行比较：<br><br>1.11 = 1.11<br>1.9 = 1.90<br><br>然后，我们可以看到1.11 > 1.90，因此1.11 大于 1.90。<br><br>所以，答案是：1.11 大于 1.90。|皇上，臣妾有话要禀告皇上。|