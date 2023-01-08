---
title: JavaSE-3进阶API以及一些刷题语法
date: 2022-12-31 22:36:10
tags:
	- javase
categories:
	- develop
	- java
	- javase
---

# JavaSE-3进阶API以及一些刷题语法

## 1. 常用API

### 1.1 Object

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231231933179.png" alt="image-20221231231933179" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231231957223.png" alt="image-20221231231957223" style="zoom:50%;" />

```java
package com.curious.javase.arraylist;

import java.util.ArrayList;

public class Test {
    public static void main(String[] args) {
        ArrayList<Integer> nums = new ArrayList<>();
        nums.add(1);
        nums.add(2);

        ArrayList<Integer> nums2 = new ArrayList<>();
        nums2.add(1);
        nums2.add(2);

        System.out.println(nums.toString());  // [1, 2]
        System.out.println(nums.equals(nums2));  // true
    }
}
```

#### 1.1.1 toString()方法（类中重写比较好使）

```java
package com.curious.javasepro.d9_api_object;

public class Test1 {
    public static void main(String[] args) {
        Student stu = new Student("Curious");
        //
        System.out.println(stu.toString());
        // 直接输出也是一样的
        System.out.println(stu);
    }
}
```

```java
package com.curious.javasepro.d9_api_object;

public class Student {
    private String name;

    Student() {}

    Student(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231232300742.png" alt="image-20221231232300742" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231232314608.png" alt="image-20221231232314608" style="zoom:50%;" />

- 在Student中重写

```java
package com.curious.javasepro.d9_api_object;

public class Student {
    private String name;

    Student() {}

    Student(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Student {name: " + name + '}';
    }
}
```

#### 1.1.2 equals()方法（可以用generate自动生成的）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231232821152.png" alt="image-20221231232821152" style="zoom:50%;" />

 equals和上面的差不多，默认是比较两个对象的地址是否相同

```java
package com.curious.javasepro.d9_api_object;

public class Test1 {
    public static void main(String[] args) {
        Student stu = new Student("Curious");
        //
        System.out.println(stu.toString());
        // 直接输出也是一样的
        System.out.println(stu);

        Student stu2= new Student("Curious");
        System.out.println("stu.equals(stu2): " + stu.equals(stu2));  // false

    }
}
```

但是如果比较地址相同的话，完全可以用==来比的，所以这里提供的equals同样是为了重写的

**<font color='red'>这里是多态的一个使用方法</font>** 

```java
@Override
public boolean equals(Object o) {
    // 1. 判断o是不是学生类型
    if (o instanceof Student) {
        if (this.name == ((Student) o).name) {
            return true;
        } else {
            return false;
        }
    } else {
        return false;
    }
}
```

官方的

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Student student = (Student) o;
    return Objects.equals(name, student.name);
}
```

### 1.2 Objects

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231234436064.png" alt="image-20221231234436064" style="zoom:50%;" />

#### 1.2.1 equals()

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231233901087.png" alt="image-20221231233901087" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231233933456.png" alt="image-20221231233933456" style="zoom:50%;" />

上面那个Objects.equals，这块避免了null.equals()这样的操作

<font color='red'>如下面这样，可能存在问题</font> 

```java
package com.curious.javasepro.d10_api_object;

public class Test {
    public static void main(String[] args) {
        String s1 = null;
        String s2 = new String("Curious");
        System.out.println(s1.equals(s2));
    }
}
```

所以进行一个修改

```java
package com.curious.javasepro.d10_api_object;

import java.util.Objects;

public class Test {
    public static void main(String[] args) {
        String s1 = null;
        String s2 = new String("Curious");
//        System.out.println(s1.equals(s2));
        System.out.println(Objects.equals(s1, s2));  // 安全的
    }
}
```

看源代码的话，有这样的操作`(a != null && a.equals(b))`，有熔断操作

#### 1.2.2 isNull

```java
System.out.println(Objects.isNull(s1));
System.out.println(s1 == null);
```

### 1.3 StringBuilder（刷题重要）

