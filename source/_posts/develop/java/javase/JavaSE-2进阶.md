---
title: JavaSE-2进阶
date: 2022-12-30 18:06:56
tags:
	- javase
categories:
	- develop
	- java
	- javase
---

# JavaSE-2进阶

主要也是面向对象的进阶

 <img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230182344169.png" alt="image-20221230182344169" style="zoom:50%;" />

## 1. static

static是静态的意思，可以修饰成员变量和成员方法

### 1.1 static修饰成员变量

static修饰成员变量，表示该成员变量只在内存中存储一份，可以被共享访问、修改

静态成员变量，推荐直接用`类名.静态成员变量`访问

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230210208212.png" alt="image-20221230210208212" style="zoom:50%;" />

```java
package com.curious.javasepro.d1_staticdemo;

public class User {
    // 静态成员变量，内存中只有一份
    public static int onlineNumber;
}
```

```java
package com.curious.javasepro.d1_staticdemo;

public class StaticFieldDemo1 {
    public static void main(String[] args) {
        User.onlineNumber += 1;
        User.onlineNumber += 1;
        User.onlineNumber += 1;

        User usr = new User();
        System.out.println(usr.onlineNumber);  // 3，但是不推荐用对象去访问
    }
}
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230210952009.png" alt="image-20221230210952009" style="zoom:50%;" />

### 1.2 static修饰成员方法

这个和python的`@staticmethod`很像，如果是static修饰的，就可以直接用类名来调用，而不用实例化对象再来访问

静态成员方法：有static修饰，归属于雷，可以被共享访问，用类名或者对象名都可以访问

 ```java
 public static int getMax(int a, int b) {
     xxxx
 }
 ```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230212227329.png" alt="image-20221230212227329" style="zoom:50%;" />

**<font color='red'>静态方法不能出现this关键字，因为没有对象</font>** 

### 1.3 static应用知识：工具类

 一次编写，处处可用，可以定义一个如`Tools.java`的类

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230213339208.png" alt="image-20221230213339208" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230213359498.png" alt="image-20221230213359498" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230213846331.png" alt="image-20221230213846331" style="zoom:50%;" />

<font color='red'>由于工具类无需创建对象，可以将工具类的构造器进行私有</font> **专业！**

```java
public class Tools {
    private Tools() {
        
    }
    
    public static void show() {
        xxxx;
    }
}
```

### 1.4 static应用知识：代码块（静态代码块、实例代码块）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230214543902.png" alt="image-20221230214543902" style="zoom:50%;" />

#### 1.4.1 静态代码块

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230215228356.png" alt="image-20221230215228356" style="zoom:50%;" />

```java
package com.curious.javasepro.d3_staticcode;

public class StaticDemo1 {

    /**
     * 静态代码块：有static修饰，属于类，与类一起优先加载一次，自动出发执行
     * 作用：可以用于初始化静态资源
     */
    static {
        System.out.println("-----静态代码块被触发执行了-----");
    }

    public static void main(String[] args) {
        System.out.println("-----main方法执行-----");
    }
}
-----静态代码块被触发执行了-----
-----main方法执行-----
```

看到这里的话，还是不是很了解静态代码块的作用，真的开发真的会用到这种操作吗？ 

```java
package com.curious.javasepro.d3_staticcode;

import java.util.ArrayList;

public class StaticDemo1 {

    public static int onlineNumber;
    public static ArrayList<String> cards = new ArrayList<>();

    /**
     * 静态代码块：有static修饰，属于类，与类一起优先加载一次，自动出发执行
     */
    static {
        System.out.println("-----静态代码块被触发执行了-----");
        onlineNumber = 0;
        cards.add("AAA");
        cards.add("BBB");
    }

    public static void main(String[] args) {
        System.out.println("-----main方法执行-----");
        System.out.println(onlineNumber);
    }
}

```

#### 1.4.2 实例代码块

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230215240476.png" alt="image-20221230215240476" style="zoom:50%;" />

每次创建对象的时候，会用到

#### 1.4.3 一个静态代码块的例子

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230215655282.png" alt="image-20221230215655282" style="zoom:50%;" />

```java
package com.curious.javasepro.d3_staticcode;

import java.util.ArrayList;

public class StaticTest3 {


    /**
     * 1. 定义一个静态集合，这个集合只加载一个。当前房间只要一副牌
     */
    public static ArrayList<String> cards = new ArrayList<>();  // 共享应该公开

