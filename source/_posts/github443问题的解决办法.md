---
title: github443问题的解决办法
date: 2021-12-06 00:56:27
tags:
    - macOS基础操作
categories:
	- Crsenal
---

背景：github经常遇到443问题，例如Failed to connect to github.com port 443: Operation timed out，这种情况下需要一些解决办法；

Reference：
> https://www.jianshu.com/p/471aeba64724

<!--more-->

# 解决方法一（暂未尝试，但是从原理上来说应该是可以解决的）

1. 查询可以用的IP，在

> https://www.ipaddress.com/

上分别搜索```github.com、github.global-ssl.fastly.net```

2. 修改host文件

操作：```sudo vi /etc/hosts``` 替换成以下ip即可

```
151.101.185.194 github.global-ssl.fastly.net
192.30.253.112  github.com
```

之后就可以进行一些尝试了


# 解决方法二（尝试了可以，需要梯子资源）

1. 设置代理的方法，首先查找梯子代理的端口：

![](/images/2021-12-06-11-45-05.png)

通过git config命令来设置
```shell
git config --global http.proxy "localhost:port"
```

2. 在使用完毕后可以改回来

```shell
git config --global --unset http.proxy
```