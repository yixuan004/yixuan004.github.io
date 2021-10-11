---
title: NLP代码学习笔记
date: 2021-10-09 11:52:19
tags:
    - NLP
categories:
	- NLP
---

# NLP代码学习笔记

记录一些代码中常用的操作说明及bug解决记录

<!--more-->

# nn.Embedding.from_pretrained

整个代码的上下文是在做label_ids和slot_ids的embedding
```python
def initialize_slot_value_lookup(self, label_ids, slot_ids):
    '''
    self.sv_encoder = BertForUtteranceEncoding.from_pretrained(
        os.path.join(args.bert_dir, 'bert-base-uncased')
    )
    '''
    self.sv_encoder.eval() # 切换到evaluate模式

https://blog.csdn.net/dakenan1/article/details/102989747

self.slot_lookup = nn.Embedding.from_pretrained(hid_slot, freeze=True)
```