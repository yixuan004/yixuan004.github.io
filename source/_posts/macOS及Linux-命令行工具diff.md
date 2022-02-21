---
title: macOS及Linux-命令行工具diff
date: 2021-09-16 20:22:05
tags:
    - macOS基础操作
categories:
	- Crsenal
---

需求：如果使用beyond compare等工具做diff还是有时候有些不方便的，或者说不够灵活。可以在macOS或者Linux系统中直接使用语句进行diff操作，并且在这里补充了一些关于zsh配置和colordiff的说明。

<!--more-->


# 1. 流程和操作说明

在目录下执行如下语句即可进行简单的diff操作
```shell
$ diff -u [file A] [file B]
```

# 2. colordiff及zsh相关配置

在目前使用的苹果电脑环境下，通过如上语句显示diff没有高亮不方便阅读，故通过如下命令安装colordiff
```shell
$ brew install colordiff
```

安装完成后，可以在任意一个zsh窗口下执行命令进行带颜色的diff操作：
```shell
$ colordiff -u [file A] [file B]
```

原来的diff已经不用了，可以指定diff直接到colordiff，在当前zsh窗口下执行如下命令，即可在当前窗口完成diff代替colordiff的操作（注：可以使用文件查看）
```shell
$ alias diff=colordiff
```

如果要每次都生效，需要执行如下操作：
```shell
$ vim ~/.zshrc

在最后一行加入
alias diff=colordiff
```
![](/images/2021-09-16-23-42-12.png)


# 3. 效果展示

![](/images/2021-09-16-23-49-33.png)



