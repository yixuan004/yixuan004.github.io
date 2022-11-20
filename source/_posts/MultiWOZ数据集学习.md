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

主要记录了通过了解数据集作为对DST任务的入门，MultiWOZ数据集是适用于**任务型对话**系统的数据集，目前看了一遍这个简介后还是不太明白，感觉还需要各种渠道多了解下

Reference：
> 任务型对话系统数据集详解大全（MultiWOZ /DSTC） https://zhuanlan.zhihu.com/p/200747822 

<!--more-->

# 2021.9.13 首次学习

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

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-09-13-20-49-44.png)

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


# 2021.10.24 笔记补充 MultiWOZ2.2以及MultiWOZ系列的前世今生

## Overview
MultiWOZ2.2数据集发表在ACL2020上，发布了MultiWOZ数据集的升级版，作者来自google和伊利诺伊大学芝加哥分校

> https://aclanthology.org/2020.nlp4convai-1.13/

## MultiWOZ发展史

1. NewWOZ（ACL2018），但好像用的人不多？
2. MultiWOZ2.0（EMNLP2018）
3. MultiWOZ2.1 针对2.0中的一些错误进行修正，添加了对槽位的解释和对对话行为的标注，亚马逊研究人员。
> Eric M, Goel R, Paul S, et al. MultiWOZ 2.1: A Consolidated Multi-Domain Dialogue Dataset with State Corrections and State Tracking Baselines[C]//Proceedings of the 12th Language Resources and Evaluation Conference. 2020: 422-428.
4. MultiWOZ2.2（ACL 2020），是2.1的进一步升级版

## 标注错误

下面开始介绍2.2对2.1的改进，首先是标注错误。
在了解标注错误之前，首先了解一下什么是**Wizard-of-Oz setup**。
Wizard-of-Oz setup是由两个众包工人组成一队，一个扮演user，一个扮演agent。
每组对话由一个特定的目标来驱动。
在每轮user对话结束后，扮演agent的众包工会标注出更新后的对话状态，并依此生成一个回复。
由于这种方法是完全靠人工去标注的，那么就会容易产生噪声。

## Hallucinated Values（直译为“幻觉价值观”）

作者将标注中错误的value分为4类。如下的4种

1. Early Markup：未来会出现的槽值被agent标注为了当前的值，如图所示，User说了：Help me find a moderate price british food place please，此时系统回复了restaurant one seven is a nice place. Do you want to book? 此时应该意图还没有锁在r-name=one seven上
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-24-22-05-41.png)

2. Annotation from Database：这些值没有在对话中出现，而是被程序错误的从数据库中抽取出来的。
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-24-22-12-29.png)

3. Typo：一些印刷或者排版书写错误
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-24-22-33-29.png)


4. Implicit Time Processing：一些隐式的时间表示，有可能是根据前面的相对时间加减计算出来的时间，也有时候会四舍五入到最接近的时间。这样会家中模型学习的负担
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-24-22-33-40.png)

## 状态更新不一致

状态更新不一致的主要原因有两种：

1. value来源有多个：一个槽值在对话状态可能有各种来源：由用户提供、由系统提供、从对话状态中不同的domain下的值继承过来的、来源于本体中定义的。

2. value的释义不规范：多个value其实含义是一样的。2.1在定义这些内容的时候缺乏一个显式的规则。这就使得模型训练的时候造成困惑，比如说同时有18：00和6pm，其实都是对的，但是训练过程中ground truth只有一个，那么就会错误的惩罚另一个
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-25-00-00-53.png)

3. 跟踪策略不一致：众包工人标注时的标准不一致，有的只标注了用户提到的value，有的将用户统一的agent提到的value也标了进来
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-25-09-13-23.png)

## 本体中的问题

在MultiWOZ2.0中定义了一个本体，他声称美居乐所有slot的value。但是后来的研究人员发现这个本体其实是很不完整的，所以为了达到很好的效果，研究人员往往要自己再重新定义一个自己的本体。为了解决这个问题，2.1试图列出对话状态中的所有值来重建本体，但是仍然存在一些未解决的问题。

比如说：

1. 在同一槽位中具有相同语义的多个value
- 8pm=20:00，a and b guesthouse = a and b guest house

2. 本体中多个slot-value无法与数据库中的实体相关联**（这个地方不是很理解）**


## 纠正程序

**为了解决上面提到了这些问题，作者这里提到了一套纠正程序**

首先，关于本体。在本体为slot枚举所有可能的value是一件很不现实的事情。比如餐馆的名称，订餐的时间。

因此这里沿用了一种Schema的概念，也就是categorical和non-categorical的区别

categorical：slot中value有限（数量小于50），value列举出所有可能的值
non-categorical：具有大量possible values的slot，value从对话历史中提取出来

schema将所有slot分成两类，一类叫做non-categorical，另一类叫做categorical

non-categorical包括那些具有大量可能value的slot，schema中对这些slot不去预定义一个value的list，对于这类slot的value是从对话历史中提取出来的

categorical包含了那些value有限的slot，以及在训练数据中具体value数量少于50个的slot。在schema里头对这类slot会列举出所有可能的value
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-25-09-40-25.png)

自：MWZ2.0数据集中的schema好像是比较明显的。

## Categorical Slots

对于分类槽位，所有可能的值都是由2.1的数据库构建的。

其中有两个特殊的词，dont-care和unknown
- don't care是用户在对某一个值没有偏好的时候使用的
- unknown指的是那些在schema中的值无法满足用户特定的需求

例如：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-25-09-43-09.png)

## Non-categorical Slots

对于非分类槽位。上面已经说过，它的value是从历史对话中提取出来的。作者这里使用一种字符串匹配的方式找到对话历史中语义最接近的值。如果有多个，就取最近提到的那个

在2.2中，在标注中允许一个slot有多个value，模型预测出来任意一个都算对
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-25-09-48-10.png)

当多个slot对应的是同一个value的时候，作者这里采用链式存储的思想。后来的slot不标注span，而是标注这个value对应的原始的slot

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-10-25-09-54-53.png)

这个图也显示了有在categorical和non-categorical上分别计算JointAcc的习惯