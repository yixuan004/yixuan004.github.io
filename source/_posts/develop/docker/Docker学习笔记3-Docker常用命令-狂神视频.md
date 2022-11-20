---
title: Docker学习笔记3-Docker常用命令-狂神视频
date: 2022-01-08 00:14:06
categories:
    - docker
---

Docker的常用命令，主要分为镜像和容器两个部分，也是Docker学习的基础

这里的命令如果对基础linux有了解，或者对screen系列命令有了解感觉还是很熟悉的，就是感觉要多用或者结合项目才能真正的记住

<!--more-->

# Docker的常用命令

## 帮助命令

```shell
docker version  	# 显示docker的版本信息
docker info  		# 显示docker更加详细的信息，sys级别系统信息，包括镜像和容器的数量等
docker 命令 --help  	# 万能命令，帮助命令
docker --help
```

帮助文档的地址：
docs.docker.com里有一个Reference

docs.docker.com/engine/reference/commandline

## 镜像命令

**docker images** 查看所有本地主机上的镜像
```shell
[bupt_114@AIServer ~]$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   3 months ago   13.3kB

# 解释
REPOSITORY	镜像的仓库源
TAG			镜像的标签
IMAGE ID    镜像的ID
CREATED		镜像的创建时间
SIZE		镜像的大小

# 可选项，可以docker image -aq
Options:
  -a, --all             Show all images (default hides intermediate images) 列出所有镜像
  -q, --quiet           Only show image IDs 只显示镜像的id
```

**docker search 搜索镜像**
```shell
[bupt_114@AIServer ~]$ docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   11912     [OK]       
mariadb                           MariaDB Server is a high performing open sou…   4558      [OK]       
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   895                  [OK]
phpmyadmin                        phpMyAdmin - A web interface for MySQL and M…   415       [OK]
......


# 可选项，通过收藏来过滤
Options:
--filter=STARS=3000  # 搜索出来的镜像就是STARS大于3000的

[bupt_114@AIServer ~]$ docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   11912     [OK]       
mariadb   MariaDB Server is a high performing open sou…   4558      [OK]
```

**docker pull 下载镜像**
```shell
# 下载镜像 docker pull 镜像名[:tag]
[bupt_114@AIServer ~]$ docker pull mysql
Using default tag: latest  					# 如果不写tag，默认就是latest
latest: Pulling from library/mysql          # 
72a69066d2fe: Pull complete 				# 分层下载，docker image的核心 联合文件系统
93619dbc5b36: Pull complete 
99da31dd6142: Pull complete 
626033c43d70: Pull complete 
37d5d7efb64e: Pull complete 
ac563158d721: Pull complete 
d2ba16033dad: Pull complete 
688ba7d5c01a: Pull complete 
00e060b6d11d: Pull complete 
1c04857f594f: Pull complete 
4d7cfa90e6ea: Pull complete 
e0431212d27d: Pull complete 
Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709		# 签名，防伪标志
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest		# 真实地址


# 等价
docker pull mysql 和 docker pull docker.io/library/mysql:latest是等价的


# 指定版本下载
[bupt_114@AIServer ~]$ docker pull mysql:5.7  # 注意版本一定要在官方文档里有
【docker笔记3图2】

[bupt_114@AIServer ~]$ docker pull mysql:5.7
5.7: Pulling from library/mysql
72a69066d2fe: Already exists 
93619dbc5b36: Already exists 
99da31dd6142: Already exists 
626033c43d70: Already exists 
37d5d7efb64e: Already exists 
ac563158d721: Already exists 
d2ba16033dad: Already exists 
0ceb82207cd7: Pull complete 
37f2405cae96: Pull complete 
e2482e017e53: Pull complete 
70deed891d42: Pull complete 
Digest: sha256:f2ad209efe9c67104167fc609cca6973c8422939491c9345270175a300419f94
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

**docker rmi 删除镜像**
（既可以通过镜像的ID来删除，也可以通过镜像的名称来删除，涉及到删除的指令都非常危险啊）
```shell
[bupt_114@AIServer ~]$ docker rmi -f c20987f18b13
Untagged: mysql:5.7
Untagged: mysql@sha256:f2ad209efe9c67104167fc609cca6973c8422939491c9345270175a300419f94
Deleted: sha256:c20987f18b130f9d144c9828df630417e2a9523148930dc3963e9d0dab302a76
Deleted: sha256:6567396b065ee734fb2dbb80c8923324a778426dfd01969f091f1ab2d52c7989
Deleted: sha256:0910f12649d514b471f1583a16f672ab67e3d29d9833a15dc2df50dd5536e40f
Deleted: sha256:6682af2fb40555c448b84711c7302d0f86fc716bbe9c7dc7dbd739ef9d757150
Deleted: sha256:5c062c3ac20f576d24454e74781511a5f96739f289edaadf2de934d06e910b92