 <img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231234826852.png" alt="image-20221231234826852" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231234840663.png" alt="image-20221231234840663" style="zoom:50%;" />

#### 1.3.1 StringBuilder常用方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221231234939550.png" alt="image-20221231234939550" style="zoom:50%;" />

目标：学会使用StringBuilder，并知道性能好的原因

```java
package com.curious.javasepro.d11_api_stringbuilder;

/**
 * 目标：学会使用
 */
public class StringBuilderDemo01 {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        sb.append("a");
        sb.append("b");
        sb.append(1);
        sb.append(false);
        sb.append(3.3);
        System.out.println(sb.toString());  // ab1false3.3
		System.out.println(sb.reverse());  // 无返回值，但是会翻转了 3.3eslaf1ba
        System.out.println(sb.length()); // 11
        
    }
}
```

### 1.4 Math

执行基本的数学运算，没有提供公开的构造器，一种**工具类**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101102727392.png" alt="image-20230101102727392" style="zoom:50%;" />

这里的`Math.pow`根据chatGPT的搜索结果，是快速幂算法

### 1.5 System

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101113815915.png" style="zoom:50%;" />

### 1.6 BigDecimal（用于解决浮点型运算精度失真的问题）

一些刷题的时候经常被这个卡了，浮点数精度

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101160655914.png" alt="image-20230101160655914" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101161302182.png" alt="image-20230101161302182" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101161353202.png" alt="image-20230101161353202" style="zoom:50%;" />

```java
BigDecimal bd1 = BigDecimal.valueOf(0.1);
BigDecimal bd2 = BigDecimal.valueOf(0.2);
BigDecimal ress = bd1.add(bd2);
System.out.println("ress: " + ress);
System.out.println(ress.getClass().getName());  // BigDecimal
System.out.println(ress.doubleValue());  // 转成double类型了
```

### 1.7 BigInteger（大数操作，一些大数加法问题，不过题目也会避免）

这个就是大数操作，基本都是用字符串来实现的

```java
BigInteger a = new BigInteger("9999999999999999999999999999");
BigInteger b = new BigInteger("9999999999999999999999999999");
System.out.println("a.add(b).toString(): " + a.add(b).toString());
```

## 2. 一批新的API介绍（日期与时间）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101163023319.png" alt="image-20230101163023319" style="zoom:50%;" />

### 2.1 日期与时间（大概了解，用的时候会查就行）

#### 2.1.1 Date类

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101163523097.png" alt="image-20230101163523097" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101163530970.png" alt="image-20230101163530970" style="zoom:50%;" />

```java
package com.curious.javasepro.d12_useful_api;

import java.util.Date;

public class Test01 {
    public static void main(String[] args) {
        Date d = new Date();
        System.out.println(d);  // Sun Jan 01 16:34:58 CST 2023
        System.out.println(d.getTime());  // 获取时间毫秒值 1672562137795
    }
}
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101163636755.png" alt="image-20230101163636755" style="zoom:50%;" />

```java
// 1. 得到当前时间的事件毫秒值
long time2 = System.currentTimeMillis();
time2 += (60 * 60 + 121) * 1000;
Date d2 = new Date(time2);
System.out.println(d2);  // 当前时间往后走一些时间
```

#### 2.1.2 SimpleDateFormat

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101163929489.png" alt="image-20230101163929489" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101164013551.png" alt="image-20230101164013551" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101164251001.png" alt="image-20230101164251001" style="zoom:50%;" />

#### 2.1.3 Calendar概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101165248519.png" alt="image-20230101165248519" style="zoom:50%;" />

#### 2.1.4 JDK8开始新增日期API

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101165902222.png" alt="image-20230101165902222" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101165928657.png" alt="image-20230101165928657" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101170440522.png" alt="image-20230101170440522" style="zoom:50%;" />

## 3. 包装类（Integer、Character、Boolean等等）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101171029863.png" alt="image-20230101171029863" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101171043062.png" alt="image-20230101171043062" style="zoom:50%;" />

**Java：一切皆对象**

```java
int a = 10;
Integer a1 = 11;
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101171204722.png" alt="image-20230101171204722" style="zoom:50%;" />

```java
Integer t1 = 1;
int t2 = 100;
System.out.println(t1+t2);  // 101
```

## 4. 正则表达式（暂时了解就可以，真正用到可以搜索使用）

### 4.1 matches，返回boolean

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101171912182.png" alt="image-20230101171912182" style="zoom:50%;" />

```java
package com.curious.javasepro.d13_regular_expression;

public class Test {
    public static void main(String[] args) {
        String qq = "123456789";
        boolean flag = qq.matches(qq);
        System.out.println(flag);  // true
    }

    public static boolean checkQQ(String qq) {
        return qq != null && qq.matches("\\d{6, 20}");
    }
}
```

### 4.2 编译后匹配

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101174353935.png" alt="image-20230101174353935" style="zoom:50%;" />

## 5. Arrays（重点）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101174426604.png" alt="image-20230101174426604" style="zoom:50%;" />

```java
package com.curious.javasepro.d14_arrays;

import java.util.Arrays;

public class ArraysDemo1 {
    public static void main(String[] args) {
        // 目标：学会使用Arrays类的常用API，并理解其原理
        int[] arr = {10, 2, 55, 23, 24, 100};
        System.out.println(arr);
        System.out.println(Arrays.toString(arr));  // 1. 返回数组内容

        // 2. 排序API，默认对数组进行升序排序
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));

        // 3. 二分搜索基础（前提是数组必须排序好，否则出问题）
        int index = Arrays.binarySearch(arr, 2);  // 如果有返回index，负数说明不存在
    }
}
```

### 5.1 Arrays类对于Comparator的支持

#### 5.1.1 Integer[]进行降序排序（支持引用类型）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101175134477.png" alt="image-20230101175134477" style="zoom:50%;" />

```java
package com.curious.javasepro.d14_arrays;

import java.util.Arrays;
import java.util.Comparator;

public class ArraysDemo2 {
    public static void main(String[] args) {
        // 目标：自定义数组的排序规则：Comparator比较器对象
        // 1. Arrays的sort方法对于有值特性的数组是默认升序排序
        int[] nums = {34, 12, 42, 23};
        Arrays.sort(nums);
        System.out.println(Arrays.toString(nums));

        // 2. 需求：降序排序（自定义比较器对象，只能支持引用类型的排序）
        Integer[] nums2 = {34, 12, 42, 23};
        /**
         * 参数一：被排序的数组，必须是引用类型
         * 参数二：匿名内部类的对象，代表了一个比较器对象
         */
        Arrays.sort(nums2, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                // 指定比较规则，注意返回值是int
//                if (o1 > o2) {
//                    return 1;
//                } else if (o1 < o2) {
//                    return -1;
//                }
//                return 0;
                // return o1 - o2;
                return o2 - o1;
            }
        });
        System.out.println(Arrays.toString(nums2));
    }
}
```

<font color='red'>这里有问题是，必须得转成引用类型？那么平常的int是咋排序啊</font> 

#### 5.1.2 对学生类进行定义条件的排序（double String也进入比较了）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101180455822.png" alt="image-20230101180455822" style="zoom:50%;" />

字符串的的话，用`compareTo`

![image-20230101180724771](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101180724771.png)

```java
String a1 = "1234";
String a2 = "2345";
a1.compareTo(a2);
```

## 6. Lambda表达式

- 作用：简化匿名内部类的代码写法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101181329301.png" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101181341596.png" alt="image-20230101181341596" style="zoom:50%;" />

```java
package com.curious.javasepro.d15_lambda;

public class LambdaDemo2 {
    public static void main(String[] args) {
        // 目标：学会使用Lambda的标准格式简化匿名内部类的代码形式
        // Lambda只能简化接口中只有一个抽象方法的匿名内部类形式

        // 复杂版本
        Swimming s1 = new Swimming() {
            @Override
            public void swim() {
                System.out.println("游泳游的很好");
            }
        };

        // 简化版本
        Swimming s2 = () -> {
            System.out.println("游游泳的很好");
        };

    }

    public static void go(Swimming s) {
        System.out.println("开始。。。");
        s.swim();
        System.out.println("结束。。。");

    }
}

@FunctionalInterface  // 一旦加上接口，必须是函数式接口，只能有一个抽象方法
interface Swimming {
    void swim();
}
```

### 6.1 lambda表达式简化Comparator接口的匿名形式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101182010789.png" alt="image-20230101182010789" style="zoom:50%;" />

```java
Integer[] nums = {5, 4, 3, 2, 1};
Arrays.sort(nums, (Integer o1, Integer o2) -> {
    return o1 - o2;
});
System.out.println(Arrays.toString(nums));
```

进一步简化

```java
Integer[] nums = {5, 4, 3, 2, 1};
Arrays.sort(nums, (o1, o2) -> {
    return o1 - o2;
});
System.out.println(Arrays.toString(nums));
```

## 7. 集合（Collections相较于List等集合，通用性更高->方法更少，是上层）

集合和数组都是容器，但是集合不定长一些特点，都让实际场景的集合应用更加广泛一些

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101203914844.png" alt="image-20230101203914844" style="zoom:50%;" />

<font color='red'>接口被类实现！</font> 

### 7.1 Collection集合的体系特点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101201242918.png" alt="image-20230101201242918" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101201335541.png" alt="image-20230101201335541" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101201536482.png" alt="image-20230101201536482" style="zoom:50%;" />

### 7.2 Collection集合常用API（Collection感觉只有几个，而List更多一些？）

关键：https://blog.csdn.net/HanTangSongMing/article/details/27080151

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101203858855.png" style="zoom:50%;" /> 

**<font color='red'>这里发现，使用Collection的，不能用.get，但是前后都是ArrayList的就可以，不知道这个地方是什么原理</font>** 

#### 7.2.1 QA补充（chatGPT，重要）

- `Collections<Integer> c = new ArrayList<>()`和`ArrayList<Integer> c = new ArrayList<>()`的区别

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101203440283.png" alt="image-20230101203440283" style="zoom:50%;" />

#### 7.2.2 QA补充2（chatGPT）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101211218290.png" alt="image-20230101211218290" style="zoom:50%;" />

#### 7.2.3 QA补充3（关键！！！）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101211410795.png" alt="image-20230101211410795" style="zoom:50%;" />



**<font color='red'>ArrayList对象实现了Collection接口！！！！方法是从接口中获取的方法！！！！</font>** 

### 7.3 Collection集合的遍历方式

#### 7.3.1 迭代器（while循环，it.hasNext()，it.next()）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101204146295.png" alt="image-20230101204146295" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101204157673.png" alt="image-20230101204157673" style="zoom:50%;" />

```java
package com.curious.javasepro.d16_collection;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

public class CollectionDemo2 {
    public static void main(String[] args) {
        Collection<Integer> c = new ArrayList<>();
        c.add(1);
        c.add(2);
        c.add(3);
        c.add(4);

        // 1. 得到当前集合的迭代器对象
        Iterator<Integer> it = c.iterator();
        // int a = it.next();  // 1，单独走一个
        // System.out.println(a);

        // 2. 定义while循环，遍历集合
        while (it.hasNext()) {
            int tmp = it.next();
            System.out.println(tmp);
        }  // 1 2 3 4都会一个个的输出
    }
}
```

#### 7.3.2 foreach/增强for循环（简单很多）

```java
package com.curious.javasepro.d16_collection;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

public class CollectionDemo2 {
    public static void main(String[] args) {
        Collection<Integer> c = new ArrayList<>();
        c.add(1);
        c.add(2);
        c.add(3);
        c.add(4);

        for (Integer a: c) {
            System.out.println(a);
        }
    }
}
```

**循环的过程中，修改没有作用！！修改无意义，和python那个很像**

#### 7.3.3 lambda表达式

```java
package com.curious.javasepro.d16_collection;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;
import java.util.function.Consumer;

public class CollectionDemo2 {
    public static void main(String[] args) {
        Collection<Integer> c = new ArrayList<>();
        c.add(1);
        c.add(2);
        c.add(3);
        c.add(4);

        c.forEach(new Consumer<Integer>() {
            @Override
            public void accept(Integer integer) {
                System.out.println(integer);
            }
        });
    }
} 
```

## 8. 集合（List集合系列，Collection的子接口）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101201335541.png" alt="image-20230101201335541" style="zoom:50%;" />

`List -> ArrayList<>()和LinkedList()`

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101212156627.png" alt="image-20230101212156627" style="zoom:50%;" />

ArrayList就是数组，查询更快，但是插入删除比较慢

LinkedList就是链表，插入删除更快，但是查询比较慢

### 8.1 ArrayList

**经典代码，多态写法！！！！！**

```java
List<String> list = new ArrayList<>();
```

#### 8.1.1 转换为数组(chatGPT yyds)

要将一个 `List<String>` 转化为一个 `String` 类型的数组，可以使用 `List` 类的 `toArray()` 方法，它返回一个包含列表中所有元素的数组。

```java
List<String> list = new ArrayList<>();
list.add("apple");
list.add("banana");
list.add("orange");

String[] array = list.toArray(new String[0]);
```

上面的代码会将 `list` 中的元素转化为一个 `String` 类型的数组。

注意：`toArray()` 方法的参数是一个指定类型的数组，并且返回的是指定类型的数组。因此，你需要传入一个空的指定类型的数组，如 `new String[0]`，以便返回一个指定类型的数组。

```java
List<String> list = new ArrayList<>();
list.add("AAA");
list.add("BBB");
list.add("CCC");
String[] res = list.toArray(new String[0]);

System.out.println(res[0]);
System.out.println(Arrays.toString(res));
```

### 8.2 LinkedList（有一些独有的，可以完成队列和栈，独有功能不能用多态写）

```java
List<String> list = new LinkedList<>();
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101213644631.png" alt="image-20230101213644631" style="zoom:50%;" />

这里更有一些新的，但是必须得不能用多态写

```java
LinkedList<String> list = new LinkedList<>();
```

#### 8.2.1 实现stack

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101214314131.png" alt="image-20230101214314131" style="zoom:50%;" />

#### 8.2.2 实现queue

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101214326719.png" alt="image-20230101214326719" style="zoom:50%;" />

### 8.3 List集合的遍历方式小结

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101213011917.png" style="zoom:50%;" />

```java
List<String> list = new ArrayList<>();
list.add("AAA");
list.add("BBB");
list.add("CCC");

for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}
```

### 8.4 遍历删除（迭代器用it删，for就从后往前删）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101215031786.png" alt="image-20230101215031786" style="zoom:50%;" />

 ## 9. 泛型深入（未来还要进一步随着项目增强）

### 9.1 自定义泛型类MyArrayList

```java
public class MyArrayList<E> {
    xxx
}
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101220418690.png" alt="image-20230101220418690" style="zoom:50%;" />

