---
title: JavaSE-1基础
date: 2022-12-28 11:08:02
tags:
	- javase
categories:
	- develop
	- java
	- javase
---

# JavaSE-1基础

reference：

https://www.bilibili.com/video/BV1Cv411372m/?vd_source=2654875159ed89b917fffce42a65196d

## 1. 入门概念

### 1.1 java有哪些技术平台？

JavaSE（标准版）、JavaEE（企业版）、JavaME（小型版）

### 1.2 如何使用java？

java语言的产品是jdk（java development kit，java开发工具包），必须安装jdk才能使用java语言

jdk8（常用）、jdk11、jdk17（新）是比较常用的LTS（long-term support，长期支持版）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228113806653.png" alt="image-20221228113806653" style="zoom:33%;" />

这里备注一个之前有的疑惑，就是说java前期更新比较慢，所以JDK1.8就是常说的java8了，然后好像从java9开始就不沿用1.x的这种命名方法了

### 1.3 java（运行）和javac（编译）的区别

javac时编译工具，java.exe是执行程序

### 1.4 java的一个过程

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228115513141.png" alt="image-20221228115513141" style="zoom:33%;" />

```shell
javac HelloWorld.java
java HelloWorld
```

jdk11开始，可以直接`java xxx.java`这样的，还是上面那个过程，但是看起来是简化了

### 1.5 jdk的组成

- jvm（java virtual machine）：java虚拟机，真正运行java程序的地方
- 核心类库：java自己写好的程序，留给程序员自己的程序调用的
- jre（java runtime environment）：java的运行环境（jvm+核心类库）
- jdk（java development kit）：java开发工具包（包括上面所有）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228162650177.png" alt="image-20221228162650177" style="zoom:50%;" />

### 1.6 java的跨平台工作原理

- 一次编译，处处可用

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228163048438.png" alt="image-20221228163048438" style="zoom:33%;" />

### 1.7 IDEA项目结构介绍

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228164630292.png" alt="image-20221228164630292" style="zoom:33%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228164851983.png" alt="image-20221228164851983" style="zoom:33%;" />

在目前练习，或者说初期使用的时候，直接建立empty的就可以，不过建基于maven的那些也是可以的

### 1.8 简单总结

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228175932416.png" alt="image-20221228175932416" style="zoom:33%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228175905372.png" style="zoom:33%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228181256176.png" alt="image-20221228181256176" style="zoom:33%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228181528190.png" alt="image-20221228181528190" style="zoom:33%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228181736271.png" alt="image-20221228181736271" style="zoom:33%;" />



## 2. JavaSE基础语法（注释、变量、数据类型）

快捷键：option+command+l格式化代码

截止到这里：java快速入门和IDEA开发工具的使用给学完了，开始学习java基础语法

### 2.1 注释

