---
title: 记录服务器miniconda配置虚拟环境
date: 2021-11-10 09:23:12
tags:
    - macOS基础操作
categories:
	- Crsenal
---

在服务器上之前自己一直用的是virtualenv的环境建立虚拟环境，有一种比较依赖本机已经安装了相关python的感觉。另外昨天在使用virtualenv的时候发现pip的版本对应有些问题，会报sys.stderr.write(f"ERROR: {exc}")这个错

大佬同学推荐了一个miniconda的方法，感觉安装起来很方便，虚拟环境感觉都可以使用类似的方法了

<!--more-->

Reference：
```https://conda.io/en/latest/miniconda.html```

# 安装Linux Installers

![](/images/2021-11-10-09-43-27.png)

下载后会得到一个```Miniconda3-latest-Linux-x86_64.sh```，上传到服务器上后可通过bash直接安装

# 建立、启动虚拟环境

可通过如下命令建立启动虚拟环境，

```shell
$ conda create -n myenv python=3.8 // 安装python
$ conda activate myenv
(myenv)$ pip install ... 
```