    /**
     * 2. 在程序真正运行main方法之前，把54张牌放进去，后续游戏可以使用了
     * （定义在这里的，只跑一次就可以了）
     * （放在这里也会比较专业）
     */
    static {
        // 3. 正式做牌，放到集合里面去
        // 3.1 定义一个数组存储全部点数
        String[] sizes = {"3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A", "2"};
        // 3.2 定义花色
        String[] colors = {"♥", "♠", "♦", "♣"};
        // 3.3
        for (int i = 0; i < sizes.length; i++) {
            for (int j = 0; j < colors.length; j++) {
                String card = sizes[i] + colors[j];
                cards.add(card);
            }
        }
        cards.add("小王");
        cards.add("大王");
    }

    public static void main(String[] args) {
        System.out.println(cards);
    }
}
```

<font color='red'>工具类，有时候会用到一些字典什么的加载，可能比较慢，要用这种写法？</font> 

### 1.5 static应用知识：单例设计模式

<font color='red'>设计模式是一个非常关键的部分，面试非常常问的内容</font> 

开发中经常遇到一些问题，一个问题通常有n个解法，其中有一种解法是相对比较优的，这个最优的解法被总结出来了，称为设计模式

<font color='red'>**单例模式**</font> 

- 可以保证系统中，应用该模式的这个类永远只有一个实例，即一个类永远只能创建一个对象

- 例如任务管理器对象，我们只需要一个就可以解决问题了，可以节省内存空间

- 饿汉单例模式、懒汉单例模式

#### 1.5.1 饿汉单例模式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231001219695.png" style="zoom:50%;" />

实现：

SingleInstance.java

```java
package com.curious.javasepro.d4_singleinstance;

/**
 * 饿汉单例模式
 */
public class SingleInstance {

    /**
     * 2. 饿汉单例是在获取对象前，对象已经提前准备好了一个
     * 这个对象只能是一个，所以静态成员变量
     */
    public static SingleInstance instance = new SingleInstance();

    /**
     * 1. 必须把构造器私有化
     */
    private SingleInstance() {

    }

}
```

Test.java

```java
package com.curious.javasepro.d4_singleinstance;

public class Test1 {
    public static void main(String[] args) {
        // 目标：理解饿汉单例的设计步骤
        SingleInstance s1 = SingleInstance.instance;
        SingleInstance s2 = SingleInstance.instance;
        System.out.println(s1==s2);  // true，地址一样
    }
}
```

#### 1.5.2 懒汉单例模式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231001924959.png" alt="image-20221231001924959" style="zoom:50%;" />

实现

```java
package com.curious.javasepro.d4_singleinstance;

public class SingleInstance2 {

    private static SingleInstance2 instance;  // 这里不能初始化，否则成饿汉单例，只有一份（静态），初始化null。私有化避免给别人挖坑

    private SingleInstance2() {
    }

    public static SingleInstance2 getInstance() {
        if (instance == null) {
            instance = new SingleInstance2();
        }
        return instance;
    }
}
```

```java
package com.curious.javasepro.d4_singleinstance;

public class Test2 {
    public static void main(String[] args) {
        SingleInstance2 s1 = SingleInstance2.getInstance();
        SingleInstance2 s2 = SingleInstance2.getInstance();

        System.out.println(s1);
        System.out.println(s2);
    }
}
```

#### 1.5.3 单例模式在系统中是怎么使用的？以环保通系统为例

TODO

## 2. 继承

**面向对象的重要特征：继承**

### 2.1 继承概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231105057620.png" alt="image-20221231105057620" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231105555097.png" alt="image-20221231105555097" style="zoom:75%;" />

使用继承最好的是可以提高代码的复用性，比如人和老师都有一些基本的年龄名称这些的，但是学生是学习，老师是教课这样的hhh

**<font color='red'>子类可以用this，子类用父类的可以用super关键字</font>** 

### 2.2 继承的设计规范

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231110030797.png" alt="image-20221231110030797" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231110043507.png" alt="image-20221231110043507" style="zoom:50%;" />

 ### 2.3 继承的特点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231111653903.png" alt="image-20221231111653903" style="zoom:50%;" />

1）感觉第一点比较关键，子类和父类有各自的构造器，子类不能继承父类的构造器（说实话，感觉在自己的开发经验里，继承好像用的比较少，因为从开始的设计规范来说一般不会那么规范？）

2）子类可以简单说不能继承父类私有的（各有各的解释，但是从实现来说是不能继承，因为用不了，或者说不建议）

3）子类不是继承父类的静态成员，因为静态成员只有一份（一种共享的行为）

### 2.4 继承后：成员变量、成员方法访问特点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231112416710.png" alt="image-20221231112416710" style="zoom:50%;" />

### 2.5 继承后：方法重写

<font color='red'>注意这里是重写，以前是重载</font> 

```java
package com.curious.javasepro.d5_extends;

