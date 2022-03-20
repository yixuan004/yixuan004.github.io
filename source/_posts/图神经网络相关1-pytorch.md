---
title: 图神经网络相关1-pytorch
date: 2022-03-15 10:26:49
tags:

categories:
	- PyTorch
---



<!--more-->


# 如何理解Graph Convolutional Network(GCN)?

Reference:
> https://www.zhihu.com/question/54504471/answer/611222866

图卷积的核心思想是利用「边的信息」对「节点信息」进行「聚合」，从而生成新的「节点表示」。（有的研究在此基础上利用「节点表示」生成「边表示」或者「图表示」完成自己的任务）

**在DST任务的槽位-槽位-对话历史三元组筛选伴随更新的情况**
