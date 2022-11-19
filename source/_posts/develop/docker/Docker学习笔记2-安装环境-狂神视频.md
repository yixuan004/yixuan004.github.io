---
title: Docker学习笔记2-安装环境-狂神视频
date: 2022-01-07 22:30:41
categories:
    - docker
---

本篇博客记录根据狂神视频学习的Docker安装过程和一些安装后的基本配置

TODO 未来将在此博客补充Docker的离线部署过程，在真实的使用场景下还是有很多离线部署的场景需求的

<!--more-->

# Docker安装环境和一些基础配置

## Docker的安装

**注，下述安装过程是联网配置的过程，如果是离线部署的话还需要未来进一步学习**

> 环境准备

1、需要会一点点Linux的基础；
2、CentOS7；
3、使用finalshell连接远程服务器进行操作；

个人在学习过程中在拥有root权限的zfx223服务器上操作，这样比较方便安装各种依赖环境等。环境查看如下所示：

```
# 系统内核是3.10以上的
[root@AIServer ~]# uname -r
3.10.0-957.el7.x86_64
[root@AIServer ~]# 
```

```
# 查看系统版本
[root@AIServer ~]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

> 安装

参考帮助文档，docs.docker.com

![](/images/2022-01-08-00-05-32.png)

```shell
# 1、 卸载旧的版本
[root@AIServer ~]# yum remove docker \
>                   docker-client \
>                   docker-client-latest \
>                   docker-common \
>                   docker-latest \
>                   docker-latest-logrotate \
>                   docker-logrotate \
>                   docker-engine
已加载插件：fastestmirror, langpacks
参数 docker 没有匹配
参数 docker-client 没有匹配
参数 docker-client-latest 没有匹配
参数 docker-common 没有匹配
参数 docker-latest 没有匹配
参数 docker-latest-logrotate 没有匹配
参数 docker-logrotate 没有匹配
参数 docker-engine 没有匹配
不删除任何软件包
```

```shell
# 2、 需要的安装包
[root@AIServer ~]# yum install -y yum-utils
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * centos-gluster7: mirrors.aliyun.com
 * centos-ovirt42: mirrors.aliyun.com
 * centos-ovirt43: mirrors.aliyun.com
 * centos-qemu-ev: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.huaweicloud.com
base                                                                                                                                                     | 3.6 kB  00:00:00     
centos-gluster7                                                                                                                                          | 3.0 kB  00:00:00     
centos-ovirt42                                                                                                                                           | 2.9 kB  00:00:00     
centos-ovirt43                                                                                                                                           | 2.9 kB  00:00:00     
centos-qemu-ev                                                                                                                                           | 3.0 kB  00:00:00     
centos-release-opstools                                                                                                                                  | 2.9 kB  00:00:00     
extras                                                                                                                                                   | 2.9 kB  00:00:00     
updates                                                                                                                                                  | 2.9 kB  00:00:00     
正在解决依赖关系
--> 正在检查事务
---> 软件包 yum-utils.noarch.0.1.1.31-50.el7 将被 升级
---> 软件包 yum-utils.noarch.0.1.1.31-54.el7_8 将被 更新
--> 解决依赖关系完成

依赖关系解决

================================================================================================================================================================================
 Package                                   架构                                   版本                                               源                                    大小
================================================================================================================================================================================
正在更新:
 yum-utils                                 noarch                                 1.1.31-54.el7_8                                    base                                 122 k

事务概要
================================================================================================================================================================================
升级  1 软件包

总计：122 k
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在更新    : yum-utils-1.1.31-54.el7_8.noarch                                                                                                                            1/2 
  清理        : yum-utils-1.1.31-50.el7.noarch                                                                                                                              2/2 
  验证中      : yum-utils-1.1.31-54.el7_8.noarch                                                                                                                            1/2 
  验证中      : yum-utils-1.1.31-50.el7.noarch                                                                                                                              2/2 

