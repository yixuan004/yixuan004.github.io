---
title: linux环境相关及命令分类总结
date: 2022-11-25 00:38:55
tags:
    - linux
categories:
	- develop
	- linux
---



<!--more-->


# linux环境相关及命令分类总结

## 1. linux环境相关

### 1.1 环境变量（软链接）

全局的环境变量路径在`/etc/profile`这个文件

对于每个用户的环境变量，在`~/.bashrc`这个文件

在修改环境变量后，需要使用`source`命令，使修改后的文件立即生效

如将python3使用用户进行安装，安装在`/home/bupt_114/lyx_python3`这个目录下，那么可以把环境变量设置成：

```shell
export PATH=$PATH:/home/bupt_114/lyx_python3/bin
export PATH=$PATH:/home/bupt_114/lyx_python3/lib
```

此外，软链接（符号链接）也是一种方式，而且似乎是更合理的方式<font color='red'>但是还不知道针对非root用户的软链接链接到哪，如果是root的话，应该是软连接到/usr/bin/这个地方</font> 

## 2. Linux命令总结

### 2.1 基础命令

- `pwd`：查看当前路径

- `ls`：查看目录下的文件
  - `ls -l/ll`：以长格式的形式查看当前目录下所有可见文件的详细属性

- `cp`：拷贝文件，`cp [-r] source_file dst_file`

- `mv`：移动文件（同时可以起到重命名的作用），`mv source_file dst_file`

- `rm -rf`：删除文件，注意这里删除了文件就找不回来了



### 2.2 服务器间文件传输——`scp -r [NAME]@[IP]:[PATH] `

示例：`scp -r bupt_114@10.109.246.188:/home/bupt_114/`



### 2.3 权限相关（文件/用户）

#### 2.3.1 修改单个文件的用户权限——`chown [USER]:[USER] [FILE]`

示例：`chown bupt_114:bupt_114 test.txt`

常用场景：在服务器之间传输文件的时候，可能习惯首先传到root目录下，或者是root用户和其他用户的文件传输，

#### 2.3.2 修改读写权限——`chmod -R [MODE_NUM] [FILE/DIR]`

示例：`chmod -R 777 test/`

-R：处理指定目录以及其子目录下的所有文件

777：可读、可写、可执行

#### 2.3.3 查看当前用户——`whoami/id -un/who -H`

```shell
(base) lyx@h9:~$ whoami
lyx

(base) lyx@h9:~$ id -un
lyx

(base) lyx@h9:~$ who -H  # 查看当前登陆的所有用户
名称   线路       时间           备注
lyx      pts/12       2022-11-26 11:33 (10.28.232.177)
lyx      pts/13       2022-11-26 11:56 (10.28.232.177)
lyx      pts/9        2022-11-26 11:33 (10.28.232.177)
lyx      pts/17       2022-11-26 11:56 (10.28.232.177)
```

#### 2.3.4 查看当前用户所属的组——`groups [NAME]|||cat /etc/group`

```shell
(base) lyx@h9:~$ groups lyx
lyx : sudoer docker
```

在linux中，每个用户都有一个用户组，用户组其实就是一组用户的集合，这些用户共享着所在用户组中的一些资源和权限，但是各自又拥有各自的私有资源

这就和家庭一样，你的兄弟姐妹（不同的用户）属于同一个家庭（用户组）。你们共同享有这个家庭（共享资源），无论是谁有困难父母都会帮助你们（共享权限），但是你私有的，比如日记（私有资源），未经你允许其他人是无权查看的（私有权限）。

一个用户是可以属于多个用户组的，就好像既属于家庭，又属于学校一样

```shell
(base) lyx@h9:~$ cat /etc/group
...
sudoer:x:1001:
zkh:x:1002:
yxy:x:1003:
lyx:x:1004:
docker:x:998:lyx
...
```

#### 2.3.5 将普通用户添加到sudoers管理员组（sudo相关）

使用root用户进行visudo（一种方式）

```shell
[root@svr6 /]# visudo 
## Allow root to run any commands anywhere 
root ALL=(ALL) ALL 
bupt_114 ALL=(ALL) ALL
```

另外一种方式，通过passwd那边的路径

> **注册名：口令：用户标识号：组标识号：用户名：用户主目录：命令解释程序** 

```shell
bupt@h9:~$ cat /etc/passwd
lyx:x:1003:1001:,,,:/home/lyx:/bin/bash
# 修改为下面这样
bupt@h9:~$ sudo vim /etc/passwd
lyx:x:1003:1003:,,,:/home/lyx:/bin/bash
```

再次登录lyx用户， 发现sudoers权限被取消了

```shell
(base) lyx@h9:~$ sudo ls
[sudo] lyx 的密码： 
lyx 不在 sudoers 文件中。此事将被报告。
```

要检查用户的sudo访问权限，请运行以下命令:

假设用户名为lyx

