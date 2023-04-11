---
title: Docker常用命令记录
date: 2023-03-13 10:02:39
tags:
    - docker
categories:
	- develop
	- docker
	- docker使用
---

记录常用的docker命令，作为一个自己积累的手册未来自己进行查阅

<!--more-->

# Docker常用命令记录

## 1. docker tag 标记本地镜像

标记本地镜像在推送到远程库的时候比较常用？

```shell
docker tag xxxx:v1.4 docker.xxx.io/xxxx:v1.4
```

## 2. docker save 镜像导出

```shell
docker save -o image.tar image:tag
docker save > image.tar image:tag
docker save image:tag > image.tar
```

## 3. docker load 镜像导入

```shell
docker load -i image.tar
docker load < image.tar
```

## 4. 删除镜像

为了避免把该镜像的一些被其他库也依赖的依赖层删掉，需要加入`--no-prune`参数，不移除该镜像的过程镜像

```shell
docker rmi -f image:tag --no-prune
```

## 5. docker exec -it [CONTAINED_ID] /bin/bash 进入正在执行的容器

有些情况下，一个正在运行的容器使用后台的方式进行挂载，这样的话可以使用这条命令挂载进去来调试

调试如果比较顺利的话，可以docker commit提交上去（类似于多安装了一些库什么的），不过还是不太推荐docker commit，这种基本会越堆越大的，还是推荐再返回来修改一下Dockerfile

## 6. docker ps -a | grep image:tag 和 docker images | grep image:tag

查看正在启动的container和已经创建过的image，再配合上`grep -v grep`等命令可以进行删除

## 7. docker logs [CONTAINER_ID]

如果有些docker出现了不知道什么情况的终止，比如通过ps看不到但是通过ps -a 可以看到，就可以通过这个docker logs，看到docker内部的一些错误输出