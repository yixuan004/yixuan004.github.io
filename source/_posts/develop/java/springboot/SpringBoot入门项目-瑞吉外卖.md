---
title: SpringBoot入门项目-瑞吉外卖
date: 2022-12-19 15:24:30
tags:
---

# SpringBoot入门带项目-瑞吉外卖

reference：黑马程序员 https://www.bilibili.com/video/BV13a411q753?p=1&vd_source=2654875159ed89b917fffce42a65196d

项目栈：SpringBoot+SSM

前置知识：java基础知识、java web、 mysql、spring boot、SSM（Spring Spring MVC MyBatis）、Maven（项目构建）

## 0. 过程中的问题记录

2022.12.19：在看到静态资源映射的时候，我只是一个后端的服务，为什么前端的代码也要写在我这里？还是说一个项目开发的时候，前后端代码就是都融合在一个项目里，只是各自git push各自部分的？

2022.12.19：在基础框架搭起来之后，每个页面感觉都是可以不登录就直接访问的？这样在后期的控制中是怎么控制的，要么感觉很容易就会有泄露等问题？



## 1. 业务开发

### 1.1 软件开发整体介绍

#### 1.1.1 软件开发流程

需求分析（产品原型、需求规格说明书）-> 设计（产品文档、UI界面设计、概要设计、详细设计、数据库设计）->**编码（项目代码、单元测试）**->测试（测试用例、测试报告）->上线运维（软件环境安装、配置）

#### 1.1.2 角色分工

- 项目经理：对整个项目负责，任务分配，把控进度
- 产品经理：进行需求调研，输出需求调研文档，产品原型
- UI设计师：根据产品原型输出界面效果图
- 架构师：项目整体设计、技术选型等
- 开发工程师：代码实现
- 测试工程师：编写测试用例，输出测试报告
- 运维工程师：软件环境搭建，项目上线

#### 1.1.3 软件环境

- 开发环境（development）：开发人员在开发阶段使用的环境，一般外部用户无法访问
- 测试环境（testing）：专门给测试人员使用的环境，用于测试项目，一般外部用户无法访问
- 生产环境（production）：即上线环境，正式提供对外服务的环境

### 1.2 瑞吉外卖项目整体介绍

#### 1.2.1 项目介绍

为餐饮企业定制的点菜产品，**管理后台**和**移动端**两部分，后台主要是维护，前台就是用来点菜

分为3期进行开发：

1. 实现基本需求，移动端应用通过h5实现，用户通过手机浏览器访问
2. 针对移动端应用改进，微信小程序实现，用户使用更方便
3. 优化升级，提升访问性能

#### 1.2.2 产品原型展示

成型之前的简单框架，通过原型展示更加了解项目的需求和提供的功能

主要是用于提供原型展示项目的功能，而不是最终实现出来的效果

#### 1.2.3 技术选型

![image-20221219155303706](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219155303706.png)

#### 1.2.4 功能架构

![image-20221219155513217](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219155513217.png)

#### 1.2.5 角色

- 后台系统管理员：登录后台管理系统，拥有后台系统中的所有操作权限
- 后台系统普通员工：登录后台管理系统，对菜品、套餐、订单进行管理
- C（Customer）端用户：登录应用，浏览菜品，添加购物车等

### 1.3 开发环境搭建

**<font color='red'>这个项目暂时就先在本地开发了，根据了解来说，好像流程就是本地开发，然后服务器放打包的包</font>** 

**<font color='red'>但是数据库mysql redis那些的可以用服务器远程的，有些时候可能需要端口转发或者怎么的方法，也可能可以用那个ip直接连，但是比如mysql就需要进行配置</font>** 

#### 1.3.1 数据库环境搭建

- 使用navicat新建数据库（或者使用命令的方式也可以，暂时先使用h9的数据库）

![image-20221219163216121](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219163216121.png)

- 使用命令行创建

```sql
create databases reggie character set utf8mb4;
```

- 导入表结构（使用课程提供的资料进行表结构的倒入）

可以用source的方式导入，也可以直接图形化界面导入

这里表结构是项目提供的，用这个方式进行领取：https://www.bilibili.com/read/cv11763184

导入后，navicat可视化如下所示

![image-20221219164719147](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219164719147.png) 

