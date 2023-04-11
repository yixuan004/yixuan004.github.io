---
title: SpringBoot-1快速入门初始化工程
date: 2023-02-18 16:16:58
tags:
---

# SpringBoot-1快速入门初始化工程

简化Spring应用的初始搭建以及开发过程，Spring已经很简化了，再进一步简化下

## 1. 开始配置一个项目 入门案例

### 1.1 初始化项目

使用IDEA创建一个SpringBoot工程，new project这边选择到Spring Initialzr，里面的各项选项注意和这里对应到

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230218162829422.png" alt="image-20230218162829422" style="zoom:50%;" />

<font color='red'>这里注意一下SpringBoot的版本，3.x需要jdk17的支持，所以这里实际降级一下选到jdk8去</font> 

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230218163022842.png" alt="image-20230218163022842" style="zoom:50%;" />

至此工程加载完毕，删掉除了.idea，src，pom.xml的其他文件，保留到下面的这些

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230218164013570.png" alt="image-20230218164013570" style="zoom:50%;" />

项目结构整体如下

![image-20230218164804916](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230218164804916.png)



### 1.2 创建controller

创建如下的Java类，并把这个RestController和RequestMapping给写上

/Users/curious/Desktop/Engineering/java/demo/src/main/java/cn/edu/bupt/controller/SwitchBoardController.java

```java
package cn.edu.bupt.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/switchboard")
public class SwitchBoardController {
    
}
```

进一步完善一个getById功能，这样写好后SpringBoot程序就已经开发完了，Tomcat服务器那些都不需要了

```java
package cn.edu.bupt.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/switchboard")
public class SwitchBoardController {
    @GetMapping("/{id}")
    public String getById(@PathVariable  Integer id) {
        System.out.println("id: " + id);
        return "Hello, SpringBoot!";
    }
}
```

右键点击src/main/java/cn/edu/bupt/DemoApplication.java，选择Run Application，直接启动起来了

```shell
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.7.8)
```

```shell
2023-02-18 17:05:28.221  INFO 18715 --- [           main] cn.edu.bupt.DemoApplication              : Starting DemoApplication using Java 1.8.0_312 on CuriousdeMacBook-Pro.local with PID 18715 (/Users/curious/Desktop/Engineering/java/demo/target/classes started by curious in /Users/curious/Desktop/Engineering/java/demo)
2023-02-18 17:05:28.222  INFO 18715 --- [           main] cn.edu.bupt.DemoApplication              : No active profile set, falling back to 1 default profile: "default"
2023-02-18 17:05:28.569  INFO 18715 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2023-02-18 17:05:28.572  INFO 18715 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2023-02-18 17:05:28.572  INFO 18715 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.71]
2023-02-18 17:05:28.598  INFO 18715 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2023-02-18 17:05:28.599  INFO 18715 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 356 ms
2023-02-18 17:05:28.714  INFO 18715 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2023-02-18 17:05:28.718  INFO 18715 --- [           main] cn.edu.bupt.DemoApplication              : Started DemoApplication in 0.661 seconds (JVM running for 0.901)
```

![image-20230218170912366](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230218170912366.png)

```shell
curl --location --request GET 'http://localhost:8080/switchboard/1'
```

### 1.3 原理分析

不是pom.xml干的活就是DemoApplication干的活

## N. 报错解决

### N.1 java: 错误: 无效的源发行版：17

报错源于点击run application的时候，解决办法是Module这里面也要跟着改

![image-20230218165818077](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230218165818077.png)

![image-20230218165951940](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230218165951940.png)

这样还是没有解决，最后发现是SpringBoot3.x版本需要jdk17作为支撑，这里选用比较简单的方法，重新创建一个项目，降低SpringBoot版本；





