```java
// 第一种注释方法，单行注释

/*
  多行注释的一种形式
 */

/**
* 文档注释，可以提取到一个程序说明文档去
*/
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228183611523.png" alt="image-20221228183611523" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228183827688.png" alt="image-20221228183827688" style="zoom:50%;" />

### 2.2 字面量

字面量：数据在程序中的书写格式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228184023983.png" alt="image-20221228184023983" style="zoom:50%;" />

注意单双引号的使用，还有一些特殊字符的使用

### 2.3 变量variable

#### 2.3.1 变量定义的格式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228184817964.png" alt="image-20221228184817964" style="zoom:50%;" />

`数据类型 变量名称 = 初始值;`

#### 2.3.2 变量使用注意事项

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228192216530.png" alt="image-20221228192216530" style="zoom:33%;" />

#### 2.3.3 变量原理

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228192840076.png" alt="image-20221228192840076" style="zoom:33%;" />

#### 2.3.4 进制转换

```java
int i1 = 0B01100001;  // 二进制
int i2 = 0141;  // 八进制
int i3 = 0x61; // 16进制
```

### 2.4 数据类型

#### 2.4.1 基本数据类型：四大类八种

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228194156114.png" alt="image-20221228194156114" style="zoom:50%;" />

byte、short、int、long

float、double

char

boolean

- long和int

注意，随便写一个证书字面量默认是int类型的，写一个比如132223243244233虽然没有超过long的范围，超过了范围则会报错

所以如果是希望随便写一个整数字面量当成long类型，需要在其后加L/l

```java
long lg2 = 132223243244233L;
```

- float和double

注意，随便写一个小数字面量默认是double类型，如果希望转成float也需要转，在后面加F/f；double类型是加D

```java
float score = 98.5F;
```

 #### 2.4.2 引用数据类型

` String name = "西门吹雪";`

### 2.5 关键字、标识符

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228195205563.png" alt="image-20221228195205563" style="zoom:33%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228195321683.png" alt="image-20221228195321683" style="zoom:25%;" />

## 3. JavaSE基础语法续（类型转换、运算符、调用Java提供的程序）

类型转换知识、运算符的使用、怎么调用Java提供的程序

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228203112945.png" alt="image-20221228203112945" style="zoom:50%;" />

### 3.1 类型转换

#### 3.1.1 自动类型转换

- 类型范围小的变量， 可以赋值给类型范围大的变量

```java
byte a = 12;
int b = a;
System.out.println(b); // 12
```

- 自动类型转换的其他形式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228220211101.png" alt="image-20221228220211101" style="zoom:50%;" />

#### 3.1.2 表达式的自动类型转换

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228220628988.png" alt="image-20221228220628988" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228221230345.png" alt="image-20221228221230345" style="zoom:33%;" />

#### 3.1.3 强制类型转换

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228221410905.png" alt="image-20221228221410905" style="zoom:33%;" />

```java
int a = 20;
byte b = (byte) a;
System.out.println("b: " + b);
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228221643258.png" alt="image-20221228221643258" style="zoom:33%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228221702075.png" alt="image-20221228221702075" style="zoom:33%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228221818506.png" style="zoom:33%;" />

### 3.2 运算符

```java
+ - * / %
```

 注意`6 * 1.0 / 7`这种问题

`+`做连接符，能算就算，不能算就加在一起！

自增自减`++ --`，这里注意`5++`和`++5`，5++是先赋值再加吧应该。面试如果考自增自减这块，那是纯纯的nt

赋值运算符：`= += -= ...`

关系运算符：`== != > >= < <=`

逻辑运算符：`& | ^ !`，条件运算符中的`&& ||`注意熔断操作（对应python的and和or）

三元运算符：`条件表达式?值1:值2`，自己测试`score > 60 ? true:false`

```java
boolean flag;
int score = 61;
flag = score > 60? true:false;
System.out.println(flag);
```

### 3.3 键盘录入的一个case

```java
// 1、导包操作（不需要自己写，以后通过工具导入更方便）
import java.util.Scanner;
...
    
// 2、得到一个键盘扫描器对象
Scanner sc = new Scanner(System.in);
// 3、调用sc对象的功能等待接收用户输入的数据
int test = sc.nextInt();
System.out.println(test);
```

导入包的过程是自动的

<font color='red'>这里在ACM模式还要学习其他人的输入方法，如果真的ACM还可以使用一些快读的方法</font> 

### 3.4 程序流程控制

if switch for while do-while break continue

switch不能用浮点数，浮点数有精度不准确问题

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221228234332617.png" alt="image-20221228234332617" style="zoom:33%;" />

## 4. 数组基础`int[] arr = {1,2,3}`

### 4.1 数组的定义

#### 4.1.1 静态初始化数组

```java
int[] arr = {1,2,3,4,5};
String[] names = {"Alice", "Bob", "Charle"};
```

数组的访问，注意这里数组的长度`names.length`

```java
Random random = new Random();
for (int i = 0; i < names.length; i++) {
    int index = random.nextInt(3);
    System.out.println("index: " + index + "\tnames: " + names[index]);
}
```

#### 4.1.2 动态初始化数组

```java
int[] arr = new int[5];
String[] names = new String[5];
```

#### 补充：一种简单的编译方法（类似for item in tmp:）

```java
int[] test = {1,2,3,4,5};
for (int item: test) {
    System.out.println(item);
}
```

#### 4.1.3 动态初始化数组的元素默认值

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221229223819055.png" alt="image-20221229223819055" style="zoom:50%;" />

#### 4.1.4 初始化数组全为一个值

```java
int[] arr = new int[20];
Arrays.fill(arr, 5);
```

