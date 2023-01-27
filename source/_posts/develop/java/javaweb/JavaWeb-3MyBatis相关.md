---
title: JavaWeb-3MyBatis相关
date: 2023-01-08 23:57:00
tags:
	- javaweb
categories:
	- develop
	- java
	- javaweb
---

# JavaWeb-3MyBatis相关

## 1. MyBatis简介（很多要配置的）

### 1.1 简介

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109212111130.png" alt="image-20230109212111130" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109212151210.png" alt="image-20230109212151210" style="zoom:50%;" />

### 1.2 JDBC缺点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109212453710.png" alt="image-20230109212453710" style="zoom:50%;" />

### 1.3 MyBatis简化

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109212610077.png" alt="image-20230109212610077" style="zoom:50%;" />

## 2. MyBatis快速入门 —— 以一个需求为例

### 2.1 MyBatis快速入门，查询user表中所有数据

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109212758182.png" alt="image-20230109212758182" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109213222252.png" alt="image-20230109213222252" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109213341970.png" alt="image-20230109213341970" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109213632831.png" alt="image-20230109213632831" style="zoom:50%;" />

官网继续复制代码，编写一个`MyBatisDemo`类

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109213950468.png" alt="image-20230109213950468" style="zoom:50%;" />

### 2.2 解决SQL映射文件的告警提示

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109214131442.png" alt="image-20230109214131442" style="zoom:50%;" />

## 3. Mapper代理开发

### 3.1 目的

上面的那种快速入门方式，还是要解决硬编码的一些问题

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109214453381.png" alt="image-20230109214453381" style="zoom:50%;" />

### 3.2 使用Mapper代理

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109214540470.png" alt="image-20230109214540470" style="zoom:50%;" />

- 第一步

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109214807815.png" alt="image-20230109214807815" style="zoom:50%;" />

- 第二步

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109214854671.png" alt="image-20230109214854671" style="zoom:50%;" />

- 第三步（参数和返回值类型一样）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109214948487.png" alt="image-20230109214948487" style="zoom:50%;" />

- 第四步，小改路径

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109215013064.png" alt="image-20230109215013064" style="zoom:50%;" />

### 3.3 编码（重点，实现接口）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109215111333.png" alt="image-20230109215111333" style="zoom:50%;" />

### 3.4 简化映射文件的加载

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109215355994.png" alt="image-20230109215355994" style="zoom:50%;" />

## 4. MyBatis核心配置文件`mybatis-config.xml`

- 配置多个，可以链接不同的数据库，default切换

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109215633629.png" alt="image-20230109215633629" style="zoom:50%;" />

- 别名（可以包扫描）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109215843771.png" alt="image-20230109215843771" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109215902431.png" alt="image-20230109215902431" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109215949688.png" alt="image-20230109215949688" style="zoom:50%;" />

- **xml的顺序约束！！！！！**

## 5. 配置文件完成增删改查

### 5.1 需求说明

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109220117240.png" alt="image-20230109220117240" style="zoom:50%;" />

条件查询，删除多个（批量删除）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109220157900.png" alt="image-20230109220157900" style="zoom:50%;" />

### 5.2 MyBatisX插件

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109220348723.png" alt="image-20230109220348723" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109220518125.png" alt="image-20230109220518125" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109220545321.png" alt="image-20230109220545321" style="zoom:50%;" />

### 5.3 查询所有&结果映射（数据库列名和属性名不一致）

#### 5.3.1 查询所有数据

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109220854395.png" alt="image-20230109220854395" style="zoom:50%;" />

sql语句怎么写、有没有参数、完成了后返回什么值

#### 5.3.2 数据库的名称，和类的名称可能对不上，比如hello_world和helloWorld

- 起别名，太麻烦了

- 灵活解决方案，resultMap

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109222537678.png" alt="image-20230109222537678" style="zoom:50%;" />

### 5.4 查看详情（根据id来进行查询）

#### 5.4.1 设置参数（参数占位符、parameterType可选、特殊字符处理CDATA区和转义字符）

查看某一条数据的详细信息，根据id来进行查询

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109222829494.png" style="zoom:50%;" />

parameterType好像不用写？

参数占位符，使用`#{}`替代`${}`，可以避免sql注入问题

#### 5.4.2 特殊字符处理

![image-20230109223401615](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109223401615.png)

### 5.5 查询-条件查询

#### 5.5.1 查询-多条件查询

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109223816259.png" alt="image-20230109223816259" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109223823534.png" alt="image-20230109223823534" style="zoom:50%;" />

参数有三种不同的接收方式

- 第一种方式

注意需要手工处理一下这个百分号的地方

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109230054484.png" alt="image-20230109230054484" style="zoom:50%;" />

- 第二种方式

对象的属性的名称，要和参数占位符都一样

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109231005222.png" alt="image-20230109231005222" style="zoom:50%;" />

- 第三种方式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109231117250.png" alt="image-20230109231117250" style="zoom:50%;" />

#### 5.5.2 查询-多条件-动态条件查询（关键，假设3个条件，用户可以输入0 1 2 3个，if where标签）

用户有可能只输入一个条件，因为拼接条件的时候用的是and，程序太不灵活了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109231335033.png" alt="image-20230109231335033" style="zoom:50%;" />

- if的强大使用，然而有and拼接问题

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109232113419.png" alt="image-20230109232113419" style="zoom:50%;" />

然而，如果只根据companyName进行查询，那么这个and就给拼上去了，SQL语法就直接错了

解决：

1. 恒等式，where 1=1 and xxxx and xxxx，这样格式就全一样了
2. where标签

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109232626129.png" alt="image-20230109232626129" style="zoom:50%;" />

#### 5.5.3 查询-单条件-动态条件查询（多个条件中选择一个）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109223648652.png" alt="image-20230109223648652" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109232848325.png" alt="image-20230109232848325" style="zoom:50%;" />

**多个条件，只能用其中一种！！！！！！！！！！**

### 5.6 添加

#### 5.6.1 最基础的添加

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109233603598.png" alt="image-20230109233603598" style="zoom:50%;" />

**增删改的时候，要commit**

```java
sqlSession.commit()
```

#### 5.6.2 添加——主键返回（TODO多主键怎么办？）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109234530680.png" alt="image-20230109234530680" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109234627841.png" alt="image-20230109234627841" style="zoom:50%;" />

### 5.7 修改（update）

#### 5.7.1 修改全部字段

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109234725994.png" alt="image-20230109234725994" style="zoom:50%;" />

可以返回影响的行数

#### 5.7.2 动态修改

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109234926110.png" alt="image-20230109234926110" style="zoom:50%;" />

set也可以用标签，一个update功能就很通用

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109235405755.png" alt="image-20230109235405755" style="zoom:50%;" />

### 5.8 删除

#### 5.8.1 删除一个

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109235531403.png" alt="image-20230109235531403" style="zoom:50%;" />

#### 5.8.2 批量删除（复选删除）

id数组，一次性的接收过来

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230109235709967.png" alt="image-20230109235709967" style="zoom:50%;" />

## 6. MyBatis参数传递

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230110000258548.png" alt="image-20230110000258548" style="zoom:50%;" />

多个参数，param注解

## 7. 注解，只适合简单语句

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230110000949230.png" alt="image-20230110000949230" style="zoom:50%;" />











