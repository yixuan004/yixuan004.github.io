---
title: JavaSE-5进阶线程单元测试反射注解XML
date: 2023-01-05 00:08:49
tags:
	- javase
categories:
	- develop
	- java
	- javase
---

# JavaSE-5进阶线程单元测试反射注解XML

## 1. 单元测试

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103173036550.png" style="zoom:50%;" />

### 1.1 单元测试

#### 1.1.1 单元测试概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103173347953.png" alt="image-20230103173347953" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103174117722.png" alt="image-20230103174117722" style="zoom:50%;" />

#### 1.1.2 Junit单元测试框架

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103174239199.png" alt="image-20230103174239199" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103174455276.png" alt="image-20230103174455276" style="zoom:50%;" />

#### 1.1.3 单元测试快速入门——步骤

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103174725636.png" alt="image-20230103174725636" style="zoom:50%;" />

- 业务方法

```java
package com.curious.javasepro.d21_junit;

/**
 * 业务方法
 */
public class UserService {
    public String loginName(String loginName, String passWord) {
        if ("admin".equals(loginName) && "123456".equals(passWord)) {
            return "登陆成功";
        } else {
            return "用户名或密码有问题";
        }
    }

    public void selectName() {
        System.out.println(10/0);
        System.out.println("查询全部用户名称成功~~");
    }
}
```

- 测试类

```java
package com.curious.javasepro.d21_junit;

import org.junit.Assert;
import org.junit.Test;

/**
 * 测试类
 */
public class TestUserService {
    /**
     * 测试方法
     * 注意点：
     *  1. 公开、无参数、无返回值
     *  2. 测试方法必须用@Test注解标记
     */
    @Test
    public void testLoginName() {
        UserService userService = new UserService();
        String res = userService.loginName("admin", "123456");

        // 进行预期结果的正确性测试：断言！
        Assert.assertEquals("您的功能业务数据可能出现bug", "登陆成功", res);
    }
}
```

- 在测试类右键直接执行，会有输出显示

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103180251120.png)

- 单独测试这个，失败了

```java
@Test
public void testSelectNames() {
    UserService userService = new UserService();
    userService.selectName();
}
```

![image-20230103180438937](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103180438937.png)

- 在类上，可以执行整体测试

![image-20230103180528110](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103180528110.png)

![image-20230103180549947](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103180549947.png)

#### 1.1.4 单元测试常用注解

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103180721391.png" alt="image-20230103180721391" style="zoom:50%;" />

- 开始执行的方法：初始化资源
- 执行完之后的方法：释放资源

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103181027920.png" alt="image-20230103181027920" style="zoom:50%;" />

## 2. 反射（重点）

### 2.1 反射概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103195035268.png" alt="image-20230103195035268" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103195125963.png" alt="image-20230103195125963" style="zoom:50%;" />

### 2.2 反射的第一步：获取Class类的对象

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103201304436.png" alt="image-20230103201304436" style="zoom:50%;" />

```java
package com.curious.javasepro.d22_reflect;

/**
 * 目标：反射的第一步：获取Class对象
 */
public class Test {
    public static void main(String[] args) throws Exception {
        // 1. Class类中的一个静态方法：forName（权限名：包名 + 类名）
        Class c = Class.forName("com.curious.javasepro.d22_reflect.Student");
        System.out.println(c);  // class com.curious.javasepro.d22_reflect.Student

        // 2. 类名.class
        Class c1 = Student.class;
        System.out.println(c1);  // class com.curious.javasepro.d22_reflect.Student

        // 3. 对象.getClass() 获取对象对应类的Class对象
        Student s = new Student();
        Class c2 = s.getClass();
        System.out.println(c2);  // class com.curious.javasepro.d22_reflect.Student
    }
}
```

### 2.3 反射获取构造器对象

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103201602654.png" alt="image-20230103201602654" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103201613793.png" alt="image-20230103201613793" style="zoom:50%;" />

- Student类，这里的无参构造器是私有的

```java
package com.curious.javasepro.d22_reflect_constructor;

public class Student {
    private String name;
    private int age;

    private Student() {

    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

}
```

- 测试类，用@Test注解