更新完毕:
  yum-utils.noarch 0:1.1.31-54.el7_8                                                                                                                                            

完毕！
```

```shell
3、 设置镜像的仓库（通过仓库的方法进行安装）

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo  # 默认是从国外下载的，十分慢

# 通过如下方式找到国内的
https://blog.csdn.net/lvdingding/article/details/112862396

yum-config-manager \
	--add-repo \
	http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo


[root@AIServer ~]# yum-config-manager \
> --add-repo \
> http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
已加载插件：fastestmirror, langpacks
adding repo from: http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
grabbing file http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo to /etc/yum.repos.d/docker-ce.repo
repo saved to /etc/yum.repos.d/docker-ce.repo
```

```shell
# 更新yum软件包索引，下述步骤中的fastestmirror还需要进一步学习
yum makecache 

[root@AIServer ~]# yum makecache fast
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * centos-gluster7: mirrors.aliyun.com
 * centos-ovirt42: mirrors.aliyun.com
 * centos-ovirt43: mirrors.aliyun.com
 * centos-qemu-ev: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.huaweicloud.com
base                                                                                                                                                     | 3.6 kB  00:00:00     
centos-gluster7                                                                                                                                          | 3.0 kB  00:00:00     
centos-ovirt42                                                                                                                                           | 2.9 kB  00:00:00     
centos-ovirt43                                                                                                                                           | 2.9 kB  00:00:00     
centos-qemu-ev                                                                                                                                           | 3.0 kB  00:00:00     
centos-release-opstools                                                                                                                                  | 2.9 kB  00:00:00     
docker-ce-stable                                                                                                                                         | 3.5 kB  00:00:00     
extras                                                                                                                                                   | 2.9 kB  00:00:00     
updates                                                                                                                                                  | 2.9 kB  00:00:00     
元数据缓存已建立
```


```shell
# 4、安装docker相关的 docker-ce社区版， ee企业版  安装的过程一路yes过去~
yum install docker-ce docker-ce-cli containerd.io
```

```shell
5、 启动docker
systemctl start docker
```

```shell
6、 使用docker version查看是否安装成功
[bupt_114@AIServer ~]$ docker version  # 查看是否安装成功
Client: Docker Engine - Community
 Version:           20.10.12
 API version:       1.41
 Go version:        go1.16.12
 Git commit:        e91ed57
 Built:             Mon Dec 13 11:45:41 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

......
```

```shell
7、 hello-world

docker run hello-world
```

![](/images/2022-01-08-00-06-12.png)


```shell
8、 查看一下下载的这个hello-world镜像
[root@AIServer ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   3 months ago   13.3kB
[root@AIServer ~]# 
```

```shell
了解，卸载docker

# 卸载依赖
sudo yum remove docker-ce docker-ce-cli containerd.io

# 删除资源
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

## Docker各个用户权限的设置，用户组

Reference: https://www.cnblogs.com/franson-2016/p/6412971.html

## 阿里云镜像加速

这是针对在阿里云上购买而言的，而且好像要收费？

1、登录阿里云，找到容器服务
2、找到镜像加速地址
3、配置使用


## 回顾HelloWorld流程

![](/images/2022-01-08-00-09-18.png)


## 底层原理

**Docker是怎么工作的**

Docker是一个Clinet-Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问

DockerServer接收到Docker-Client的指令，就会执行这个命令！

![](/images/2022-01-08-00-09-49.png)

**Docker为什么比VM快？**

1、Docker有着比虚拟机更少的抽象层。
2、Docker利用的是宿主机的内核，vm需要是Guest OS。

![](/images/2022-01-08-00-10-23.png)

所以说，新建一个容器的时候，docker不需要像虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机是加载Guest OS，分钟级别的，而Docker是利用宿主机的操作系统，省略了这个复杂的过程，秒级！

![](/images/2022-01-08-00-10-57.png)