---
title: 记录一次hexo博客迁移过程
date: 2021-12-05 23:53:36
tags: 
    - hexo
categories:
	- Crsenal
---

背景：换了一个新电脑，需要把原来在github上配置的yixuan004.github.io博客环境重新在本地部署，中间涉及到一些软件的安装等

<!--more-->

# 记录一次hexo博客迁移过程

换了一个电脑后要重新部署自己的yixuan004.github.io博客，因为之前设置的已经比较完备了，把master分支和coding-pages分支（实际上的主分支）在github上分隔开了，所以首先拉取远程的coding-pages（实际上的主分支）

```shell
git clone https://github.com/yixuan004/yixuan004.github.io.git
```

在之后需要在本地补充npm安装hexo环境，**注意hexo环境必须安装在文件夹下**

如果本地没node/npm环境的话，可到官网下载
> http://nodejs.cn/download/

```shell
sudo npm install hexo
sudo npm install npm install hexo-deployer-git --save
```

在这之后理论上就可以进行操作和push了，和之前的博客一样即可：

> https://yixuan004.github.io/2021/09/13/我的首篇github博客/