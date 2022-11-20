---
title: 记录一次pycharm的配置过程
date: 2021-11-24 09:21:29
tags:
    - macOS基础操作
categories:
	- Crsenal
---

在之前会用vscode远程连接服务器写代码，pycharm是另外一种的操作，用了pycharm后感觉可以在本地也备份一份的感觉

在同步的时候，可以本地不存储那些数据文件，只存储核心的代码文件，这样如果远程服务器和github无法连接的时候，就可以将本地的那些代码文件向github同步（正好在向github同步的时候要避免对于数据的同步，而只同步代码）

<!--more-->
# 操作流程

1. 新建project

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-22-57-01.png)

2. 选择路径这里，这里venv可以随意选择（个人感觉最好不选择，但是不一定能跳过这一步）

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-22-58-37.png)

3. 打开File -> Preference，选择Interpreter

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-23-00-48.png)

这里的Interpreter需要选择远程的ssh Interpreter
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-23-01-51.png)

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-23-05-04.png)

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-23-05-14.png)

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-23-05-57.png)

4. 设置deployment，设置有远程文件夹和本地文件夹的同步

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-23-09-45.png)

5. 测试torch.cuda.is_available()

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-23-10-41.png)

6. 设置本地删除文件后，远程也会随之删除

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2021-11-24-23-12-10.png)

# Reference

> https://zhuanlan.zhihu.com/p/149040742
> https://blog.csdn.net/renhanchi/article/details/93769003