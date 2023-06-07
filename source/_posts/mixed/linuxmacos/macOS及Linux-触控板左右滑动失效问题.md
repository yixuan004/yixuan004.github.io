---
title: macOS及Linux-触控板左右滑动失效问题
date: 2022-02-28 10:58:39
tags: 
    - linuxmacos
categories:
	- develop
	- mixed
	- linuxmacos
---

需求：某些场景下（其实感觉是插拔HDMI线外接显示器的时候），可能出现触控板手势左右滑动失效的问题；

<!--more-->

# 解决办法 killall Dock

在网上查到的解决办法，可在终端执行如下命令，即可通过类似一种自动重启的方法解决这个问题

```shell
killall Dock
```