拓展，做功能（偷鸡哈哈哈哈哈）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101220630097.png" alt="image-20230101220630097" style="zoom:50%;" />

### 9.2 自定义泛型方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101220856448.png" alt="image-20230101220856448" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101220906235.png" alt="image-20230101220906235" style="zoom:50%;" />



<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101221119526.png" alt="image-20230101221119526" style="zoom:50%;" />

### 9.3 泛型接口

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101221758375.png" alt="image-20230101221758375" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101221810061.png" alt="image-20230101221810061" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101221851869.png" alt="image-20230101221851869" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101221939178.png" alt="image-20230101221939178" style="zoom:50%;" />

## 10. 集合（Set）

### 10.1 Set系列概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101201335541.png" alt="image-20230101201335541" style="zoom:50%;" />

这个set和python那么理解就行了，LinkedHashSet是有序的

- **家族类特点**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101223350369.png" alt="image-20230101223350369" style="zoom:50%;" />

- **实现类特点**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101223422556.png" alt="image-20230101223422556" style="zoom:50%;" />

#### 10.1.1 HashSet的一个例子

```java
package com.curious.javasepro.d17_set;

import java.util.HashSet;
import java.util.Set;

public class SetTest1 {
    public static void main(String[] args) {
        Set<Integer> st = new HashSet<>();
        st.add(1);
        st.add(1);
        st.add(2);
        System.out.println(st.toString());
        for(Integer a: st) {
            System.out.println(a);
        }
    }
}
```

