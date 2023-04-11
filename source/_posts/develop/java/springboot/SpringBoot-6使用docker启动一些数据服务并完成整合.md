---
title: SpringBoot-6使用docker启动一些数据服务并完成整合
date: 2023-04-10 23:14:35
tags:
---

# SpringBoot-6使用docker启动一些数据服务并完成整合

mysql、redis、elasticsearch，都去使用h9上的服务？

## 1. mysql

### 1.1 docker拉取镜像（m1架构没有这个）

```shell
(base) ➜ /Users/curious ✻ docker pull mysql:5.7                      [23:13:44]
5.7: Pulling from library/mysql
no matching manifest for linux/arm64/v8 in the manifest list entries
```

### 1.2 使用SpringBoot整合MyBatisPlus完成标准Dao开发

https://www.bilibili.com/video/BV12T4y1B7C3/?spm_id_from=333.337.search-card.all.click&vd_source=2654875159ed89b917fffce42a65196d，直接去连h9服务器的

1. 导入MyBatisPlus的依赖pom.xml，还需要一个Druid

```xml
<!--mybatisplus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.2</version>
</dependency>

<!--druid-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.16</version>
</dependency>
```

2. 在application.yml中写内容









## 2. redis