```shell
# 具有sudo权限时候
(base) lyx@h9:~$ sudo -l -U lyx
匹配 %2$s 上 %1$s 的默认条目：
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

用户 lyx 可以在 h9 上运行以下命令：
    (ALL) ALL
(base) lyx@h9:~$

# 不具有sudo权限的时候
bupt@h9:~$ sudo -l -U lyx
用户 lyx 无权在 h9 上运行 sudo。
```

#### 2.3.6 一次用户权限配置的解决

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

#### 2.3.7 添加/删除用户——`sudo userdel [NAME]/sudo useradd [NAME]`

- 通过这样的useradd命令，进行用户添加，会自动在`/home/`目录下创建属于这个用户的一个目录

```shell
sudo useradd [NAME]
```

- userdel命令会删除用户（但好像这个用户的文件夹不会被已删除）

```shell
```





### 2.4 进程查看相关

#### 2.4.1 通过进程id/程序名称查看——`ps [-ef/-aux] | grep [ID/NAME]`

示例，查看包含有python的进程：`ps -ef | grep python`

```shell
(base) lyx@h9:~$ ps -ef | grep python
root        1113       1  0 10月11 ?      00:00:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
root        1231       1  0 10月11 ?      00:00:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
yxy        86552   86537  0 11月04 ?      00:11:07 python /home/yxy/.vscode-server/extensions/ms-python.python-2022.18.0/pythonFiles/get_output_via_markers.py /home/yxy/.vscode-server/extensions/ms-python.python-2022.18.0/pythonFiles/tensorboard_launcher.py /data/sdb1/yxy/event_extraction/exp/2022-11-04/FewFC_paie_bartLarge_20221104161231/tensorboard
...
```

示例，通过进程号查看进程：`ps -ef | grep 86552`

```shell
(base) lyx@h9:~$ ps -ef | grep 86552
yxy        86552   86537  0 11月04 ?      00:11:07 python /home/yxy/.vscode-server/extensions/ms-python.python-2022.18.0/pythonFiles/get_output_via_markers.py /home/yxy/.vscode-server/extensions/ms-python.python-2022.18.0/pythonFiles/tensorboard_launcher.py /data/sdb1/yxy/event_extraction/exp/2022-11-04/FewFC_paie_bartLarge_20221104161231/tensorboard
yxy        86636   86552  0 11月04 ?      02:12:53 /data/sdb1/yxy/miniconda3/envs/torch/lib/python3.8/site-packages/tensorboard_data_server/bin/server --logdir=/data/sdb1/yxy/event_extraction/exp/2022-11-04/FewFC_paie_bartLarge_20221104161231/tensorboard --reload=5 --samples-per-plugin= --port=0 --port-file=/tmp/tensorboard_data_server_ho53fawj/port --die-after-stdin --error-file=/tmp/tensorboard_data_server_ho53fawj/startup_error
yxy        86723   86552  0 11月04 ?      00:00:00 /data/sdb1/yxy/miniconda3/envs/torch/bin/python -c from multiprocessing.resource_tracker import main;main(8)
yxy        86730   86552  0 11月04 ?      00:02:08 /data/sdb1/yxy/miniconda3/envs/torch/bin/python -c from multiprocessing.spawn import spawn_main; spawn_main(tracker_fd=9, pipe_handle=14) --multiprocessing-fork
lyx      3069868 3066086  0 14:45 pts/17   00:00:00 grep --color=auto 86552
```

示例，使用`-aux`

```shell
(base) lyx@h9:~$ ps -aux | grep python
root        1113  0.0  0.0  42764  7368 ?        Ss   10月11   0:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
root        1231  0.0  0.0 121216  4280 ?        Ssl  10月11   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
yxy        86552  0.0  0.0 2347560 89332 ?       Sl   11月04  11:09 python /home/yxy/.vscode-server/extensions/ms-python.python-2022.18.0/pythonFiles/get_output_via_markers.py /home/yxy/.vscode-server/extensions/ms-python.python-2022.18.0/pythonFiles/tensorboard_launcher.py /data/sdb1/yxy/event_extraction/exp/2022-11-04/FewFC_paie_bartLarge_20221104161231/tensorboard
```

`-ef`与`-aux`的参数解释：

- `-e`列出全部的进程；`-f`显示全部的列（UID，PID，PPID，StartTime，Time，CMD）

- `-aux`

```shell
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
USER: 行程拥有者
PID: pid
%CPU: 占用的 CPU 使用率
%MEM: 占用的记忆体使用率
VSZ: 占用的虚拟记忆体大小
RSS: 占用的记忆体大小
TTY: 终端的次要装置号码 (minor device number of tty)
STAT: 该行程的状态
	linux的进程有5种状态：
		D 不可中断 uninterruptible sleep (usually IO)
		R 运行 runnable (on run queue)
		S 中断 sleeping
		T 停止 traced or stopped
		Z 僵死 a defunct (”zombie”) process
```

#### 2.4.2 top命令