#### 4.1.5 二维数组的定义和初始化

```java
int[][] matrix = new int[10][10];
for (int[] row: matrix) {
    Arrays.fill(row, 7);
}
for (int i = 0; i < matrix.length; i++) {
    for (int j = 0; j < matrix[0].length; j++) {
        System.out.println(matrix[i][j]);
    }
}
```

 ## 5. 方法基础

方法是一种语法结构，可以把一段代码封装成一个功能，以方便重复调用

### 5.1 方法的定义

```java
public static int twoSum(int a, int b) {
    return a + b;
}
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221229231151440.png" alt="image-20221229231151440" style="zoom:50%;" />

```java
public static void twoSum() {
    sout"Hello, World";
}
```

### 补充：静态方法的调用，调用另外一个类的静态方法

假设同目录下，有`Test.java`和`Student.java`两个类

在Student.java中，如果声明静态方法（public static这里的static），则可以不实例化对象直接调用

```java
public class Student {
    public static void show(int[][] matrix) {
        for (int[] row: matrix) {
            System.out.println(Arrays.toString(row));
        }
    }
}
```

```java
Student.show(matrix);
```

如果是非静态方法，则必须实例化后才能调用

```java
public class Student {
    public void show(int[][] matrix) {
        for (int[] row: matrix) {
            System.out.println(Arrays.toString(row));
        }
    }
}
```

```java
Student stu = new Student();
stu.show(matrix);
```

这个不仅是跨文件，在一个文件内也要遵从这种方法

在python中，是需要额外加`@staticmethod`做修饰的，但一般是用不上的

### 5.2 方法参数传递机制

#### 5.2.1 基本类型的参数传递（以基本类型为例，传递值）

**如果是这种简单基本类型的，在里面不会有改动**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230000234151.png" alt="image-20221230000234151" style="zoom:50%;" />

#### 5.2.2 引用类型的参数传递（以数组为例，传递地址）

**如果是引用类型的，在里面就会被修改了，因为传递的是地址**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230001218530.png" alt="image-20221230001218530" style="zoom:50%;" />

### 5.3 方法重载

同一个类中，出现多个方法名称相同，但是形参列表是不同的，那么这些方法就是重载方法

会根据传入的形参类型，去选择相应的方法

```java
double aa = 0.5;
double bb = 0.3;
double doubleres = twoSum(aa, bb);
System.out.println(doubleres);

public static int twoSum(int a, int b) {
    return a + b;
}


public static double twoSum(double a, double b) {
    return a + b;
}
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230003354682.png" alt="image-20221230003354682" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230003417108.png" alt="image-20221230003417108" style="zoom:50%;" />

## 6. 面向对象

学习自己设计对象并使用

### 6.1 设计对象并使用

#### 6.1.1 设计类，创建对象并使用

类：是对象共同特征的描述

对象：类的实例化

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230104157555.png" style="zoom:50%;" />

Car类

```java
package com.curious.javase.createobject;

public class Car {
    // 成员变量
    String name;
    double price;
	
    // 成员方法
    public void start() {
        System.out.println("汽车启动了");
    }
}
```

使用：

```java
package com.curious.javase.createobject;

public class ObjectTest01 {
    public static void main(String[] args) {
        Car c = new Car();
        c.start();
        c.name = "宝马";
        System.out.println(c.name);
    }
}
// 汽车启动了
// 宝马
```

这个是弄完了能直接使用的，而没有加什么getter setter方法，那些好像还挺关键的，不知道在哪里会提到

<font color='red'>实际开发中，建议一个代码文件只定义一个类，比较规范</font> 

### 补充：垃圾回收

注意：当堆内存中的对象，没有被任何变量引用（指向）时，就会被判定为内存中的”垃圾“

如`Student s1 = new Student();`，然后`s1 = null;`

Java存在自动垃圾回收器，会定期进行清理

### 6.2 构造器+this关键字（有参数构造器、无参数构造器）

#### 6.2.1 构造器

承上启下的内容，这里就有点和之前的知识不懂了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230113214980.png" alt="image-20221230113214980" style="zoom:50%;" />

有点类似python的`def __init__()`

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230113252567.png" alt="image-20221230113252567" style="zoom:50%;" />

