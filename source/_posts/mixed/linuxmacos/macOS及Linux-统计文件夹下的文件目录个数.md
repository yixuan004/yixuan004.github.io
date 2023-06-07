---
title: macOS及Linux-统计文件夹下的文件目录个数
date: 2021-10-28 00:34:37
tags: 
    - linuxmacos
categories:
	- develop
	- mixed
	- linuxmacos
---

需求：希望直接在命令行中统计文件夹下的文件、目录个数

<!--more-->

1. 统计文件夹下文件个数，包括子文件
```
ls -lR | grep "^-"| wc -l
```

2. 统计文件夹下目录个数，包括子目录
```
ls -lR | grep "^d"| wc -l
```