public class Test {
    public static void main(String[] args) {
        Student stu = new Student();
        stu.run();
    }
}
```

父类

```java
package com.curious.javasepro.d5_extends;

public class People {
    public void run() {
        System.out.println("父类人会跑");
    }
}
```

子类

```java
package com.curious.javasepro.d5_extends;

public class Student extends People {
    public void run() {
        super.run();  // 先用父类的
        System.out.println("子类人会跑");
    }
}
```

> 父类人会跑
> 子类人会跑

#### 2.5.1 @Override重写注解

@Override是放在重写的方法上，作为重写是否正确的校验注解

```java
package com.curious.javasepro.d5_extends;

public class Student extends People {
    @Override  // 重写校验注解，加上了之后，这个方法必须是正确重写的；提高可读性，代码优雅
    public void run() {
        super.run();  // 先用父类的
        System.out.println("子类人会跑");
    }
}
```

 <img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231113604102.png" alt="image-20221231113604102" style="zoom:50%;" />

#### 2.5.2 方法重写注意事项和要求

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231114431613.png" alt="image-20221231114431613" style="zoom:50%;" />

### 2.6 继承后：子类构造器的特点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231153430498.png" alt="image-20221231153430498" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231153901008.png" alt="image-20221231153901008" style="zoom:50%;" />

### 2.7 this和super的总结

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231154219479.png" alt="image-20221231154219479" style="zoom:50%;" />

## 3. 语法

### 3.1 包

#### 3.1.1 建包

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231161540595.png" alt="image-20221231161540595" style="zoom:50%;" />

#### 3.1.2 导包

只要不是相同类下的，就要去导包用

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231161934200.png" alt="image-20221231161934200" style="zoom:50%;" />

### 3.2 权限修饰符

#### 3.2.1 什么是权限修饰符

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231162037489.png" alt="image-20221231162037489" style="zoom:50%;" />

#### 3.2.2 权限修饰符的分类和具体作用范围

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231162054905.png" alt="image-20221231162054905" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231162111442.png" alt="image-20221231162111442" style="zoom:50%;" />

#### 3.2.3 权限修饰符使用规范

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231162748778.png" alt="image-20221231162748778" style="zoom:50%;" />

### 3.3 final

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231162932022.png" alt="image-20221231162932022" style="zoom:50%;" />

- 工具类可以加final修饰

- 修饰一些全局变量？：Cannot assign a value to final variable 'CONS'，常量，固定的值

```java
final double z;
public final void method(int aaa) {
    
}
```

### 3.4 常量

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231163926758.png" alt="image-20221231163926758" style="zoom:50%;" />

 <img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231164103679.png" alt="image-20221231164103679" style="zoom:50%;" />

```java
public static final String SCHOOL_NAME = "Curious";
```

一些配置文件做成常量

### 3.5 枚举（用的少）

#### 3.5.1 枚举的特点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231165116827.png" alt="image-20221231165116827" style="zoom:50%;" />

![image-20221231165443713](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231165443713.png)

#### 3.5.2 枚举的作用

做信息分类技术

```java
package com.curious.javasepro.d7_enum;

public class Test {
    public static void main(String[] args) {
        method(Season.SPRING);
    }
    public static void method(Season s) {
        switch (s) {
            case SPRING:
                System.out.println("春天");
                break;
            case SUMMER:
                System.out.println("夏天");
                break;
            case AUTUMN:
                System.out.println("求太难");
                break;
            case WINTER:
                System.out.println("冬天");
                break;
        }
    }
}
```

```java
package com.curious.javasepro.d7_enum;

/**
 * 做信息标志和分类
 */
public enum Season {
    // 枚举的第一行必须罗列枚举类的对象名称，建议全部大写
    SPRING, SUMMER, AUTUMN, WINTER;
}

```

### 3.6 抽象类

#### 3.6.1 概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231170336026.png" alt="image-20221231170336026" style="zoom:50%;" />

```java
public abstract class Animal {
    public abstract void run();
}
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231171521567.png" alt="image-20221231171521567" style="zoom:50%;" />

抽象类一般是父类，让子类来继承

#### 3.6.2 抽象类使用场景

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231171559184.png" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231172517835.png" alt="image-20221231172517835" style="zoom:50%;" />

