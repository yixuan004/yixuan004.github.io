---
title: SpringBoot-6整合MyBatisPlus完成标准Dao开发
date: 2023-04-10 23:14:35
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot-6整合MyBatisPlus完成标准Dao开发

直接使用h9服务器上的mysql

reference：

https://www.bilibili.com/video/BV12T4y1B7C3/?spm_id_from=333.337.search-card.all.click&vd_source=2654875159ed89b917fffce42a65196d

## 1. 坐标和外部依赖配置

1. 导入MyBatisPlus的依赖pom.xml，并且导入Druid的坐标

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

2. 在application.yml中写内容，配置数据库的连接

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
    password: ********
```

## 2. 数据库初始化

一般有个需求，就是说项目在冷启动的之后，自动执行数据库初始化的mysql-ini.sql，这样会比较方便一些部署

注意在mysql-ini.sql的编写过程中，需要使用`CREATE TABLE IF NOT EXISTS worker`

（resources目录下）`aiswitchboard/src/main/resources/mysql-ini.sql`

```sql
CREATE TABLE IF NOT EXISTS `worker` (
   `id` bigint NOT NULL AUTO_INCREMENT COMMENT '自增主键',
   `corpid` varchar(8) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "" COMMENT '企业id',
    `dep` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "" COMMENT '部门名称',
    `per` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "" COMMENT '人名',
    `extension` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "",
    `directline` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "",
    `mandarin_per` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "",
    `mandarin_strip_per` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "",
    `mandarin_dep` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "",
    `mandarin_strip_dep` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "",
    `mandarin_set` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "",
    `mandarin` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "",
    `mandarin_strip` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL DEFAULT "",
    PRIMARY KEY (`id`),
    UNIQUE KEY `worker` (`corpid`, `dep`, `per`)
) ENGINE=InnoDB AUTO_INCREMENT=4001 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

将这个数据库表加入`application.yml`的配置中，将在启动的时候被自动执行

```yaml
# MybatisPlusConfig
spring:
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://10.109.246.188:3306/icoe_sbai
    username: root
    password: ********
  sql:
    init:
      mode: always
      schema-locations: classpath:mysql-ini.sql
      # data-locations: classpath:data.sql
```

## 3. Worker类

在model目录下（也有人会称为别的名字，一般就是存放各种基础类的这个文件夹）创建Worker类

在创建的过程中注意，这里使用驼峰命名法，要和数据库那边的下划线命名法完全一一对应上

`aiswitchboard/src/main/java/cn/edu/bupt/aiswitchboard/model/Worker.java`

