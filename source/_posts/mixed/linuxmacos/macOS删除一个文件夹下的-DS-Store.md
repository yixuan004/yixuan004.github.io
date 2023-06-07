---
title: macOS删除一个文件夹下的.DS_Store
date: 2021-10-11 08:47:34
tags: 
    - linuxmacos
categories:
	- develop
	- mixed
	- linuxmacos
---

进入目标目录，输入以下代码

```shell
find ./ -name ".DS_Store" -depth -exec rm {} \;
```