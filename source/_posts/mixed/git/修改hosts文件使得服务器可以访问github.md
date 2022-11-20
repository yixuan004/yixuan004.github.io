---
title: 修改hosts文件使得服务器可以访问github
date: 2022-11-19 16:48:51
tags:
    - git
categories:
	- Crsenal
---

之前有过在本机上如何访问github的操作，原理上来说是让git相关的时候走本机代理配置的端口，但是在该服务器上操作的时候这个不是很好用

在服务器上，可以通过首先网站查询可以ping到的github ip，然后再修改`/etc/hosts`文件的方式

<!--more-->

# 修改hosts文件使得服务器可以访问github

## 1. 操作

首先登录如下网站，找到一个可以ping到的github ip地址：https://ping.chinaz.com/github.com

![image-20221119200639129](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221119200639129.png)

通过root账号修改`/etc/hosts`文件，加入如下内容：

```shell
140.82.113.3	www.github.com
140.82.113.3	github.com
```

加入后效果如下，再次`ping www.github.com`，可以得到响应，证明正确

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221119200931544.png" alt="image-20221119200931544" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221119201002658.png" alt="image-20221119201002658" style="zoom:50%;" />