#### 10.1.2 补充：Set转数组

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101224652636.png" alt="image-20230101224652636" style="zoom:50%;" />

#### 10.1.3 补充：Set转List

```java
Set<String> set = new HashSet<>();
set.add("apple");
set.add("banana");
set.add("orange");

List<String> list = new ArrayList<>(set);
```

### 10.2 Set集合去重复（Student类）

- Student类，**全都是自动生成的**

```java
package com.curious.javasepro.d17_set;

import java.util.Objects;

public class Student {
    private String name;
    private int age;
    private char sex;

    Student() {

    }

    public Student(String name, int age, char sex) {
        this.name = name;
        this.age = age;
        this.sex = sex;
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

    public char getSex() {
        return sex;
    }

    public void setSex(char sex) {
        this.sex = sex;
    }

    @Override
    public String toString() {
        return "Student{" + "name='" + name + '\'' + ", age=" + age + ", sex=" + sex + '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Student student = (Student) o;
        return age == student.age && sex == student.sex && Objects.equals(name, student.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age, sex);
    }
}
```

- 主类Test类

```java
package com.curious.javasepro.d17_set;

import java.util.HashSet;
import java.util.Set;

public class SetDemo3 {
    public static void main(String[] args) {
        Student s1 = new Student("A", 20, '男');
        Student s2 = new Student("A", 20, '男');
        Student s3 = new Student("B", 21, '女');

        Set<Student> st = new HashSet<>();
        st.add(s1);
        st.add(s2);
        st.add(s3);

        for (Student stu: st) {
            System.out.println(stu);  // 这里自动调用了toString方法
        }
    }
}
```

