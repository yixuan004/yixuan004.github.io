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

把整个Transformer当做一个黑盒，在机器翻译任务中，一种语言作为Transformer的输入，另外一种经过翻译后的语言作为Transformer的输出。
![](/images/2021-10-08-00-23-19.png)

略微细化下，我们看到Transformer架构由Encoding组件，Decoding组件，还有Encoding Decoding两个组件之间的连接组成。
![](/images/2021-10-08-00-25-51.png)

其中的Encoding组件是一个由encoders组成的栈。Decoding组件是一个由和encoders相同数目的decoders组成的栈。
![](/images/2021-10-08-01-21-44.png)

编码器在结构上都是相同的（但是互相之间不共享权重）。每一层分为两个子层，分别是Self-Attention机制和Feed Forward Neural Network

编码器的输入首先要通过一个self-attention层，一个帮助编码器在编码特定单词时查看输入句子中其他单词的层。将在之后进一步分析。

self-attention层的输出反馈给前馈神经网络（feed forward neural network），完全相同的前馈网络独立应用于每个位置。
![](/images/2021-10-08-14-45-35.png)

解码器具有这两个层，但在这两个层之间有一个Encoder-Decoder注意力层，帮助解码器关注输入句子的相关部分（类似于seq2seq模型中注意力的作用）。
![](/images/2021-10-08-14-57-11.png)

# Bringing The Tensors Into The Picture

首先使用embedding算法将每个输入字词转化为向量。使用这种小型的vectors来进行表示。（在SUMBT中，这一步对应了哪一步？为什么这里每个输入字词会变为vectors?）
![](/images/2021-10-08-15-01-00.png)

embedding只发生在最底层的编码器中。所有编码器都有一个共同的抽象概念，即它们接收一个大小为512的向量列表——在底部编码器中是word embedding，在其他编码器中则是直接位于下方的编码器的输出。这个列表的大小是我们可以设置的超参数，基本上是训练数据集中最长句子的长度。

在输入序列中word embedding后，每个单词都会流经编码器的两层。