```java
package com.curious.javasepro.d22_reflect_constructor;

import org.junit.Test;

import java.lang.reflect.Constructor;

public class TestStudent01 {
    // 1. getConstructors:
    // 获取全部的构造器：只能获取public修饰的构造器
    // Constructor[] getConstructors()
    @Test
    public void getConstructors() {
        // a. 第一步：获取类对象
        Class c = Student.class;
        // b. 提取类中的全部构造器对象，private的拿不到
        Constructor[] constructors = c.getConstructors();
        // c. 遍历构造器，private的拿不到
        for (Constructor constructor: constructors) {
            System.out.println(constructor.getName() + " ====> " + constructor.getParameterCount());
        }
    }

    // 2. getDeclaredConstructors():
    // 获取全部的构造器：只要你感谢，这里就能拿到，无论是否有权限
    @Test
    public void getDeclaredConstructors() {
        // a. 第一步：获取类对象
        Class c = Student.class;
        // b. 提取类中的全部构造器对象，private的也能拿到
        Constructor[] constructors = c.getDeclaredConstructors();
        // c. 遍历构造器，private的也能拿到
        for (Constructor constructor: constructors) {
            System.out.println(constructor.getName() + " ====> " + constructor.getParameterCount());
        }
    }

    // 3. getConstructor(Class... parameterTypes)
    // 获取某个构造器：只能拿public修饰的某个构造器
    @Test
    public void getConstructor() throws Exception {
        // a. 第一步：获取类对象
        Class c = Student.class;
        // b. 定位单个构造器对象，按照这里的参数进行定位
        // Constructor cons = c.getConstructor();  // 无参的拿不到，因为private
        Constructor cons = c.getConstructor(String.class, int.class);
        // c. 遍历构造器，private的也能拿到
        System.out.println(cons.getName() + " ====> " + cons.getParameterCount());

    }

    // 4. getDeclaredConstructor
    // 获取某个构造器：只要你敢写，这里就能拿到，无论权限是否可及
    @Test
    public void getDeclaredConstructor() throws Exception {
        // a. 第一步：获取类对象
        Class c = Student.class;
        // b. 定位单个构造器对象，按照这里的参数进行定位
        Constructor cons = c.getDeclaredConstructor();  // 无参的也能拿到
        // c. 遍历构造器，private的也能拿到
        System.out.println(cons.getName() + " ====> " + cons.getParameterCount());

    }
}
```

### 2.4 使用反射获取的构造器对象

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103210904327.png" alt="image-20230103210904327" style="zoom:50%;" />

```java
package com.curious.javasepro.d22_reflect_constructor;

import org.junit.Test;

import java.lang.reflect.Constructor;

public class TestStudent02 {
    // 4. getDeclaredConstructor
    // 获取某个构造器：只要你敢写，这里就能拿到，无论权限是否可及
    @Test
    public void getDeclaredConstructor() throws Exception {
        // a. 第一步：获取类对象
        Class c = Student.class;

        // b. 定位单个构造器对象，按照这里的参数进行定位
        Constructor cons = c.getDeclaredConstructor();  // 无参的也能拿到

        // c. private的也能拿到
        System.out.println(cons.getName() + " ====> " + cons.getParameterCount());

        // 遇到私有的构造器，普通的方法拿不到，所以需要暴力
        cons.setAccessible(true);
        Student s = (Student) cons.newInstance();
        System.out.println(s);


        Constructor cons1 = c.getDeclaredConstructor(String.class, int.class);
        Student s1 = (Student) cons1.newInstance("ABC", 12);
        System.out.println(s1);


    }
}
```

> Student{name='null', age=0}
> Student{name='ABC', age=12}

### 2.5 反射获取成员变量对象

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103211902092.png" alt="image-20230103211902092" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103211914597.png" alt="image-20230103211914597" style="zoom:50%;" />

第一步拿到类对象`xxx.class，例如Student.class`相当于拿到所有的代码

- 拿一个

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103212145097.png" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103212202754.png" style="zoom:50%;" />

- 拿多个（根据**名称**定位某一个成员变量）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103212458663.png" alt="image-20230103212458663" style="zoom:50%;" />

### 2.6 使用反射获取的成员变量对象

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103212921920.png" alt="image-20230103212921920" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103212745370.png" alt="image-20230103212745370" style="zoom:50%;" />

### 2.7 使用反射获取方法对象

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103213100420.png" alt="image-20230103213100420" style="zoom:50%;" />

- Dog类

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103213121101.png" alt="image-20230103213121101" style="zoom:50%;" />

- 获取+使用（DeclaredMethods，把私有的也暴力搞出来）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103213348646.png" alt="image-20230103213348646" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103213435737.png" alt="image-20230103213435737" style="zoom:50%;" />

### 2.8 使用反射获取的方法对象

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103213506408.png" alt="image-20230103213506408" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103213633066.png" alt="image-20230103213633066" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103213904741.png" alt="image-20230103213904741" style="zoom:50%;" />

### 2.9 反射的作用——绕过编译阶段为集合添加数据、做企业级通用框架

#### 2.9.1 绕过编译阶段为集合添加数据

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103214334635.png" alt="image-20230103214334635" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103214650415.png" alt="image-20230103214650415" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103214707248.png" alt="image-20230103214707248" style="zoom:50%;" />

#### 2.9.2 通用框架的底层原理

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103214919264.png" alt="image-20230103214919264" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103214958932.png" alt="image-20230103214958932" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103215824320.png" alt="image-20230103215824320" style="zoom:50%;" />

只有反射才能解决这个场景的问题↑

## 3. 注解

