---
title: macOS及Linux-在arm架构mac芯片上安装homebrew
date: 2023-06-07 23:55:14
tags: 
    - linuxmacos
categories:
	- develop
	- mixed
	- linuxmacos
---

需求：在arm架构（m1 m2芯片的苹果电脑）下，直接使用一些官方的homebrew安装可能会有问题，找了一个博客解决了这个问题

<!--more-->

reference：

https://blog.csdn.net/m0_55644132/article/details/129006590

```shell
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

之后按照shell上面的提示安装就可以