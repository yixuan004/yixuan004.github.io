---
title: SpringBoot-5整合JUnit
date: 2023-04-10 21:40:04
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot-5整合JUnit

JUnit是执行单元测试使用的，导入坐标，写测试类，并在测试类加一些注解

## 1. JUnit坐标导入

导入JUnit的坐标（可能如果是高版本的SpringBoot就把这个集成了，这样会Junit4）

```xml
<!--junit-->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <scope>test</scope>
</dependency>
```

## 2. Test类里面测Service Controller

首先在这里创建一个test类，和Service名称前缀一样`src/test/java/cn/edu/bupt/aiswitchboard/NameFinderServiceTest.java`

在里面可以测Service，也可以测Controller，自动装配相对应的

<font color='red'>除了预期的输入输出，可能非预期的输入输出也要给写上，包括报出来的错误类型是什么</font> 

```java
package cn.edu.bupt.aiswitchboard;

import cn.edu.bupt.aiswitchboard.common.Response;
import cn.edu.bupt.aiswitchboard.controller.NameFinderController;
import cn.edu.bupt.aiswitchboard.service.NameFinderService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import static org.junit.Assert.assertEquals;

/**
 * NameFinder的测试类，可以测Service也可以测Controller
 */
@RunWith(SpringRunner.class)
@SpringBootTest
public class NameFinderServiceTest {
    @Autowired
    private NameFinderService nameFinderService;

    @Autowired
    private NameFinderController nameFinderController;

    @Test
    public void testNameFinderService() {
        // 除了预期的输入输出，非预期的也要写上，报什么错返回什么，如果能传的话
        Integer a = nameFinderService.test(5);
        assertEquals((Integer) 5, a);

        a = nameFinderService.test(4);
        assertEquals((Integer) 4, a);
    }

    @Test
    public void testNameFinderController() {
        Response<Object> resp = nameFinderController.postJsonInput();
        Response<Object> tmp = new Response<>();
        tmp.update(200, "SUCCESS", null);
        assertEquals(tmp, resp);
    }
}
```