- 输出

```shell
Student{name='A', age=20, sex=男}
Student{name='B', age=21, sex=女}
```

### 10.3 LinkedHashSet 有序的

插入的过程中是有序的Set，好像在一些LeetCode题目会用到这种特殊的结构

### 10.4 TreeSet结构（可排序）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101232342167.png" alt="image-20230101232342167" style="zoom:50%;" />

这里有一系列可以定义比较规则等等的，留着以后使用了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101233324672.png" alt="image-20230101233324672" style="zoom:50%;" />

和之前的对比

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101180455822.png" alt="image-20230101180455822" style="zoom:50%;" />

## 11. 补充知识：可变参数

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101234105641.png" alt="image-20230101234105641" style="zoom:50%;" />

```java
package com.curious.javasepro.d18_params;

import java.util.Arrays;

public class MethodDemo {
    public static void main(String[] args) {
        sum();
        sum(10);
        sum(10, 20, 30);
        sum(new int[]{10, 20, 30});

    }

    public static void sum(int... nums) {
        // 实际上内部就是一个数组
        System.out.println(Arrays.toString(nums));
    }
}
```

```shell
[]
[10]
[10, 20, 30]
[10, 20, 30]
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101234114009.png" alt="image-20230101234114009" style="zoom:50%;" />

## 12. 补充知识：集合工具类（Collections）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101234507290.png" alt="image-20230101234507290" style="zoom:50%;" />

### 12.1 批量添加元素（使用到了可变参数）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101234724706.png" alt="image-20230101234724706" style="zoom:50%;" />

### 12.2 打乱

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101234931697.png" alt="image-20230101234931697" style="zoom:50%;" />

### 12.3 排序（对list集合）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101235250328.png" alt="image-20230101235250328" style="zoom:50%;" />

```java
package com.curious.javasepro.d18_params;

