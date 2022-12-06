---
title: Docker-Learning
date: 2021-12-06 11:51:51
tags:
  - docker
categories:
	- develop
	- docker
	- docker学习
---

背景：docker有一种更加轻量级虚拟机的感觉，正好在一个课程中需要使用HBase、Hadoop等，又不方便在本地安装，所以可以使用docker的方式，也借此机会学习docker

<!--more-->

# docker的安装

macOS上可以安装docker destop，是个可视化的工具
> https://www.docker.com/get-started


# Getting Started

1. 第一步，clone a repository

Getting Started项目是一个简单的Github repository，包含了一切建立镜像（image）和运行为容器（container）的过程

通过在容器中运行git克隆存储库

```shell
CuriousdeMacBook-Pro:~ curious$ docker run --name repo alpine git clone https://github.com/docker/getting-started.git
```

2. 之后，构建image

Docker映像是仅用于容器的私有文件系统。它提供容器所需的所有文件和代码。

```shell
CuriousdeMacBook-Pro:~ curious$ cd getting-started 
CuriousdeMacBook-Pro:getting-started curious$  docker build -t docker101tutorial .
```

3. 运行第一个container

基于上一步中生成的映像启动容器。运行容器会启动具有私有资源的应用程序，这些资源与计算机的其余部分安全隔离。

```shell
CuriousdeMacBook-Pro:getting-started curious$ docker run -d -p 80:80 --name docker-tutorial docker101tutorial cfd8702ce730feb351565711d7a44d147476d8faa18b03ae2d4815cc137d121b
```

4. 保存并分享镜像

在Docker Hub上保存和共享您的映像，以便其他用户可以轻松地在任何目标计算机上下载和运行映像。