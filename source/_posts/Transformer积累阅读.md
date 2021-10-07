---
title: Transformer积累阅读
date: 2021-10-07 23:57:00
tags:
    - NLP
    - Transformer
categories:
	- NLP
---

# Transformer积累阅读

Transformer是Google的研究者在2017年《Attention Is All You Need》论文中提出的用于seq2seq任务的模型，它没有RNN的循环结构或CNN的卷积结构，在机器翻译等任务中取得了一定的提升。

主要的motivation在于RNN、LSTM、GRU类的序列结构中的固有顺序属性阻碍了训练样本之间的并行化，对于长序列，内存限制将阻碍对训练样本的批量处理。

Transformer中完全依赖于注意力机制对输入输出的全局依赖关系进行建模。因为对依赖的建模完全依赖于注意力机制，Transformer使用的注意力机制被称为自注意力（self-attention）

<!--more-->

References:
> https://zhuanlan.zhihu.com/p/85864250
> http://jalammar.github.io/illustrated-transformer/

# A High-Level Look

