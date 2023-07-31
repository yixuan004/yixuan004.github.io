---
title: SpringBoot-13使用JMeter进行接口压力测试
date: 2023-06-25 22:41:39
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot-13使用JMeter进行接口压力测试

可以用来进行接口的压力测试，官网：https://jmeter.apache.org/

Apache JMeter features include:

- Ability to load and performance test many different applications/server/protocol types:
  - Web - HTTP, HTTPS (Java, NodeJS, PHP, ASP.NET, …)
  - SOAP / REST Webservices
  - FTP
  - Database via JDBC
  - LDAP
  - Message-oriented middleware (MOM) via JMS
  - Mail - SMTP(S), POP3(S) and IMAP(S)
  - Native commands or shell scripts
  - TCP
  - Java Objects

## 1. 下载及打开

在官网左侧[Download Releases](https://jmeter.apache.org/download_jmeter.cgi)即可下载，macOS和Linux操作系统下载tgz那个包，下载后目录如下，JMeter基于java开发：

```
.
├── LICENSE
├── NOTICE
├── README.md
├── bin
├── docs
├── extras
├── lib
├── licenses
└── printable_docs
```

启动使用`bin/jmeter`这个Unix可执行文件，启动后是这个界面

![image-20230625230516674](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625230516674.png)

在Options-Language里面选择简体中文可以切换到中文模式

![image-20230625230718097](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625230718097.png)

## 2. 使用JMeter进行接口压力测试

压测测的是并发，并发不是一种特别标准的描述，最准确的说法是并发达到多少的时候，QPS/TPS是多少

QPS：Queries Per Second，每秒的查询率

TPS：Transaction Per Second，每秒的事务

压测接口：localhost:5001/namefinder/match

![image-20230625232133078](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625232133078.png)

![image-20230625232253051](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625232253051.png)

![image-20230625232320198](../../../../../../../Library/Application Support/typora-user-images/image-20230625232320198.png)

![image-20230625232743628](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625232743628.png)

![image-20230625232510042](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625232510042.png)

![image-20230625232758173](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625232758173.png)

![image-20230625232824541](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625232824541.png)

![image-20230625232855287](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625232855287.png)

![image-20230625232931191](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625232931191.png)

![image-20230625232952174](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230625232952174.png)







{"timestamp":"2023-06-25T15:34:51.166+00:00","status":415,"error":"Unsupported Media Type","path":"/namefinder/match"}



