```java
package cn.edu.bupt.aiswitchboard.model;

public class Worker {
    private Long id;
    private String corpid;
    private String dep;
    private String per;
    private String extension;
    private String directline;
    private String mandarinPer;
    private String mandarinStripPer;
    private String mandarinDep;
    private String mandarinStripDep;
    private String mandarinSet;
    private String mandarin;
    private String mandarinStrip;

    public Worker() {
    }

    public Worker(String corpid, String dep, String per, String extension, String directline, String mandarinPer, String mandarinStripPer, String mandarinDep, String mandarinStripDep, String mandarinSet, String mandarin, String mandarinStrip) {
        this.corpid = corpid;
        this.dep = dep;
        this.per = per;
        this.extension = extension;
        this.directline = directline;
        this.mandarinPer = mandarinPer;
        this.mandarinStripPer = mandarinStripPer;
        this.mandarinDep = mandarinDep;
        this.mandarinStripDep = mandarinStripDep;
        this.mandarinSet = mandarinSet;
        this.mandarin = mandarin;
        this.mandarinStrip = mandarinStrip;
    }

    public Worker(Long id, String corpid, String dep, String per, String extension, String directline, String mandarinPer, String mandarinStripPer, String mandarinDep, String mandarinStripDep, String mandarinSet, String mandarin, String mandarinStrip) {
        this.id = id;
        this.corpid = corpid;
        this.dep = dep;
        this.per = per;
        this.extension = extension;
        this.directline = directline;
        this.mandarinPer = mandarinPer;
        this.mandarinStripPer = mandarinStripPer;
        this.mandarinDep = mandarinDep;
        this.mandarinStripDep = mandarinStripDep;
        this.mandarinSet = mandarinSet;
        this.mandarin = mandarin;
        this.mandarinStrip = mandarinStrip;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getCorpid() {
        return corpid;
    }

    public void setCorpid(String corpid) {
        this.corpid = corpid;
    }

    public String getDep() {
        return dep;
    }

    public void setDep(String dep) {
        this.dep = dep;
    }

    public String getPer() {
        return per;
    }

    public void setPer(String per) {
        this.per = per;
    }

    public String getExtension() {
        return extension;
    }

    public void setExtension(String extension) {
        this.extension = extension;
    }

    public String getDirectline() {
        return directline;
    }

    public void setDirectline(String directline) {
        this.directline = directline;
    }

    public String getMandarinPer() {
        return mandarinPer;
    }

    public void setMandarinPer(String mandarinPer) {
        this.mandarinPer = mandarinPer;
    }

    public String getMandarinStripPer() {
        return mandarinStripPer;
    }

    public void setMandarinStripPer(String mandarinStripPer) {
        this.mandarinStripPer = mandarinStripPer;
    }

    public String getMandarinDep() {
        return mandarinDep;
    }

    public void setMandarinDep(String mandarinDep) {
        this.mandarinDep = mandarinDep;
    }

    public String getMandarinStripDep() {
        return mandarinStripDep;
    }

    public void setMandarinStripDep(String mandarinStripDep) {
        this.mandarinStripDep = mandarinStripDep;
    }

    public String getMandarinSet() {
        return mandarinSet;
    }

    public void setMandarinSet(String mandarinSet) {
        this.mandarinSet = mandarinSet;
    }

    public String getMandarin() {
        return mandarin;
    }

    public void setMandarin(String mandarin) {
        this.mandarin = mandarin;
    }

    public String getMandarinStrip() {
        return mandarinStrip;
    }

    public void setMandarinStrip(String mandarinStrip) {
        this.mandarinStrip = mandarinStrip;
    }

    @Override
    public String toString() {
        return "Worker{" +
                "id=" + id +
                ", corpid='" + corpid + '\'' +
                ", dep='" + dep + '\'' +
                ", per='" + per + '\'' +
                ", extension='" + extension + '\'' +
                ", directline='" + directline + '\'' +
                ", mandarinPer='" + mandarinPer + '\'' +
                ", mandarinStripPer='" + mandarinStripPer + '\'' +
                ", mandarinDep='" + mandarinDep + '\'' +
                ", mandarinStripDep='" + mandarinStripDep + '\'' +
                ", mandarinSet='" + mandarinSet + '\'' +
                ", mandarin='" + mandarin + '\'' +
                ", mandarinStrip='" + mandarinStrip + '\'' +
                '}';
    }
}
```

## 4. 创建MyBatisPlusConfig类以及WorkerDao接口

MyBatisPlusConfig类创建在config目录下，这里的MapperScan简单来说表示会去找dao目录下的

`aiswitchboard/src/main/java/cn/edu/bupt/aiswitchboard/config/MybatisPlusConfig.java`

```java
package cn.edu.bupt.aiswitchboard.config;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@MapperScan("cn.edu.bupt.aiswitchboard.dao")
public class MybatisPlusConfig {
}
```

创建WorkerDao接口，由于MyBatisPlus，就可以比较简单的使用这个接口

`aiswitchboard/src/main/java/cn/edu/bupt/aiswitchboard/dao/WorkerDao.java`

```java
package cn.edu.bupt.aiswitchboard.dao;

import cn.edu.bupt.aiswitchboard.model.Worker;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface WorkerDao extends BaseMapper<Worker> {
}
```

## 5. 在ServiceImpl类中，注入Dao并可以直接使用

```java
@Service
public class NameFinderServiceImpl implements NameFinderService{

    // 注入Dao
    @Autowired
    private WorkerDao workerDao;

    @Override
    public Object find(NameFinderFindRequest requestParameters) {
        ... 
        List<Worker> workerList = workerDao.selectList(null);
        ...
    }
}
```