#### 3.6.3 抽象类的特征、注意事项小结

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231172900396.png" alt="image-20221231172900396" style="zoom:50%;" />

抽象类不能创建对象，并且继承了抽象类的必须重写抽象类的全部方法

#### 3.6.4 设计模式（抽象类相关）——模板方法模式（重点）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231173354028.png" alt="image-20221231173354028" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231173419495.png" alt="image-20221231173419495" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231174003940.png" alt="image-20221231174003940" style="zoom:50%;" />

**<font color='red'>建议模板，write那块，用final修饰</font>** 

## 4. 接口

### 4.1 接口的定义与特点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231174422802.png" alt="image-20221231174422802" style="zoom:50%;" />

```java
package com.curious.javasepro.d8_interface;

public interface InterfaceDemo {
    // 目标：接口中的成分特点
    // 1. 常量
    public static final String SCHOOL_NAME = "ABC大学";

    // 2. 抽象方法
    // 注意，由于接口都体现的是规范思想，所以代码层面可以把修饰的都干掉
    public abstract void run();
    public abstract void eat();
}
```

简化后

```java
package com.curious.javasepro.d8_interface;

public interface InterfaceDemo {
    // 目标：接口中的成分特点
    // 1. 常量
    String SCHOOL_NAME = "ABC大学";

    // 2. 抽象方法
    // 注意，由于接口都体现的是规范思想，所以代码层面可以把修饰的都干掉
    void run();
    void eat();
}
```

### 4.2 接口的基本使用，接口与类的关系：被类实现（可以多实现）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231174858108.png" alt="image-20221231174858108" style="zoom:50%;" />

接口可以多实现，实现的时候必须完成上面所有的规定，也是用@Override进行修饰

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231175046855.png" alt="image-20221231175046855" style="zoom:50%;" />

多实现的例子

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231175223383.png" alt="image-20221231175223383" style="zoom:50%;" />

### 4.3 接口与接口的关系：多继承

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231175318294.png" alt="image-20221231175318294" style="zoom:50%;" />

### 4.4 JDK8接口新增方法、注意事项

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231175620955.png" alt="image-20221231175620955" style="zoom:50%;" />

项目Version2.0需要对Inter接口丰富，加入10个新的抽象方法，此时改了接口就要所有实现类实现这些方法

——牵一发而动全身

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231175738155.png" alt="image-20221231175738155" style="zoom:50%;" />

#### 4.4.1 第一种：默认方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231175757440.png" alt="image-20221231175757440" style="zoom:50%;" />

#### 4.4.2 第二种：静态方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231175822268.png" style="zoom:50%;" />

#### 4.4.3 第三种：私有方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231175843555.png" alt="image-20221231175843555" style="zoom:50%;" />

## 5. 多态

### 5.1 多态的概述

Animal可以是个抽象类

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231213525170.png" alt="image-20221231213525170" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231213740475.png" alt="image-20221231213740475" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231213843447.png" alt="image-20221231213843447" style="zoom:50%;" />

### 5.2 多态的优势

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231214510437.png" alt="image-20221231214510437" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231214520085.png" alt="image-20221231214520085" style="zoom:50%;" />

### 5.3 多态下引用数据类型的类型转换

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231214721807.png" alt="image-20221231214721807" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231214855417.png" alt="image-20221231214855417" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231214919375.png" style="zoom:50%;" />

## 6. 内部类

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231220120970.png" alt="image-20221231220120970" style="zoom:50%;" />



<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231220133074.png" alt="image-20221231220133074" style="zoom:50%;" />

### 6.1 静态内部类（简单，了解）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231220354986.png" alt="image-20221231220354986" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231220510053.png" alt="image-20221231220510053" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231220628855.png" alt="image-20221231220628855" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231220901564.png" alt="image-20221231220901564" style="zoom:50%;" />

### 6.2 成员内部类（了解）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231220944226.png" alt="image-20221231220944226" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231221224124.png" alt="image-20221231221224124" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231221729194.png" alt="image-20221231221729194" style="zoom:50%;" />

### 6.3 局部内部类（了解）

TODO

### 6.4 匿名内部类（重点）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231222143994.png" alt="image-20221231222143994" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231222227333.png" alt="image-20221231222227333" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231222357122.png" alt="image-20221231222357122" style="zoom:50%;" />

```java
Animal a = new Animal() {
    @Override
    public void run() {
        xxxxxx;
    }
}
```

Animal是个抽象类















































