---
title: MultiWOZ数据集学习
date: 2021-09-13 20:25:07
tags:
    - NLP
    - Dialogue State Tracking
    - Dataset
categories:
	- NLP
---

# MultiWOZ数据集学习

主要记录了通过了解数据集作为对DST任务的入门，MultiWOZ数据集是适用于**任务型对话**系统的数据集，目前看了一遍这个简介后还是不太明白，感觉还需要各种渠道多了解下

Reference：
> 任务型对话系统数据集详解大全（MultiWOZ /DSTC） https://zhuanlan.zhihu.com/p/200747822 

<!--more-->

## 1. MultioWOZ的前世今生

·New WOZ：2018年剑桥大学研究人员在提出一个新的multi-domain DST模型的同时，顺带提出了New WOZ数据集，被看作是1.0的最初版本，发表在ACL；

·MultiWOZ 2.0：在1.0版本不错后，这些研究人员首次提出了MultiWOZ这个数据集，也就是一般所说的MultiWOZ 2.0，是当年EMNLP的Best Paper；

·MultiWOZ 2.1：2019年亚马逊的研究人员对MultiWOZ 2.0数据集中的一些错误进行修正，添加了对槽位的解释和对话行为的标注，代表MultiWOZ 2.1版本

·MultiWOZ 2.2：2020年google的研究人员在2.1的基础上提出升级版2.2；

## 2. MultiWOZ这个名字的由来

MultiWOZ的全称是Multi Domain Wizard-of-Oz，其中Wizard-of-Oz是Wizard-of-Oz Experiment或者Wizard-of-Oz Testing或者Wizard-of-Oz Method，是一种方法、技术。简单来说，你以为你在跟机器对话，实际上这个机器的背后是另一个人，是一种使还没有实现的技术先通过人工模拟系统的相应的技术手段。

因为用到了Wizard-of-Oz方法进行数据的制作和收集，所以采用这个名字。也提到了crowd-sourcing众包的方法，比较通俗的解释是“众包找人来干活，但不花钱，找社会上的志愿者，你一点我一点大家一起把这个事情完成”。关于这个方法作者还展示了他们做的GUI，专门用来让大众制作收集数据集。

## 3. MultiWOZ 2.0数据集细节：

·主要目标：在旅游城市的信息中心获取旅游者查询的高度自然对话

·Domain（域）：Attraction，Hospital，Police，Hotel，Restaurant，Taxi，Train，其中后四个域属于扩展域，包括子任务Booking。每段对话涉及1-5个领域，因此长度和复杂性差别很大。全部的act和slot如下：

![](/images/2021-09-13-20-49-44.png)

·数量及分布：10438个对话，其中3406个单领域对话，7032个多领域对话，多领域中，包含最少2-5个领域。70%的对话超过10个会和，其中单领域平均轮数为8.93，多领域为15.39。在数据里，对话的序号前面是SNG的就是单轮，MUL的就是多轮。

·数据结构：每个对话包含a goal，multiple user，system utterances(系统话语)，belief state（？信度状态），dialogue acts and slots（可能需要结合具体数据进一步理解下）

·Belief state：有三个部分，分别是semi，book，booked、其中semi是特定领域里的槽值；book在特定领域的booking slots；booked是book的一个子集，在book这个字典里，是booked entity

↑这段话不太好理解，感觉还得从具体数据内容来看

## 4. MultiWOZ as a New Benchmark

MultiWOZ作为新的benchmark，提供了三个子任务，分别是，dialogue state tracking、dialogue-act-to-text generation，dialogue context-to-text generation

其中
·Dialogue state tracking：用在餐厅子领域的overall和joint goals的accuracy作为评价指标


相关指标更新见：https://github.com/budzianowski/multiwoz

感觉理解的还不是很清楚，还需要进一步看下对话相关的任务一类的来理解吧