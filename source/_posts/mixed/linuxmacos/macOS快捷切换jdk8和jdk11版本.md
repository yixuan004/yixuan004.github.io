---
title: macOS快捷切换jdk8和jdk11版本
date: 2023-07-18 10:28:19
tags: 
    - linuxmacos
categories:
	- develop
	- mixed
	- linuxmacos
---

需求：有的时候不同的项目需要用到不同的jdk版本，在idea中应该是直接指定上了的，但是如果是在shell窗口中执行如mvn clean install等命令的时候，这个时候的jdk环境又是没有配置的，所以需要提供一种灵活切换jdk版本的功能

<!--more-->

reference：

https://blog.csdn.net/zhuliliang/article/details/120231041

jdk8（可以配置到~/.zshrc中）

```
alias jdk8="export JAVA_HOME='/Users/curious/Library/Java/JavaVirtualMachines/azul-1.8.0_312/Contents/Home'"
```

jdk11（可以配置到~/.zshrc中）

```
alias jdk11="export JAVA_HOME='/Users/curious/Library/Java/JavaVirtualMachines/corretto-11.0.19/Contents/Home'"
```

在使用的时候，先jdk8，jdk11，然后切换了就可以使用了