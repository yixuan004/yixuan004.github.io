---
title: 再读transformer-李沐bilibili笔记
date: 2021-11-19 16:18:40
tags:
    - NLP
    - Transformer
categories:
	- NLP
---

Transformer是Google的研究者在2017年《Attention Is All You Need》论文中提出的用于seq2seq任务的模型，它没有RNN的循环结构或CNN的卷积结构，在机器翻译等任务中取得了一定的提升。https://proceedings.neurips.cc/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Paper.pdf

李沐Transformer论文逐段精读【论文精读】：https://www.bilibili.com/video/BV1pu411o7BE?spm_id_from=333.999.0.0

之前在自己的一些学习中，

<!--more-->

# Abstract 摘要

![](/images/2021-11-19-16-34-00.png)

在主流的序列转录（例如对一个序列进行翻译这类的任务，机器翻译）模型中，主要是依赖循环和卷积神经网络，使用一个encoder-decoder的网络架构


# Conclusion 结论

很有前瞻性，预测了未来，在图像、语音和视频领域上Transformer架构的应用


# Introduction 导言


# Background 相关工作


# Method

query为了算相似度，会和所有k做内积，内积后softmax也就的得到了一组分布的感觉，代表和每个k的相似度