<font color='red'>一旦定义了有参数构造器，那么无参数构造器就没有了，如果还想用无参数构造器，此时就需要手写一个无参数构造器了</font> 

```java
package com.curious.javase.createobject;

public class Car {
    String name;
    double price;

    public Car() {
        System.out.println("调用了无参数的构造器");
    }

    public Car(String name, double price) {
        this.name = name;
        this.price = price;
    }

    public void start() {
        System.out.println("汽车启动了");
    }
}
```

#### 6.2.2 this关键字

可以出现在构造器，这个和**python的self**好像是不是很像的感觉，就是用上面那种构造器做一个初始化的感觉？

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230114004342.png" alt="image-20221230114004342" style="zoom:50%;" /><img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230114014425.png" alt="image-20221230114014425" style="zoom:50%;" />

<font color='red'>一个文件中，出现多个class，只能有一个是public的？还需要从代码刷题的角度来理解这个事情</font> 

### 6.3 封装（getter setter方法）

#### 6.3.1 封装的概述

面向对象的三大特征：封装、继承、多态

封装：告诉我们，如何正确设计对象的属性和方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230114446753.png" alt="image-20221230114446753" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230114515062.png" alt="image-20221230114515062" style="zoom:50%;" />

**<font color='red'>封装的原则：对象代表什么，就得封装对应的数据，并提供数据对应的行为</font>** 

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230114933483.png" alt="image-20221230114933483" style="zoom:50%;" />

#### 6.3.2 如何进行更好的封装？

一般建议对成员变量使用private关键字进行修饰（private修饰的成员只能在当前类中访问）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230115336818.png" alt="image-20221230115336818" style="zoom:50%;" />

```java
package com.curious.javase.createobject;

public class Student {
    private int age;

    public int getAge() {
        return this.age;
    }
    public void setAge(int age) {
        // 这里还可以补充一些其他校验逻辑
        this.age = age;
    }
}
```

### 6.4 标准JavaBean（一种对象的很标准的写法）

也可以称为实体类，其对象可以用于在程序中封装数据

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230115831985.png" alt="image-20221230115831985" style="zoom:50%;" />

可以自动生成，在IDEA写完成员变量后，可以自动`generate`的getter setter方法

## 7. 常用API——String、ArrayList

### 7.1 String

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230123228111.png" alt="image-20221230123228111" style="zoom:50%;" />

#### 7.1.1 String类概述

```java
String name = "Curious";
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230123740913.png" alt="image-20221230123740913" style="zoom:50%;" />

#### 7.1.2 String类创建字符串对象的2种方式

```java
// 第一种
String name = "123";

// 第二种
String s1 = new String("123");
String s1 = new String(char[] c);  // 根据字符数组来创建
```

#### 补充：字符串的比较

- 第一种比较方式

```java
String ss1 = "abc";
String ss2 = "abc";
ss1 == ss2; // true
```

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230124833463.png" alt="image-20221230124833463" style="zoom:50%;" />

- 第二种比较方式

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230124919471.png" alt="image-20221230124919471" style="zoom:50%;" />

- chatGPT给出的答案

Q：使用java比较两个字符串是否相等

A：

在 Java 中，可以使用 `String.equals()` 方法来比较两个字符串是否相等。

例如：

```java
String str1 = "Hello";
String str2 = "Hello";

if (str1.equals(str2)) {
  System.out.println("The strings are equal.");
} else {
  System.out.println("The strings are not equal.");
}
```

在这个例子中，输出会是 "The strings are equal."。

注意：在比较字符串时，不能使用 `==` 运算符。这是因为 `==` 运算符比较的是两个对象的引用（即内存地址）是否相同，而不是实际的字符串内容是否相同。因此，如果使用 `==` 运算符来比较两个字符串，只有在两个字符串引用同一个对象时才会返回 `true`，否则会返回 `false`。

例如：

```java
String str1 = "Hello";
String str2 = "Hello";

if (str1 == str2) {
  System.out.println("The strings are equal.");
} else {
  System.out.println("The strings are not equal.");
}
```

在这个例子中，输出会是 "The strings are equal."，因为 `str1` 和 `str2` 引用了同一个字符串对象。但是，如果将这个例子改为这样：

```java
String str1 = "Hello";
String str2 = new String("Hello");

