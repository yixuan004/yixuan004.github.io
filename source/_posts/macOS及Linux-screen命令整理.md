---
title: macOS及Linux-screen命令整理
date: 2021-12-08 14:48:35
tags:
    - macOS基础操作
categories:
	- Crsenal
---

需求：例如在跑深度学习等程序的时候，需要让程序挂载在后台执行，这时候就可以使用screen命令进行后台的挂载

<!--more-->

# screen -ls

查看：```screen -ls```可以查看当前有哪些Attached/Detached的screen界面

```shell
(base) lyx@h5:~$ screen -ls
There is a screen on:
        12442.xxxxxxx       (12/08/2021 02:12:53 PM)        (Detached)
1 Socket in /var/run/screen/S-lyx.=
```

# screen -S [name]

新建：```screen -S [name]```可以打开一个新的名称为[name]的screen界面

# screen -r [name]

返回：```screen -r [name]```可以返回名称为[name]的screen界面（例如之前暂时退出了，可通过此命令返回），名称可通过```screen -ls```查看

# screen -X -S [id] quit

删除：```screen -X -S [id] quit```可以关闭挂载的一些screen，这里只用输入id号即可

# screen -D [name]

有些情况下，如果finalshell等ssh窗口已经关闭，但是还显示在Attached的状态，就需要先D掉再进去

```screen -D [name]``



