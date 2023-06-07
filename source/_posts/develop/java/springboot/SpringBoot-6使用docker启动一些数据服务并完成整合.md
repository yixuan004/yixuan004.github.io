---
title: SpringBoot-6使用docker启动一些数据服务并完成整合
date: 2023-04-10 23:14:35
tags:
---

# SpringBoot-6使用docker启动一些数据服务并完成整合

mysql、redis、elasticsearch，都去使用h9上的服务？

## 1. mysql

### 1.1 使用SpringBoot整合MyBatisPlus完成标准Dao开发

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

```yaml
spring:
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://10.109.246.188:3306/icoe_sbai
    username: root
    password: vpc-8vbd
```

3. 数据库表结构

```sql
CREATE TABLE IF NOT EXISTS `icoe_contacts` (
   `id` bigint NOT NULL AUTO_INCREMENT,
   `corpid` varchar(8) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `dep` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `per` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `extension` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `directline` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `mandarin_per` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `mandarin_strip_per` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `mandarin_dep` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `mandarin_strip_dep` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `mandarin_set` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `mandarin` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    `mandarin_strip` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
    PRIMARY KEY (`id`,`corpid`,`dep`,`per`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
```









## 2. redis