```mysql
mysql> show tables;
+------------------+
| Tables_in_reggie |
+------------------+
| address_book     |  # 地址簿表
| category         |  # 菜品和套餐分类表
| dish             |  # 菜品表
| dish_flavor      |  # 菜品口味关系表
| employee         |  # 员工表
| order_detail     |  # 订单明细表
| orders           |  # 订单表
| setmeal          |  # 套餐表
| setmeal_dish     |  # 套餐菜品关系表
| shopping_cart    |  # 购物车表
| user             |  # 用户表（Customer端）
+------------------+
11 rows in set (0.00 sec)
```

#### 1.3.2 maven项目搭建

1. **创建maven项目（使用IDEA）**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219165917019.png" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219170108556.png" alt="image-20221219170108556" style="zoom:50%;" />

注意，创建完成后要看看maven和jdk那些选的对不对，如下所示

![image-20221219170543761](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219170543761.png)

maven：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219170628790.png" alt="image-20221219170628790" style="zoom:33%;" />

runner：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219170705330.png" alt="image-20221219170705330" style="zoom:33%;" />

sdk（file->project structure）：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219173119502.png" alt="image-20221219173119502" style="zoom:33%;" />

2. **导入pom文件，直接导入的方式，在教程给的资料里面有个pom.xml文件**

继承了`spring-boot-starter-parent2.4.5`，先把这段给复制到那边的maven里面去

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.5</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

然后properties，这块有个1.8也给复制到那边的properties里面去

```xml
<properties>
    <java.version>1.8</java.version>
</properties>
```

dependency，也粘贴到下面去

```xml
<dependencies>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.4.2</version>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.20</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.76</version>
    </dependency>

    <dependency>
        <groupId>commons-lang</groupId>
        <artifactId>commons-lang</artifactId>
        <version>2.6</version>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <scope>runtime</scope>
    </dependency>

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.23</version>
    </dependency>

</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.4.5</version>
        </plugin>
    </plugins>
</build>
```

这里整体过一下，这些感觉就是一些jar包，类似python那种import东西的感觉？python要pip install，但是这个在这块就直接ide下载了

`starter` `mybatis`等等

3. application.yml

这个搞到那个resources目录`src/main/resources/applications.yml`

这里的mysql要改一下密码，然后设置到远程的服务器那边

```yml
server:
  port: 8080
spring:
  application:
    name: reggie_take_out
  datasource:
    druid:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://ipipipipip:3306/reggie?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&useSSL=false&allowPublicKeyRetrieval=true
      username: root
      password: xxxxxx
mybatis-plus:
  configuration:
    #在映射实体或者属性时，将数据库中表名和字段名中的下划线去掉，按照驼峰命名法映射
    #一般在表里设置表名的时候，习惯用下划线分割address_book 对应 AddressBook（实体类的类名）
    #user_name -> userName 属性
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      id-type: ASSIGN_ID
```

#### 1.3.3 编写一个启动类并启动测试（SpringBoot）

创建包结构`src/main/java/com/curious/reggie`

启动类的类名创建为`ReggieApplication`

`src/main/java/com/curious/reggie/ReggieApplication.java`

```java
package com.curious.reggie;

import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@Slf4j // 日志注解，lombok提供
@SpringBootApplication  // 这个注解是SpringBoot的一个模板
public class ReggieApplication {
    public static void main(String[] args) {
        SpringApplication.run(ReggieApplication.class, args);
        log.info("项目启动成功...");  // info级别的日志
    }
}
```

点击绿色箭头来run main方法， 这里输出项目启动成功

![image-20221219180038600](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219180038600.png)

这个时候访问localhost进行curl请求，已经会有返回值了，而不是一个端口不开放那种的

```shell
(base) ➜ /Users/curious ✻ curl --location --request GET 'localhost:8080'
{"timestamp":"2022-12-19T10:06:18.520+00:00","status":404,"error":"Not Found","message":"","path":"/"}%
```

#### 1.3.4 导入前端界面（这里不要求前端知识）

项目已经提供好了前端的页面，backend是后台管理系统设计的页面，front是移动端的一些页面

把`backend`和`front`这两个粘贴到resources目录下，即`src/main/resources`

这块有个小问题，前面讲SpringBoot的时候，应该也提到过了，对于我们引入的静态资源，建议放在`static`或`template`目录下面，这样放在`backend`和`front`默认是访问不到的

我们需要进行一个静态资源映射，为了能访问到这两个静态资源，需要编写一个配置类`src/main/java/com/curious/reggie/config/WebMvcConfig.java`，MVC框架静态资源的映射，如下：