import java.util.*;

public class Test {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        Collections.addAll(list, 2, 1, 3, 4);
        // Arrays.sort(list); 这个排不了
        Collections.sort(list, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2-o1;
            }
        });
        System.out.println(list);
    }
}
```

## 13. Map集合体系（区别于Collection集合体系，Map是双列）

是一个新的集合体系，不属于Collection的

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230102003508039.png" alt="image-20230102003508039" style="zoom:50%;" />

### 13.1 概述、API、遍历方式

#### 13.1.1 概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230102000342429.png" alt="image-20230102000342429" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230102000352565.png" alt="image-20230102000352565" style="zoom:50%;" />

#### 13.1.2 特点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230102000735273.png" alt="image-20230102000735273" style="zoom:50%;" />

```java
Map<Integer, Integer> mp = new HashMap<>();
```

#### 13.1.3 常用API（可以用一个.来自动显示，Java的做题感觉很依赖IDEA）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230102001711690.png" alt="image-20230102001711690" style="zoom:50%;" />

```java
package com.curious.javasepro.d19_map;

import java.util.HashMap;
import java.util.Map;

public class Test {
    public static void main(String[] args) {
        Map<Integer, Integer> mp = new HashMap<>();

        mp.put(0, 0);
        mp.put(1, 1);
        mp.put(2, 2);

        int tmp = mp.remove(2);
        System.out.println("tmp: " + tmp);

        for (Map.Entry<Integer, Integer> entry: mp.entrySet()) {
            System.out.println(entry.getKey());

            System.out.println(entry.getValue());
        }
    }
}
```

#### 13.1.4 遍历方式（其他方式可以去chatGPT问，很准确）

- 键值对

```java
package com.curious.javasepro.d19_map;

