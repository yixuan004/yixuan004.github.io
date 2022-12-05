---
title: github-GnuTLS-recv-error
date: 2022-12-03 20:58:04
tags:
    - git
categories:
	- Develop
	- mixed
	- git
---

# github-GnuTLS-recv-error

报错的完整内容是：

```shell
GnuTLS recv error (-110): The TLS connection was non-properly terminated
```

搜索到的解决办法详见这篇博客：https://blog.csdn.net/joey_ada/article/details/123580592，依次执行以下代码，然后再执行git相关的操作

```shell
export GIT_TRACE_PACKET=1
export GIT_TRACE=1
export GIT_CURL_VERBOSE=1
```

这个后期加入`~/.bashrc`内容中，然后完成`source ~/.bashrc`，就可以完成环境变量了