```java
package com.curious.reggie.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

@Slf4j
@Configuration  // 为了说明是一个配置类，加个注解
public class WebMvcConfig extends WebMvcConfigurationSupport {
    /**
     * 设置静态资源映射
     * @param registry
     */
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        log.info("开始进行静态资源映射...");
        registry.addResourceHandler("/backend/**").addResourceLocations("classpath:/backend/");  // 通配符
        registry.addResourceHandler("/front/**").addResourceLocations("classpath:/front/");  // 通配符
    }
}
```

这样再次通过main函数进行启动的时候，会可以看到下面这个界面：

<font color='red'>这里本来还是打不开，弹幕指导下需要清除一下缓存，idea清一下缓存：</font> https://blog.csdn.net/nandao158/article/details/123803318

![image-20221219200145258](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219200145258.png)

这里为什么报错？因为发送了ajax请求，请求了后端，但是后端还没开发，所有报错了，暂时先不用管他

### 1.4 后台登录功能开发

前面的步骤把环境已经搭建好了，现在在搭建环境的基础上，开发进一步的功能，这里登录功能比较简单

#### 1.4.1 需求分析

一般是从页面原型开始说起`src/main/resources/backend/page/login`

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219200455172.png" alt="image-20221219200455172" style="zoom:50%;" />

![image-20221219200754893](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219200754893.png)

关心点击登录按钮后，请求到哪里去了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219200839051.png" alt="image-20221219200839051" style="zoom:50%;" />

![image-20221219200936857](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219200936857.png)

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219201018334.png" alt="image-20221219201018334" style="zoom:50%;" />

这里还可以去关心一下前端那块的逻辑，**约定了1表示登陆成功**等等，这里约定好了服务端需要给返回什么字段的数据，code data msg

![image-20221219201719162](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219201719162.png)

#### 1.4.2 代码开发——基础各个地方的定义

- 创建实体类Employee，和employee表进行映射，数据库中有一个表，就习惯去创建一个实体类和他映射，实体类的属性和表里的字段一一来映射，创建的实体类放到这里`src/main/java/com/curious/reggie/entity/Employee.java`

```java
package com.curious.reggie.entity;

import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.TableField;

import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * 员工实体
 */

// 一个类只有实现了Serializable接口 它的对象才是可序列化的，当你想把的内存中的对象写入到硬盘的时候需要序列化等等
// 因为在application.yml的时候，设置了映射了：在映射实体或者属性时，将数据库中表名和字段名中的下划线去掉，按照驼峰命名法映射，所以这样
public class Employee implements Serializable {

    private static final long serialVersionID = 1L;  // 这里的修饰的意思是说，不修改final？然后各个对象共享static？

    private Long id;

    private String username;

    private String password;

    private String phone;

    private String sex;

    private String idNumber;  // 身份证号码

    private Integer status;

    private LocalDateTime createTime;

    private LocalDateTime updateTime;

    @TableField(fill = FieldFill.INSERT)
    private Long createUser;  // 这个还不知道是干什么的，到时候还会去讲

    @TableField(fill=FieldFill.INSERT_UPDATE)
    private Long updateUser;  // 这个还不知道是干什么的

}
```

- 下面来把调用链条上的，Controller Service Mapper都给创建出来

![image-20221219203517527](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219203517527.png)

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219203448512.png" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219203537627.png" style="zoom:67%;" />

<font color='red'>这个地方和mybatis关系很大，之后需要详细看看mybatis这块是怎么搞的</font> 

**mapper**：`src/main/java/com/curious/reggie/mapper/EmployeeMapper.java`，这是个接口（Interface）

```java
package com.curious.reggie.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.curious.reggie.entity.Employee;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface EmployeeMapper extends BaseMapper<Employee> {
    // 虽然代码量不大，但是因为继承了BaseMapper，增删改查都已经完成了
}
```

**service**：`src/main/java/com/curious/reggie/service/EmployeeService.java`，**接口**定义好了

```java
package com.curious.reggie.service;

import com.baomidou.mybatisplus.extension.service.IService;
import com.curious.reggie.entity.Employee;

public interface EmployeeService extends IService<Employee> {
}
```

创建接口的实现类（Impl那块）`src/main/java/com/curious/reggie/service/impl/EmployeeServiceImpl.java`

```java
package com.curious.reggie.service.impl;

import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.curious.reggie.entity.Employee;
import com.curious.reggie.mapper.EmployeeMapper;
import com.curious.reggie.service.EmployeeService;
import org.springframework.stereotype.Service;

@Service
public class EmployeeServiceImpl extends ServiceImpl<EmployeeMapper, Employee> implements EmployeeService {  // 这里implements是继承接口，接口里面本身没有明确的说实现的方法
}
```

