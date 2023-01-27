---
title: JavaWeb-4WebTomcatServlet
date: 2023-01-08 23:58:28
tags:
---

# JavaWeb-4WebTomcatServlet

## 1. Web

基本在python开发还有自己学习的过程里，很多内容都明白了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230115113816834.png" alt="image-20230115113816834" style="zoom:50%;" />

- 请求数据格式

- 响应数据格式

## 2. Web服务器-Tomcat（war包，可能未来Spring的时候就用不到了，那时候是jar包并且集成了http服务器？）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117000626605.png" alt="image-20230117000626605" style="zoom:50%;" />

### 2.1 简介

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117001636901.png" alt="image-20230117001636901" style="zoom:50%;" />

### 2.2 下载、安装、卸载、启动、关闭

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117002222930.png" alt="image-20230117002222930" style="zoom:50%;" />

### 2.3 配置和部署项目

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117002241395.png" alt="image-20230117002241395" style="zoom:50%;" />

tomcat有一种python中django的感觉？

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117002517381.png" alt="image-20230117002517381" style="zoom:50%;" />

通过IDEA插件就能给打包成war包了

### 2.4 IDEA中创建maven项目

打包就变成右边那样了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117002918226.png" alt="image-20230117002918226" style="zoom:50%;" />

### 2.5 创建maven web项目

#### 2.5.1 使用骨架的方式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117002956402.png" alt="image-20230117002956402" style="zoom:50%;" />

#### 2.5.2 不使用骨架的方式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117003153148.png" alt="image-20230117003153148" style="zoom:50%;" />

### 2.6 IDEA中使用Tomcat

#### 2.6.1 继承本地Tomcat

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117003500498.png" alt="image-20230117003500498" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117003713331.png" alt="image-20230117003713331" style="zoom:50%;" />

#### 2.6.2 使用Tomcat Maven插件（需要前置Maven Helper插件）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117003927784.png" style="zoom:50%;" />

## 3. Servlet相关（动态相关）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117004126203.png" alt="image-20230117004126203" style="zoom:50%;" />

动态资源：不同用户，不一样，有一些逻辑判断（类似flask那种方式？）

### 3.1 快速入门

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117004321834.png" alt="image-20230117004321834" style="zoom:50%;" />

实现Servlet接口，复写接口的几个方法

类上使用注解的方式！！！！！！

### 3.2 执行流程

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117004925852.png" alt="image-20230117004925852" style="zoom:50%;" />

**service方法很关键！**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117005020069.png" alt="image-20230117005020069" style="zoom:50%;" />

### 3.3 生命周期（Servlet是由tomcat来创建的）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117005118285.png" alt="image-20230117005118285" style="zoom:50%;" />

耗时的操作提前到服务器启动的时候

destroy这块还需要再理解下，结束的时候会被destory，不知道什么用处还

### 3.4 Servlet方法介绍

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117005924411.png" alt="image-20230117005924411" style="zoom:50%;" />

提升成员变量作用域

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117010052401.png" alt="image-20230117010052401" style="zoom:50%;" />

然后在`ServletConfig getServletConfig()`中给返回回去就行了

### 3.5 体系结构——简化Servlet开发

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117010221656.png" alt="image-20230117010221656" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117011048174.png" alt="image-20230117011048174" style="zoom:50%;" />

### 3.6 Servlet urlPattern配置

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117011151985.png" alt="image-20230117011151985" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117011912868.png" alt="image-20230117011912868" style="zoom:50%;" />

精确匹配优先级高于目录匹配

default处理静态资源？但是暂时不管前端那边的，后端要设计个好的技术

### 3.8 XML配置Servlet

还是用注解的方式吧hh

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230117011933496.png" alt="image-20230117011933496" style="zoom:50%;" />

调用模型

## 4. Request & Response

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118232958027.png" alt="image-20230118232958027" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118233031252.png" alt="image-20230118233031252" style="zoom:50%;" />

### 4.1 Request

#### 4.1.1 继承体系

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118233310067.png" alt="image-20230118233310067" style="zoom:50%;" />

#### 4.1.2 获取请求数据

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118234359429.png" alt="image-20230118234359429" style="zoom:50%;" />

- get方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118233712440.png" alt="image-20230118233712440" style="zoom:50%;" />

- post

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118234328215.png" alt="image-20230118234328215" style="zoom:50%;" />

#### 4.1.3 Request通用方式获取请求参数

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118234940698.png" alt="image-20230118234940698" style="zoom:50%;" />

get和post都可以这么用，post里面可以`this.doGet()`

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118235205394.png" alt="image-20230118235205394" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118235442614.png" alt="image-20230118235442614" style="zoom:50%;" />

#### 4.1.4 使用Servlet模板创建Servlet更高效

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230118235906322.png" alt="image-20230118235906322" style="zoom:50%;" />

#### 4.1.5 Request请求参数中文乱码处理（POST GET）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230119001105740.png" alt="image-20230119001105740" style="zoom:50%;" />

#### 4.1.6 请求转发（透传？）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230119001237088.png" alt="image-20230119001237088" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230119001708149.png" alt="image-20230119001708149" style="zoom:50%;" />

共享数据的理解：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230119001642719.png" alt="image-20230119001642719" style="zoom:50%;" />

### 4.2 Response

- 重定向

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230119002418247.png" alt="image-20230119002418247" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230119002440308.png" alt="image-20230119002440308" style="zoom:50%;" />



<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230119002751022.png" alt="image-20230119002751022" style="zoom:50%;" />

- 响应

































