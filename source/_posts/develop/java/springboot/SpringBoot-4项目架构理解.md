---
title: SpringBoot-4项目架构理解
date: 2023-04-10 21:59:17
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot-4项目架构理解

chatgpt给了下面这样一个输出，

```shell
.
├── controller
│   ├── UserController.java
│   └── ...
├── service
│   ├── UserService.java
│   ├── UserServiceImpl.java
│   └── ...
├── repository
│   ├── UserRepository.java
│   ├── UserRepositoryImpl.java
│   └── ...
├── model
│   ├── User.java
│   ├── Order.java
│   └── ...
├── view
│   ├── index.html
│   ├── error.html
│   └── ...
├── configuration
│   ├── AppConfig.java
│   └── ...
├── Application.java
├── resources
│   ├── application.properties
│   ├── static
│   │   ├── css
│   │   ├── js
│   │   ├── img
│   │   └── ...
│   └── templates
│       ├── index.html
│       ├── error.html
│       └── ...
└── ...

```

一般来说

- controller层就像是定义一个输入输出，然后把PostMapping啥的设置在这一层
- service层是具体业务的处理，需要在controller层自动注入service层的内容，通过@Autowired的方式
- repository层一般就算是数据访问层（Dao层），放和数据库交互的内容
- model一般就是实体类的层，面向对象变成，可以定义一些接口的输入然后配合swagger放在这里
- configuration一般就写一些配置的类，比如说SwaggerConfig类这样的
- 外层的Application.java像是一个全局的入口，有点像python里面views.py和server.py那样的理解？