import java.util.HashMap;
import java.util.Map;

public class Test {
    public static void main(String[] args) {
        Map<Integer, Integer> mp = new HashMap<>();

        mp.put(0, 0);
        mp.put(1, 1);
        mp.put(2, 2);

        for (Map.Entry<Integer, Integer> entry: mp.entrySet()) {
            System.out.println(entry.getKey());
            System.out.println(entry.getValue());
        }
    }
}
```

### 13.2 Java的Map可以用类做Key吗

是的，在Java中，可以使用类作为Map的键。但是，如果要使用类作为Map的键，该类必须遵循一些规则。

首先，该类必须实现`java.lang.Comparable`接口或者`java.util.Comparator`接口。这是因为Map是通过键来对映射进行排序的，因此必须能够比较Map中的键。

其次，该类必须重写`equals`和`hashCode`方法。在Java中，`equals`方法用于比较两个对象是否相等，而`hashCode`方法用于生成对象的哈希码。Map根据键的哈希码来快速确定映射的位置，因此必须能够生成键的哈希码。

例如，如果要使用类`Person`作为Map的键，则可以这样实现：

```java
public class Person implements Comparable<Person> {
  private String name;
  private int age;

  // 省略其他代码

  @Override
  public int compareTo(Person o) {
    // 按照年龄排序
    return this.age - o.age;
  }

  @Override
  public boolean equals(Object o) {
    if (o == this) {
      return true;
    }
    if (!(o instanceof Person)) {
      return false;
    }
    Person p = (Person) o;
    return this.name.equals(p.name) && this.age == p.age;
  }

  @Override
  public int hashCode() {
    int result = 17;
    result = 31 * result + name.hashCode();
    result = 31 * result + age;
    return result;
  }
}
```

然后，就可以使用`Person`类作为Map的键了。

```java
Map<Person, String> map = new HashMap<>();
map.put(new Person("Alice", 20), "Alice's phone number");
map.put(new Person("Bob", 30), "Bob's phone number");
map.put
```

### 13.3 用类做Key一个自己的尝试

#### 13.3.1 Student类（好像只需要实现equals和hashCode）

```java
package com.curious.javasepro.d17_set;

import java.util.Objects;

public class Student {
    private String name;
    private int age;
    private char sex;

    Student() {

    }

    public Student(String name, int age, char sex) {
        this.name = name;
        this.age = age;
        this.sex = sex;
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

    public char getSex() {
        return sex;
    }

    public void setSex(char sex) {
        this.sex = sex;
    }

//    @Override
//    public int compareTo(Student o) {
//        return this.age - o.age;
//    }

    @Override
    public String toString() {
        return "Student{" + "name='" + name + '\'' + ", age=" + age + ", sex=" + sex + '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Student student = (Student) o;
        return age == student.age && sex == student.sex && Objects.equals(name, student.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age, sex);
    }
}
```

#### 13.3.2 Test测试类

```java
package com.curious.javasepro.d19_map;

import java.util.HashMap;
import java.util.Map;

public class Test2 {
    public static void main(String[] args) {
        Map<Person, String> mp = new HashMap<>();

        Person a = new Person("AAA", 11);
        Person b = new Person("BBB", 22);

        mp.put(a, "123456");
        mp.put(b, "456789");

        System.out.println(mp.get(a));  // 123456

    }
}
```

### 13.4 TreeMap可以对key进行排序，要求写出来排序的那个要求

比如`Map<Apple, String> mp = new TreeMap<>();`

在Apple类中重写`compareTo`

```java
@Override
public int compareTo(Student o) {
    return this.age - o.age;
}
```

### 13.5 集合嵌套

```java
Map<String, List<String>> mp = new HashMap<>();
```















