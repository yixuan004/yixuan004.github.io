---
title: JavaSSM-1Spring入门
date: 2023-02-08 22:52:19
tags:
---

# JavaSSM-1Spring入门

Spring Framework、Spring Boot、Spring Cloud

## 1. Spring Framework 系统架构

是Spring生态圈中最基础的项目，是其他项目的根基。从Core Container（装对象的）开始学习

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230208231043515.png" alt="image-20230208231043515" style="zoom:50%;" />

## 2. 核心概念——IoC

### 2.1 IoC概念

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230208231323058.png" alt="image-20230208231323058" style="zoom:50%;" />



<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230208231532222.png" alt="image-20230208231532222" style="zoom:50%;" />

IoC管Bean，IoC内部的Bean绑定！

### 2.2 IoC入门案例（还没有充分做到解耦的效果）

业务层接口、业务层实现类。数据层接口，数据层实现类

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230208235707824.png" alt="image-20230208235707824" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209000429812.png" alt="image-20230209000429812" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209000615859.png" alt="image-20230209000615859" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209000644195.png" alt="image-20230209000644195" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209000655197.png" alt="image-20230209000655197" style="zoom:50%;" />

## 3. DI入门案例

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209001833780.png" alt="image-20230209001833780" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209001844419.png" alt="image-20230209001844419" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209001914032.png" alt="image-20230209001914032" style="zoom:50%;" />

## 4. bean

### 4.1 bean配置

#### 4.1.1 基础配置

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209002649711.png" alt="image-20230209002649711" style="zoom:50%;" />

#### 4.1.2 别名name

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209002222940.png" alt="image-20230209002222940" style="zoom:50%;" />



#### 4.1.3 作用范围配置（默认单例）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209002510565.png" alt="image-20230209002510565" style="zoom:50%;" />

bean默认单例，有状态的不适合单例

### 4.2 bean实例化

Spring创建bean的时候用的是**无参的构造办法**

#### 4.2.1 构造方法实例化bean

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209003110520.png" alt="image-20230209003110520" style="zoom:50%;" />

#### 4.2.2 静态工厂实例化bean

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209003315606.png" alt="image-20230209003315606" style="zoom:50%;" />

#### 4.2.3 实例工厂初始化bean（FactorBean）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209003917166.png" alt="image-20230209003917166" style="zoom:50%;" />

### 4.3 bean生命周期

第一种方式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209004615386.png" alt="image-20230209004615386" style="zoom:50%;" />

第二种方式（接口，了解）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230209004632261.png" alt="image-20230209004632261" style="zoom:50%;" />

## 5. 依赖注入方式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230210005955689.png" alt="image-20230210005955689" style="zoom:50%;" />

### 5.1 Setter注入

引用类型

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230210005205774.png" alt="image-20230210005205774" style="zoom:50%;" />

简单类型

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230210005311603.png" alt="image-20230210005311603" style="zoom:50%;" />

### 5.2 构造器注入

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230210005906297.png" alt="image-20230210005906297" style="zoom:50%;" />

## 6. 自动装配

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230210010527061.png" alt="image-20230210010527061" style="zoom:50%;" />

## 7. 集合注入

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230210011317493.png" alt="image-20230210011317493" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230210011325668.png" alt="image-20230210011325668" style="zoom:50%;" />

...

## 8. 案例：数据源对象管理

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230210012020423.png" alt="image-20230210012020423" style="zoom:50%;" />