# 批量删除所有的
[bupt_114@AIServer ~]$ docker rmi -f $(docker images -aq)  # 删除全部的容器
```

```shell
[bupt_114@AIServer ~]$ docker rmi -f 容器id  			   	# 删除指定的容器
[bupt_114@AIServer ~]$ docker rmi -f 容器id 容器id 容器id   	# 删除多个容器
[bupt_114@AIServer ~]$ docker rmi -f $(docker images -aq)  	# 删除全部的容器
```

## 容器命令

**说明：我们有了镜像才可以创建容器，linux，下载一个centos镜像来测试学习**

在docker里面再装一个虚拟机！？

```shell
docker pull centos
```

**新建容器并启动**
```shell
docker run [可选参数] image

# 参数说明
--name="Name"		# 容器名字，跑起来要知道叫什么，tomcat01, tomcat02，用来区分容器
-d    				# 后台方式运行
-it                 # 使用交互方式运行，进入容器查看内容
-p   				# 指定容器的端口 -p 8080:8080，可以和主机映射起来也
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口（常用）
	-p 容器端口
	容器端口
-P                  # 大P，随机指定端口


# 测试，启动并进入容器
[bupt_114@AIServer ~]$ docker run -it centos /bin/bash
[root@364710ff50c7 /]# 
[root@364710ff50c7 /]# ls    						# 查看容器内部的centos，基础版本，很多命令都是不完善的
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var


# 从容器中退回主机
[root@364710ff50c7 /]# exit
[bupt_114@AIServer ~]$ 
```

**列出所有运行中的容器**
```shell
# docker ps 命令
		# 列出当前正在运行的容器
-a 		# 列出当前正在运行的容器 + 带出历史运行过的容器
-n=?    # 显示最近创建的容器，即个数
-q 		# 只显示容器的编号


