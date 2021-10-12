---
title: NLP代码学习笔记
date: 2021-10-09 11:52:19
tags:
    - NLP
categories:
	- NLP
---

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
    # 作者把调用sv_encoder的部分fix住
    for p in self.sv_encoder.bert.parameters():
        p.requires_grad = False
    '''
    self.sv_encoder.eval() # 切换到evaluate模式

    # Slot encoding，这个是fix住的部分
    slot_type_ids = torch.zeros(slot_ids.size(), dtype=torch.long).to(self.device) # 初始化一个空的slot_type_ids
    slot_mask = slot_ids > 0
    hid_slot, _ = self.sv_encoder(slot_ids.view(-1, self.max_label_length),
                                    slot_type_ids.view(-1, self.max_label_length),
                                    slot_mask.view(-1, self.max_label_length),
                                    output_all_encoded_layers=False) # 获取CLS的token，hidden的
    hid_slot = hid_slot[:, 0, :] # 博客中指出，这样的操作是在获取CLStoken，而CLStoken是用来进行分类的，也一般被认为是整句话的embedding
    hid_slot = hid_slot.detach()
    self.slot_lookup = nn.Embedding.from_pretrained(hid_slot, freeze=True) # slot的embedding结果，是不可以训练的
```

# 报在同时使用cpu和gpu的错误

报在同时使用cpu和gpu的错误除了tensor要tensor.to(device)以外，model也要.to(device)，否则可能会报在同时使用cpu和gpu的错误

# pytorch中判断两个tensor是否相等

1. tensor.equal()方法
```python
# 该方法用于比较两个tensor是否一样，一样则返回True否则为False
a = torch.tensor([1,2,3,4])
b = torch.tensor([1,2,3,4])
print(a.equal(b))    # 返回True
```

2. tensor.eq()方法
```python
# 该方法用于主元素比较是否相等，相等则在对应位置返回True，否则为False
a = torch.tensor([1,2,2,3])
b = torch.tensor([2,2,3,3])
print(a.eq(b))  # 返回tensor([False,True,False,True]),与a==b返回的结果一样
```

# pytorch输出整个tensor的方法
```python
torch.set_printoptions(profile="full")
print(x) # prints the whole tensor
torch.set_printoptions(profile="default") # reset
print(x) # prints the truncated tensor
```
在这样的输出下，之后就可以写到文件里了

# tensor = tensor[0, :, 0]
这种操作可能代表着仅需要获取bert的cls token的embedding结果，也被认为是整句话的embedding