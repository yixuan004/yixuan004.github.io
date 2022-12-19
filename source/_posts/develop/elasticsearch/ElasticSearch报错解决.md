---
title: ElasticSearch报错解决
date: 2022-12-02 11:04:18
tags:
	- elasticsearch
categories:
	- develop
	- elasticsearch
---

和ElasticSearch相关的报错解决以后都将陆续写在这个博客中

<!--more-->

# ElasticSearch报错解决

## 1. ES本身的报错与解决

### 1.1 启动ES时候报错max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

### 1.1.1 报错解决

这个报错首先是发现在使用`./bin/elasticsearch -d`启动es的时候，调用es会报错，然后再次`ps -ef | grep elasticsearch`进行查看，发现es的进程没了

然后不加`-d`的参数，直接前台启动es，发现会有这个报错，解决方法参考了这篇博客：https://blog.csdn.net/xcc_2269861428/article/details/100186654

使用root用户，在`/etc/sysctl.conf`文件最后添加一行

```shell
vm.max_map_count=262144
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221202114008577.png" alt="image-20221202114008577" style="zoom:50%;" />

使用root用户（可能需要sudo），执行`/sbin/sysctl -p `立即生效

```shell
bupt@h9:~$ /sbin/sysctl -p
sysctl: permission denied on key "vm.max_map_count", ignoring
bupt@h9:~$ sudo /sbin/sysctl -p
[sudo] bupt 的密码： 
vm.max_map_count = 262144
```

再次启动，发现报错消失，可以正常挂载在后台

#### 1.1.2 报错背后的原因分析

系统虚拟内存默认最大映射数为65530，无法满足es要求，需要调整为262144以上，设置成这个数目就可以了

## 2. 在调用ES服务时候产生的报错与解决