[bupt_114@AIServer ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[bupt_114@AIServer ~]$ docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                          PORTS     NAMES
364710ff50c7   centos         "/bin/bash"   5 minutes ago   Exited (0) About a minute ago             elated_lichterman
93427549984f   feb5d9fea6a5   "/hello"      29 hours ago    Exited (0) 29 hours ago                   exciting_villani
[bupt_114@AIServer ~]$ docker ps -a -n=1
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                     PORTS     NAMES
364710ff50c7   centos    "/bin/bash"   7 minutes ago   Exited (0) 3 minutes ago             elated_lichterman
[bupt_114@AIServer ~]$ 
```
**退出容器**
```shell
exit 			# 直接停止容器并退出
ctrl + P + Q   	# 容器不停止并退出
```


**删除容器**
```shell
docker rm 容器id					# 删除指定的容器，不能删除正在运行的容器，如果要强制删除，rm -f
docker rm -f $(docker ps -aq)   # 删除所有的容器
docker ps -a -q | xargs docker rm  # 删除所有的容器（docker玩的不错跑路就玩得不错哈哈哈哈）

# 运行中的不能直接删除，要加-f代表force
[bupt_114@AIServer ~]$ docker rm f406a8591325
Error response from daemon: You cannot remove a running container f406a8591325995950d4defb6d7eefbe45bd5ec11e895f7238ce53de4546c297. Stop the container before attempting removal or force remove
```

**启动和停止容器的操作**
```shell
docker start 容器id			# 启动容器
docker attach 容器id			# 进入容器
docker restart 容器id		# 重启容器
docker stop 容器id			# 停止当前正在运行的容器
docker kill 容器id			# 强制定制当前正在运行的容器

[bupt_114@AIServer ~]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                     PORTS     NAMES
87ac7567be74   centos    "/bin/bash"   9 seconds ago   Exited (0) 6 seconds ago             determined_cohen
[bupt_114@AIServer ~]$ docker start 87ac7567be74
87ac7567be74

[bupt_114@AIServer ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS         PORTS     NAMES
87ac7567be74   centos    "/bin/bash"   24 seconds ago   Up 4 seconds             determined_cohen
[bupt_114@AIServer ~]$ docker stop 87ac7567be74
87ac7567be74
[bupt_114@AIServer ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[bupt_114@AIServer ~]$ 
```

## 常用的其他命令


**后台启动容器**
```shell
# 命令，docker run -d 镜像名！
[bupt_114@AIServer ~]$ docker run -d centos
0aab13a3c396931520159092a755ccc7eb1a2fc56895ab2e2cc43267e86be432

# 问题，docker ps，发现centos停止了
[bupt_114@AIServer ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[bupt_114@AIServer ~]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                      PORTS     NAMES
0aab13a3c396   centos    "/bin/bash"   12 seconds ago   Exited (0) 11 seconds ago             exciting_bose

# 常见的坑：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止，没有程序了
# 这块有点没理解，往后学习再看了
[bupt_114@AIServer ~]$ 
```

**查看日志**
```shell
docker logs

[bupt_114@AIServer ~]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                      PORTS     NAMES
0aab13a3c396   centos    "/bin/bash"   15 minutes ago   Exited (0) 15 minutes ago             exciting_bose
[bupt_114@AIServer ~]$ docker logs -tf --tail 10 0aab13a3c396


# 显示日志
-tf  				# 显示日志，f是实时追踪，滚动查看
--tail number		# 要显示的日志条数
```


**查看容器中的进程信息** ps用来查linux的进程信息
```shell
# 注意，要容器正在运行才能查的出来

docker top [容器id]

[bupt_114@AIServer ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
8d05d658639d   centos    "/bin/bash"   15 seconds ago   Up 14 seconds             pensive_dirac
[bupt_114@AIServer ~]$ docker top 8d05d658639d
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                11758               11735               0                   21:37               pts/0               00:00:00            /bin/bash
[bupt_114@AIServer ~]$ 
```


**查看镜像的元数据**

```shell
[bupt_114@AIServer ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS              PORTS     NAMES
8d05d658639d   centos    "/bin/bash"   About a minute ago   Up About a minute             pensive_dirac
[bupt_114@AIServer ~]$ docker inspect 8d05d658639d
[
    {
        "Id": "8d05d658639d9dbc015cfef44b7390bc4cab2b658d646f0933defaa4ee2a8732",
        "Created": "2022-01-07T13:37:12.953676774Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 11758,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-01-07T13:37:13.28978368Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/8d05d658639d9dbc015cfef44b7390bc4cab2b658d646f0933defaa4ee2a8732/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/8d05d658639d9dbc015cfef44b7390bc4cab2b658d646f0933defaa4ee2a8732/hostname",
        "HostsPath": "/var/lib/docker/containers/8d05d658639d9dbc015cfef44b7390bc4cab2b658d646f0933defaa4ee2a8732/hosts",
        "LogPath": "/var/lib/docker/containers/8d05d658639d9dbc015cfef44b7390bc4cab2b658d646f0933defaa4ee2a8732/8d05d658639d9dbc015cfef44b7390bc4cab2b658d646f0933defaa4ee2a8732-json.log",
        "Name": "/pensive_dirac",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/58cabfbd05b381fae72981c01524435bc9231561f1a492c32b9a4e402522db74-init/diff:/var/lib/docker/overlay2/70a302960300b9000282549b5adf25e9a4bd66f8e9c50f12844d5d156ea2c712/diff",
                "MergedDir": "/var/lib/docker/overlay2/58cabfbd05b381fae72981c01524435bc9231561f1a492c32b9a4e402522db74/merged",
                "UpperDir": "/var/lib/docker/overlay2/58cabfbd05b381fae72981c01524435bc9231561f1a492c32b9a4e402522db74/diff",
                "WorkDir": "/var/lib/docker/overlay2/58cabfbd05b381fae72981c01524435bc9231561f1a492c32b9a4e402522db74/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "8d05d658639d",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "e1251439f6970af7a132751b9c2f1bc9bcd997589ed16c1824047626a5c34153",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/e1251439f697",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "f95da790cb9492af2410a35ee110943d1af0f393ab5ec266071cb40f4621525a",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "46238680691e2ebb3e190ccf96fb7814787a55c663b4585dae38cd8e3abc4070",
                    "EndpointID": "f95da790cb9492af2410a35ee110943d1af0f393ab5ec266071cb40f4621525a",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
[bupt_114@AIServer ~]$ 
```


**进入当前正在运行的容器**

```shell
# 我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

# 命令1
docker exec -it 容器id bashShell

[bupt_114@AIServer ~]$ docker exec -it 8d05d658639d /bin/bash
[root@8d05d658639d /]# 



# 命令2，注意进入的是一个正在进行的命令行！！！！而不是开启一个新的窗口
docker attach 容器id

[bupt_114@AIServer ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
8d05d658639d   centos    "/bin/bash"   8 minutes ago   Up 8 minutes             pensive_dirac
[bupt_114@AIServer ~]$ docker attach 8d05d658639d
[root@8d05d658639d /]# ps -ef   
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 13:37 pts/0    00:00:00 /bin/bash
root        30     1  0 13:45 pts/0    00:00:00 ps -ef
[root@8d05d658639d /]# 


# docker exec 		# 进入容器后开启一个新的终端，可以在里面操作
# dockerattach 		# 进入容器正在执行的终端，不会启动新的进程！
```


**从容器内拷贝文件到主机** 注：从主机内拷到容器内一般用挂载来做！

容器内外是隔离的

```shell
docker cp 容器id:容器内路径 目的的主机路径

# 拷贝使用一个手动过程，未来我们使用 -v 卷的技术，可以实现
```

学习方式：命令全部跟着敲一遍，自己记录笔记


## docker命令小结

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-01-08-00-24-45.png)

docker的命令是十分多的，上边是常见的容器和镜像命令