if (str1 == str2) {
  System.out.println("The strings are equal.");
} else {
  System.out.println("The strings are not equal.");
}
```

这个例子中，输出会是 "The strings are not equal."，因为 `str2` 是使用 `new` 关键字创建的新的字符串对象，它的引用

#### 7.1.3 字符串内容比较API str1.equals(str2)

```java
String stra = "hello";
String strb = "hello";
String strc = "HelLO";
System.out.println(stra.equals(strb)); // true
System.out.println(stra.equalsIgnoreCase(strc));  // true
```

#### 7.1.4 常用API——遍历、替换、截取、分割

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230154424561.png" alt="image-20221230154424561" style="zoom:50%;" />

还有`contains，startsWith`这些的方法等等

### 7.2 ArrayList

集合类，集合的大小是不固定的，类似python的list？随时进行add操作这样的感觉

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230123359064.png" alt="image-20221230123359064" style="zoom:50%;" />

数组的长度是确定的，类型是hi固定的，但是在个数不能确定的情况下，用数组就不是很合适了

实际上有了ArrayList后，用数组的场景就会比较少了？

**类型可以不固定，是个很关键的**

ArrayList是集合中的一种，支持索引

#### 7.2.1 ArrayList集合添加元素的方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230162435505.png" alt="image-20221230162435505" style="zoom:50%;" />

```java
ArrayList tmp = new ArrayList();
tmp.add("Hello");
tmp.add(123);
tmp.add(1, 5.6)
for (int i = 0; i < tmp.size(); i++) {
    System.out.println(tmp.get(i));
}
/*
Hello
5.6
123
*/
```

多种类型都可以添加的

这个添加是有返回值的，一般不会失败

#### 7.2.2 ArrayList对于泛型的支持

<font color='red'>泛型的概念在这里第一次听说，之前也听说过不过这里算是一个正式的接触</font> 

motivation：实际开发中，java不希望我们存类型不确定的元素，可能要给封装到一起那种的

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230163314930.png" alt="image-20221230163314930" style="zoom:50%;" />

**<font color='red'>注意：集合中只能存储引用类型，不支持基本数据类型</font>**

```java
ArrayList<Integer> int_list = new ArrayList<>();
int_list.add(1);
int_list.add(2);
int_list.add(3);
for (int i = 0; i < int_list.size(); i++) {
    System.out.println(int_list.get(i));
}
```

#### 7.2.3 ArrayList常用API，遍历

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230163844144.png" alt="image-20221230163844144" style="zoom:50%;" />

```java
ArrayList<String> stringList = new ArrayList<>();
stringList.add("abc");
stringList.add("abc");
stringList.add("abc");
stringList.add("def");
stringList.remove("abc");
String resString = stringList.remove(stringList.size()-1);

for (int i = 0; i < stringList.size(); i++) {
    System.out.println(stringList.get(i));
}
System.out.println(resString);

abc
abc
def
```

remove只能删除一次

#### 7.2.4 ArrayList遍历并删除元素值

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221230164617481.png" alt="image-20221230164617481" style="zoom:50%;" />

坑点在于：删除的时候，角标会有变化？

```java
ArrayList<Integer> scores = new ArrayList<>();
scores.add(98);
scores.add(77);
scores.add(66);
scores.add(89);
scores.add(79);

int i = 0;
while (i < scores.size()) {
    if (scores.get(i) < 80) {
        scores.remove(i);
    } else {
        i ++;
    }
}

for (int j = 0; j < scores.size(); j++) {
    System.out.println(scores.get(j));
}
```

#### 7.2.5 ArrayList存储自定义类型的对象

```java
package com.curious.javase.arraylist;

public class Student {
    private int age;
    private String name;

    public Student() {

    }

    public Student(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void show() {
        System.out.println("[" + this.age + ", " + this.name + "]");
    }
}

```

```java
ArrayList<Student> students = new ArrayList<>();
Student s1 = new Student(10, "AAA");
Student s2 = new Student(11, "BBB");
Student s3 = new Student(12, "CCC");
students.add(s1);
students.add(s2);
students.add(s3);

for (int i = 0; i < students.size(); i++) {
    students.get(i).show();
}
```



