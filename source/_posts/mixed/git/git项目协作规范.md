---
title: git项目协作规范
date: 2022-12-07 22:41:35
tags:
    - git
categories:
	- develop
	- mixed
	- git
---

在多人使用git的时候，需要大家来协作产生一个规范，这里整理了下自己在项目中曾经使用到的规范

<!--more-->

# git项目的协作规范

## 1. 自定的协作规范（在114和环保通项目有用到）

1. git pull master 拉取主分支
2. git checkout -b 自己的branch
3. 改
4. git push 自己的branch
5. 远程merge（github上）
6. git 切换到主分支并拉取最新的主分支
7. 删除自己的分支 git branch -d（小d）

对这个过程进行一个解释，首先拉取主分支，这样就能和现在最新的代码版本同步，然后建立自己的分支，在自己的分支上做修改，然后push到自己的branch后，再merge到master上（其中还可能涉及到codereview），然后切换到主分支，拉取最新的主分支同步别人的代码，这时候自己的分支就可以删除掉了，继续基于现在的最新master分支来做开发；