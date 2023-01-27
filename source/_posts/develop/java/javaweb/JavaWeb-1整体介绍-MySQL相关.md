---
title: JavaWeb-1整体介绍+MySQL相关
date: 2023-01-08 17:15:18
tags:
	- javaweb
categories:
	- develop
	- java
	- javaweb
---

# JavaWeb-1整体介绍+MySQL相关

![image-20230108174927136](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108174927136.png)

## 1. 数据库

### 1.1 数据库基础&自己已经掌握过的内容简记

#### 1.1.1 数据库相关概念

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108175206703.png" alt="image-20230108175206703" style="zoom:50%;" />

#### 1.1.2 MySQL数据模型

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108192351416.png" alt="image-20230108192351416" style="zoom:50%;" />



#### 1.1.3 SQL简介&通用语法&分类

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108193503363.png" alt="image-20230108193503363" style="zoom:50%;" />

## 2. JDBC简介、快速入门

使用Java语言操作数据库（Logstash内部就是通过Java语言操作数据库，然后往Elasticsearch中进行数据导入的）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108195926114.png" alt="image-20230108195926114" style="zoom:50%;" />

### 2.1 JDBC简介

Java DataBase Connectivity，Java数据库连接

实现类自己来搞

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108200455610.png" alt="image-20230108200455610" style="zoom:50%;" />

### 2.2 JDBC快速入门

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108200559059.png" alt="image-20230108200559059" style="zoom:50%;" />

注册驱动的时候要把异常抛出去，抛一个大一点的Exception

### 2.3 JDBC API详解

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108195926114.png" alt="image-20230108195926114" style="zoom:50%;" />

#### 2.3.1 DriverManager

注册驱动的`Class.forName()`未来不用写了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108201858075.png" alt="image-20230108201858075" style="zoom:50%;" />

#### 2.3.2 Connection

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108202142418.png" alt="image-20230108202142418" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108202153537.png" alt="image-20230108202153537" style="zoom:50%;" />

**事务：transaction，事务管理也是很需要的**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108202428859.png" alt="image-20230108202428859" style="zoom:50%;" />

#### 2.3.3 Statement

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108193503363.png" alt="image-20230108193503363" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108202606732.png" alt="image-20230108202606732" style="zoom:50%;" />

count > 0就执行成功，否则可能执行失败了，这个根据返回值来判断

**<font color='red'>select语句的返回对象是什么？</font>** 

#### 2.3.4 Resultset（查询结果封装）

**<font color='red'>解决了上面的问题，是select后executeQuery的查询</font>** 

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108203104508.png" alt="image-20230108203104508" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108203437864.png" alt="image-20230108203437864" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108203650729.png" alt="image-20230108203650729" style="zoom:50%;" />

大概是这个意思

#### 2.3.5 PreparedStatement（防止sql注入）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108204440050.png" alt="image-20230108204440050" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108205716991.png" alt="image-20230108205716991" style="zoom:50%;" />

？作为占位符进行替代，所以不要拼字符串了，用占位符。执行前需要设置？的值

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108210104883.png" alt="image-20230108210104883" style="zoom:50%;" />

**原理是，把单引号进行转义**

#### 2.3.6 PreparedStatement原理（预编译，需要手动开启）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108210439863.png" alt="image-20230108210439863" style="zoom:50%;" />

**预编译功能需要开启！！！！**

![image-20230108210644387](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108210644387.png)

### 2.4 数据库连接池（Driud为例）

德鲁伊

#### 2.4.1 简介

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108211134524.png" alt="image-20230108211134524" style="zoom:50%;" />

频繁开启关闭，资源消耗过大

#### 2.4.2 数据库连接池实现

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108211354852.png" alt="image-20230108211354852" style="zoom:50%;" />

以后Connection就用这个获取了

#### 2.4.3 Driud使用步骤（重点）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108211510338.png" alt="image-20230108211510338" style="zoom:50%;" />

定义配置文件：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108211639681.png" alt="image-20230108211639681" style="zoom:50%;" />

加载配置文件、获取数据库连接池对象、获取连接等代码

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108211906789.png" alt="image-20230108211906789" style="zoom:50%;" />

路径有问题，改换一下拼一下：

![image-20230108212009328](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108212009328.png)

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108212026229.png" alt="image-20230108212026229" style="zoom:50%;" />