**controller**：`src/main/java/com/curious/reggie/controller/EmployeeController.java`

```java
package com.curious.reggie.controller;

import com.curious.reggie.service.EmployeeService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/employee")  // 这个是提前约定好的
public class EmployeeController  {

    @Autowired
    private EmployeeService employeeService;

}
```

<font color='red'>上面都创建好了之后，需要去写我们的login方法，在写登录方法之前导入返回结果类R</font> 

**R**：这个类是一个通用类，服务端相应的所有请求结果，最终都会包装乘这种类型返回给前端页面，这个项目把所有的都封装成R了

![image-20221219211858846](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219211858846.png)

通用结果类解析： https://blog.csdn.net/Give_me_a_future/article/details/124579217

`src/main/java/com/curious/reggie/common/R.java`

```java
package com.curious.reggie.common;

import lombok.Data;
import java.util.HashMap;
import java.util.Map;

/**
 * 通用返回结果，服务端响应的数据最终都会封装成此对象
 * @param <T>
 */
@Data
public class R<T> {

    private Integer code; //编码：1成功，0和其它数字为失败

    private String msg; //错误信息

    private T data; //数据

    private Map map = new HashMap(); //动态数据

    public static <T> R<T> success(T object) {
        // 自己new一个R对象，把Employee对象给他这个data
        R<T> r = new R<T>();
        r.data = object;
        r.code = 1;
        return r;
    }

    public static <T> R<T> error(String msg) {
        R r = new R();
        r.msg = msg;
        r.code = 0;
        return r;
    }

    public R<T> add(String key, Object value) {
        this.map.put(key, value);
        return this;
    }

}
```

#### 1.4.4 真正开发登录方法

![image-20221219203517527](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219203517527.png)

![image-20221219215109904](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221219215109904.png)

在**controller**部分添加：

```java
package com.curious.reggie.controller;

import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.curious.reggie.common.R;
import com.curious.reggie.entity.Employee;
import com.curious.reggie.service.EmployeeService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.util.DigestUtils;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletRequest;
import java.nio.charset.StandardCharsets;

@Slf4j
@RestController
@RequestMapping("/employee")  // 这个是提前约定好的
public class EmployeeController  {

    @Autowired
    private EmployeeService employeeService;

    /**
     * 员工登录
     * @param request
     * @param employee
     * @return
     */
    @PostMapping("/login")
    public R<Employee> login(HttpServletRequest request, @RequestBody Employee employee){  // 传的要和我们命名的是一样的

        // 1. 将页面提交的密码password进行md5加密处理
        String password = employee.getPassword();  // 那边需要Data修饰，这里才能get
        password = DigestUtils.md5DigestAsHex(password.getBytes());

        // 2. 根据页面提交的用户名username来查数据库，这里涉及的是Service
        LambdaQueryWrapper<Employee> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(Employee::getUsername, employee.getUsername());  // 条件
        Employee emp = employeeService.getOne(queryWrapper);  // 为什么要用getOne方法，因为对username做唯一了，用Service去查

        // 3. 如果没有查询到则返回登录失败结果
        if(emp == null){
            return R.error("登录失败");  // static方法，不需要实例化了
        }

        // 4. 密码的比对
        if(!emp.getPassword().equals(password)){
            return R.error("登录失败");
        }

        // 5. 密码比对成功了，查看一下员工的状态是不是正常可用的，如果禁用则返回已禁用结果
        if(emp.getStatus() == 0){
            return R.error("账号已禁用");
        }

        // 6. 登录成功，将员工id存入Session并返回登录成功结果
        // 为什么这样的
        request.getSession().setAttribute("employee", emp.getId());
        return R.success(emp);
    }

}
```

```json
// 登录成功
{
    "code": 1,
    "msg": null,
    "data": {
        "id": 1,
        "username": "admin",
        "password": "e10adc3949ba59abbe56e057f20f883e",
        "phone": "13812312312",
        "sex": "1",
        "idNumber": "110101199001010047",
        "status": 1,
        "createTime": [
            2021,
            5,
            6,
            17,
            20,
            7
        ],
        "updateTime": [
            2021,
            5,
            10,
            2,
            24,
            9
        ],
        "createUser": 1,
        "updateUser": 1
    },
    "map": {}
}

// 登录失败
{
    "code": 0,
    "msg": "登录失败",
    "data": null,
    "map": {}
}
```





#### 1.4.5 功能测试