### 3.1 注解概述、作用

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230104235518272.png" alt="image-20230104235518272" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230104235552691.png" alt="image-20230104235552691" style="zoom:50%;" />

### 3.2 自定义注解

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230104235734737.png" alt="image-20230104235734737" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230104235934729.png" alt="image-20230104235934729" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230104235951667.png" alt="image-20230104235951667" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105000129335.png" alt="image-20230105000129335" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105000147929.png" alt="image-20230105000147929" style="zoom:50%;" />

### 3.3 元注解

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105000319884.png" alt="image-20230105000319884" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105000642832.png" alt="image-20230105000642832" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105000721611.png" alt="image-20230105000721611" style="zoom:50%;" /> 

### 3.4 注解解析

#### 3.4.1 注解的解析与技巧

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105001132324.png" alt="image-20230105001132324" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105001219611.png" alt="image-20230105001219611" style="zoom:50%;" />

- 技巧

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105001332271.png" alt="image-20230105001332271" style="zoom:50%;" />

#### 3.4.2 注解解析的案例

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105001453600.png" alt="image-20230105001453600" style="zoom:50%;" />

- 解析类的注解

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105001828261.png" alt="image-20230105001828261" style="zoom:50%;" />

- 解析方法的注解

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105001939989.png" alt="image-20230105001939989" style="zoom:50%;" />

### 3.5 注解的应用：模拟junit框架

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105002102512.png" alt="image-20230105002102512" style="zoom:50%;" />

有注解，使用反射机制触发执行

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105002408186.png" alt="image-20230105002408186" style="zoom:50%;" />

**测试必须无参数无返回值**

## 4. 动态代理（重难点）

### 4.1 代理概述，无代理的场景

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105213421163.png" alt="image-20230105213421163" style="zoom:50%;" />

这里还有一种解释是nginx的反向代理，还有VPN的正向代理

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105213451447.png" alt="image-20230105213451447" style="zoom:50%;" />

对对象的行为做一些额外的操作

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105215330693.png" alt="image-20230105215330693" style="zoom:50%;" />

### 4.2 如何创建代理对象

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105215353536.png" alt="image-20230105215353536" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105220020909.png" alt="image-20230105220020909" style="zoom:50%;" />

调用`s2.jump()`，走的流程

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105220350522.png" alt="image-20230105220350522" style="zoom:50%;" />

未来：对象给Spring，然后返回一个更强大的对象

### 4.3 动态代理的应用案例（理解使用动态代理的优势）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105220633382.png" alt="image-20230105220633382" style="zoom:50%;" />

动态代理必须基于接口进行设计

- 接口

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105221135458.png" alt="image-20230105221135458" style="zoom:50%;" />

- `public class UserServiceImpl implements UserService`

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105221039232.png" alt="image-20230105221039232" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105221101460.png" alt="image-20230105221101460" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105221115771.png" alt="image-20230105221115771" style="zoom:50%;" />

- 创建对象解决问题

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105221201583.png" alt="image-20230105221201583" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105221214795.png" style="zoom:50%;" />

- 上面的问题

上面重复代码太多了，前面要干一件事，后面要干一件事，这样用代理提高代码复用性，就不用每次都写一下这个时间统计了

- 代理的解决方案

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105221832582.png" alt="image-20230105221832582" style="zoom:50%;" />

### 4.4 动态代理的优点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105222100663.png" alt="image-20230105222100663" style="zoom:50%;" />

泛型的进一步优化

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105222157166.png" alt="image-20230105222157166" style="zoom:50%;" />

## 5. XML文件（简单了解，未来有接触到再进一步学习）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105222339815.png" alt="image-20230105222339815" style="zoom:50%;" />

### 5.1 XML概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105222505672.png" alt="image-20230105222505672" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105222530148.png" alt="image-20230105222530148" style="zoom:50%;" />

### 5.2 语法规则

- 第一行

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105222927419.png" alt="image-20230105222927419" style="zoom:50%;" />

- 标签规则

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105223034984.png" alt="image-20230105223034984" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105223548534.png" alt="image-20230105223548534" style="zoom:50%;" />

### 5.3 文档约束（文档需要定统一的规则）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105224019727.png" alt="image-20230105224019727" style="zoom:50%;" />

### 5.4 XML解析

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105224507161.png" alt="image-20230105224507161" style="zoom:50%;" />

### 5.5 其他规则，未来使用时候进一步学习了

## 6. 设计模式补充

### 6.1 工厂模式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105224815008.png" alt="image-20230105224815008" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105225120096.png" alt="image-20230105225120096" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105225137586.png" alt="image-20230105225137586" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105225215331.png" alt="image-20230105225215331" style="zoom:50%;" />

只从工厂这一个地方来改就行了，类似个配置文件？

### 6.2 装饰模式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105225445234.png" alt="image-20230105225445234" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230105225933653.png" alt="image-20230105225933653" style="zoom:50%;" />

**一个方法的功能，知识为了增强另一个类的功能**



















