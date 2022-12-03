---
title: Docker离线安装
date: 2022-11-25 16:49:06
tags:
    - docker
categories:
	- develop
	- docker
	- docker使用
---

背景：很多场景下我们需要对服务进行离线部署

一方面像是电信那边服务器的话，本身是内网环境不能连外网

另外一方面是有最近有一句让我触动很深的话，在我们部署服务的时候，要明白自己

<!--more-->

# Docker离线安装

## 1. Ubuntu20.04离线安装docker社区版

reference：https://zhuanlan.zhihu.com/p/458172588

### 1.1 下载离线包

这里以这几个为例，是相对新的版本，下载包后离线复制到计算机上：

- `containerd.io_1.4.12-1_amd64.deb`
- `docker-ce-cli_20.10.12~3-0~ubuntu-focal_amd64.deb`
- `docker-ce_20.10.12~3-0~ubuntu-focal_amd64.deb`

百度网盘地址：

链接: https://pan.baidu.com/s/1EpX_W_l3lPtXrXIoi_KPPg 提取码: 4xmc 

### 1.2 依次安装上述包，并添加docker用户组

```shell
sudo dpkg -i containerd.io_1.4.12-1_amd64.deb
sudo dpkg -i docker-ce-cli_20.10.12_3-0_ubuntu-focal_amd64.deb
sudo dpkg -i docker-ce_20.10.12_3-0_ubuntu-focal_amd64.deb
```

<font color='red'>注意，这里还需要一些docker组的配置相关，这里可能之后要学习一下</font> 

**安装docker同时，会自动创建docker组，但不会添加任何用户**

docker daemon绑定的是Unix socket，这就导致docker需要root权限才能使用，但这十分麻烦，因为其他用户必须经常使用sudo

为此，docker daemon创建unix socket的时候，会允许所有docker组内成员访问，所以我们只需要将用户加入docker组内，就可以避免使用sudo

这里的参数`-a`代表是在当前的基础上给加上这个用户组，而不是直接替换掉

```shell
sudo usermod -aG docker $USER
```

查看用户组`groups lyx`

```shell
(base) lyx@h9:~$ groups lyx
lyx : sudoer docker
```

#### 1.2.1 一次用户权限配置的解决

背景是lyx用户具有sudo权限，但是不在sudoers文件中。并且使用`groups lyx`查看，lyx用户属于sudoer和docker组中

解决，主要依靠的是`/etc/sudoers`、`/etc/passwd`、`/etc/groups`这几个文件

1）查看`/etc/passwd`文件，这里发现lyx用户的组标识号是1001，而实际上不应该是这样的，1001代表就加进root那边的组里了，修改成1003，代表还原回自己的这个组来：

> **注册名：口令：用户标识号：组标识号：用户名：用户主目录：命令解释程序** 

```shell
bupt@h9:~$ cat /etc/passwd
lyx:x:1003:1001:,,,:/home/lyx:/bin/bash
# 修改为下面这样
bupt@h9:~$ sudo vim /etc/passwd
lyx:x:1003:1003:,,,:/home/lyx:/bin/bash
```

2）修改1003后，再次groups查看，发现此时被归为其他用户的组里了

```shell
(base) lyx@h9:~$ groups lyx
lyx : yxy docker
```

查看`/etc/group`文件，发现这里对应的组标识号还是有对不上的情况，每个人没有对应到自己的用户组号上的感觉（不知道在安装的时候，这块是咋搞的）

```shell
yxy:x:1003:
lyx:x:1004:
```

3）这里把passwd那边和group这边的都修改成一个自己定义的2003（需要使用root用户）

```shell
# passwd
bupt@h9:~$ sudo vim /etc/passwd
lyx:x:2003:2003:,,,:/home/lyx:/bin/bash

bupt@h9:~$ sudo vim /etc/group
lyx:x:2003:
```

再次groups查看，发现可以，并且失去了root权限

```shell
(base) lyx@h9:~$ groups lyx
lyx : lyx docker

(base) lyx@h9:~$ sudo ls
[sudo] lyx 的密码： 
lyx 不在 sudoers 文件中。此事将被报告。
(base) lyx@h9:~$ 
```

4）后续再这样发现，已经创建的文件会有问题，包括一些目录的访问上来说，感觉还是有权限问题

再次修改`/etc/passwd`

```shell
# passwd
bupt@h9:~$ sudo vim /etc/passwd
lyx:x:1003:1003:,,,:/home/lyx:/bin/bash
```

### 1.3 验证docker的安装

```shell
(base) lyx@h9:~$ docker

Usage:  docker [OPTIONS] COMMAND

A self-sufficient runtime for containers

Options:
      --config string      Location of client config files (default "/home/lyx/.docker")
  -c, --context string     Name of the context to use to connect to the daemon (overrides DOCKER_HOST env var and default context set with "docker context use")
  -D, --debug              Enable debug mode
  -H, --host list          Daemon socket(s) to connect to
  -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/home/lyx/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/home/lyx/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/home/lyx/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit
...
To get more help with docker, check out our guides at https://docs.docker.com/go/guides/
```

### 1.4 卸载docker

```shell
sudo apt-get purge docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

## 2. Ubuntu20.04服务器安装docker-compose

### 2.1 安装（应该是需要root）

- 离线版本

在github的release上下载匹配系统环境的docker-compose

docker-compose-linux-x86_64，百度网盘地址：链接: https://pan.baidu.com/s/1RQmVZg5-tqX8OdFWQASdyA 提取码: 53j4 

重命名后，直接传到这个路径`/usr/local/bin/docker-compose`

- 在线版本

`curl -L https://github.com/docker/compose/releases/download/1.24.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose`

- 修改权限

上述完成后，修改权限：`chmod +x /usr/local/bin/docker-compose`

### 2.2 验证

```shell
(base) lyx@h9:~$ docker-compose --version
docker-compose version 1.24.0, build 0aa59064
```

### 2.3 卸载

```shell
sudo rm /usr/local/bin/docker-compose
```

## 2. CentOS 7离线安装docker

TODO