---
title: JavaSE-4进阶流异常日志文件等
date: 2023-01-02 00:41:42
tags:
	- javase
categories:
	- develop
	- java
	- javase
---

# JavaSE-4进阶流异常日志文件等

概述1

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230102011359707.png" alt="image-20230102011359707" style="zoom:50%;" />

## 1. 不可变集合（对应python的元组？）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230102011529055.png" alt="image-20230102011529055" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230102011552449.png" alt="image-20230102011552449" style="zoom:50%;" />

### 1.1 创建不可变集合——X.of方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230102011619275.png" alt="image-20230102011619275" style="zoom:50%;" />

```java
// 1. 不可变的List集合
List<Double> lists = List.of(569.5, 700.5, 523, 570.4);  // 不可变集合
```

## 2. Stream流（重点）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103001848036.png" alt="image-20230103001848036" style="zoom:50%;" />

 ### 2.1 体会Stream流的方便（一根传送带）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103001936465.png" alt="image-20230103001936465" style="zoom:50%;" />

```java
// 之前的变量用高级for循环实现比较方便

// 现在有了stream流之后，就很好操作了
// 在项目阶段会大量的使用了，names是一个ArrayList
names.stream().filter(s -> s.startsWith("张")).filter(s -> s.length() == 3).forEach(s -> sout(s))
```

### 2.2 Stream流的获取（集合、数组获取方式不一样）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103002707578.png" alt="image-20230103002707578" style="zoom:50%;" />

maps需要先获取到keySet()这种的，再通过Stream流

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103003128028.png" alt="image-20230103003128028" style="zoom:50%;" />

### 2.3 Stream流常用API

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103003210404.png" alt="image-20230103003210404" style="zoom:50%;" />

还有`.count()`等，count和forEach是终结方法

- `.map()`

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103004217941.png" alt="image-20230103004217941" style="zoom:50%;" />

### 2.4 Stream流的收集操作

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103004517683.png" alt="image-20230103004517683" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103004528895.png" alt="image-20230103004528895" style="zoom:50%;" />

使用示例，得到可变集合

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103004625044.png" alt="image-20230103004625044" style="zoom:50%;" />

简化，但是得到不可变集合

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103004911994.png" alt="image-20230103004911994" style="zoom:50%;" />

## 3. 异常处理

python中好像比较简单，就是

```python
try:
    xxx
except:
    raise NotImplementError("xxxxx")
```

### 3.1 概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103105359416.png" alt="image-20230103105359416" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103105511995.png" alt="image-20230103105511995" style="zoom:50%;" />

### 3.2 常见运行时异常（编译阶段不会报错，运行时可能出现错误）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103105735811.png" alt="image-20230103105735811" style="zoom:50%;" />

### 3.3 常见编译时异常（编译阶段就报错，必须处理否则代码不通过）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103110535643.png" alt="image-20230103110535643" style="zoom:50%;" />

### 3.4 异常的默认处理流程

创建异常对象，层层往上抛给虚拟机

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103110955328.png" alt="image-20230103110955328" style="zoom:50%;" />

### 3.5 编译时异常的处理机制

编译阶段就出错的，所以必须处理，否则代码根本无法通过

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103111054208.png" alt="image-20230103111054208" style="zoom:50%;" />

#### 3.5.1 异常处理方式1 —— throws

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103111544529.png" alt="image-20230103111544529" style="zoom:50%;" />

- 抛出一个

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103111207449.png" alt="image-20230103111207449" style="zoom:50%;" />

- 多个的（但不是全有机会抛出去，只有一个会出去的感觉）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103111504695.png" alt="image-20230103111504695" style="zoom:50%;" />

#### 3.5.2 异常处理方式2 —— try... catch...（重点）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103112016097.png" alt="image-20230103112016097" style="zoom:50%;" />

```java
try {
    
} catch(Exception e) {
    e.printStackTrace();
}
```

#### 3.5.3 前两种方式结合

抛给上层调用者，然后上层调用者再try，否则不知道哪出问题了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103112336597.png" alt="image-20230103112336597" style="zoom:50%;" />

### 3.6 运行时异常的处理机制

直接用try-catch抛就行，不用写throws

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103112648855.png" alt="image-20230103112648855" style="zoom:50%;" />

### 3.7 自定义异常

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103114345827.png" alt="image-20230103114345827" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103115322036.png" alt="image-20230103115322036" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103115713961.png" alt="image-20230103115713961" style="zoom:50%;" />

#### 3.7.1 自定义异常的分类

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103115839958.png" alt="image-20230103115839958" style="zoom:50%;" />

## 4. 日志（对jar包的再次接触）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103120422630.png" alt="image-20230103120422630" style="zoom:50%;" />

### 4.1 日志技术的概述

#### 4.1.1 概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103120805380.png" alt="image-20230103120805380" style="zoom:50%;" />

输出语句打印到控制台，太捞了（python中有那个logger.info，写一套模板一直用就行了）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103120930014.png" alt="image-20230103120930014" style="zoom:50%;" />

#### 4.1.2 优势

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103121120910.png" alt="image-20230103121120910" style="zoom:50%;" />

### 4.2 日志技术体系结构（学习Logback）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103121353612.png" alt="image-20230103121353612" style="zoom:50%;" />

 

### 4.3 Logback概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103121516179.png" alt="image-20230103121516179" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103121747138.png" alt="image-20230103121747138" style="zoom:50%;" />

需要下载三个jar包（未来使用maven？）

### 4.4 Logback快速入门（未来使用maven？项目直接maven导入jar包）

jar包的内部就是一些class文件，编译好的，能直接用

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103122454304.png" alt="image-20230103122454304" style="zoom:50%;" />

<font color='red'>这玩意能在线更新吗，还是说配置文件的修改也需要冷启动系统什么的</font> 

<font color='red'>xml文件很关键</font> 

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103122658686.png" alt="image-20230103122658686" style="zoom:50%;" />

### 4.5 Logback配置详解，`logback.xml`

比如有一些日志文件输出路径这些的，会配置就可以了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103123623317.png" alt="image-20230103123623317" style="zoom:50%;" />

#### 4.5.1 热更新xml文件

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103124106522.png" alt="image-20230103124106522" style="zoom:50%;" />

### 4.6 日志级别设置

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103123825348.png" alt="image-20230103123825348" style="zoom:50%;" />

## 5. File（这个地方可能跳过的内容多一些，未来使用到了再学习）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103124859571.png" alt="image-20230103124859571" style="zoom:50%;" />

 ### 5.1 自己补充：使用java读取json文件并转化为`Map<String, List<String>>`对象

你可以使用 Gson 库来读取 JSON 文件并将其转换为 `Map<String, List<String>>` 对象。

首先，你需要在项目中添加 Gson 依赖。如果你使用的是 Maven，可以在 pom.xml 中添加如下依赖：

```
<dependency>
  <groupId>com.google.code.gson</groupId>
  <artifactId>gson</artifactId>
  <version>2.8.6</version>
</dependency>
```

然后，你可以使用如下代码来读取 JSON 文件并将其转换为 Map<String, List<String>> 对象：

```java
import com.google.gson.Gson;
import com.google.gson.reflect.TypeToken;
import java.io.FileReader;
import java.lang.reflect.Type;
import java.util.Map;

public class Main {
  public static void main(String[] args) throws Exception {
    Gson gson = new Gson();
    Type mapType = new TypeToken<Map<String, List<String>>>(){}.getType();

    // 读取 JSON 文件
    FileReader reader = new FileReader("file.json");
    Map<String, List<String>> map = gson.fromJson(reader, mapType);

    // 使用 Map 对象
    for (Map.Entry<String, List<String>> entry : map.entrySet()) {
      String key = entry.getKey();
      List<String> value = entry.getValue();
      System.out.println(key + ": " + value);
    }
  }
}
```

上述代码中file.json的示例是什么样的

例如，你可以定义一个名为 file.json 的文件，其内容如下：

```json
{
  "key1": ["value1", "value2"],
  "key2": ["value3", "value4"]
}
```

在这个例子中，Map 对象的内容为 { "key1" -> ["value1", "value2"], "key2" -> ["value3", "value4"] }。

## 6. IO流（这里也跳过一些内容，未来有用到再学习）

 <img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103125811888.png" alt="image-20230103125811888" style="zoom:50%;" />

## 7. 线程概述、线程创建方式一

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103150413407.png" alt="image-20230103150413407" style="zoom:50%;" />

### 7.1 概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103150222025.png" alt="image-20230103150222025" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103150231293.png" alt="image-20230103150231293" style="zoom:50%;" />

### 7.2 多线程的创建

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103154447451.png" alt="image-20230103154447451" style="zoom:50%;" />

#### 7.2.1 方式一：继承Thread类（优缺点+注意点）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103151215365.png" alt="image-20230103151215365" style="zoom:50%;" />

```java
package com.curious.leetcode;

/**
 * 目标：多线程的创建方式一：继承Thead类实现
 */
public class ThreadCreateDemo1 {
    public static void main(String[] args) {
        // 3. new一个新线程对线
        Thread t = new MyThread();  // 多态写法
        // 4. 调用start方法启动线程（执行的还是run方法）
        t.start();

        // 5. 主线程，谁先谁后就不一定了
        for (int i = 0; i < 5; i++) {
            System.out.println("主线程执行输出: " + i);
        }
    }
}

/**
 * 1. 定义一个线程类继承Thread类
 */
class MyThread extends Thread {
    /**
     * 2. 重写run方法，里面定义线程以后要干啥
     */
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println("子线程执行输出: " + i);
        }
    }
}
```

> 子线程执行输出: 0
> 主线程执行输出: 0
> 主线程执行输出: 1
> 主线程执行输出: 2
> 主线程执行输出: 3
> 主线程执行输出: 4
> 子线程执行输出: 1
> 子线程执行输出: 2
> 子线程执行输出: 3
> 子线程执行输出: 4

- 优缺点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103151234901.png" alt="image-20230103151234901" style="zoom:50%;" />

- 注意点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103151442252.png" alt="image-20230103151442252" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103152229185.png" alt="image-20230103152229185" style="zoom:50%;" />

#### 7.2.2 方式二：实现Runnable接口

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103151703416.png" alt="image-20230103151703416" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103151956009.png" alt="image-20230103151956009" style="zoom:50%;" />

```java
package com.curious.javasepro.d20_thread;

/**
 * 目标：学会线程的创建方式而，理解它的优缺点
 */
public class ThreadCreateDemo2 {
    public static void main(String[] args) {
        // 3. 创建一个任务对象
        Runnable target = new MyRunnable();
        // 4. 把任务对象交给Thread处理
        Thread t = new Thread(target);
        // 5. 启动线程
        t.start();

        for (int i = 0; i < 10; i++) {
            System.out.println("主线程执行输出: " + i);
        }
    }
}

/**
 * 1. 定义一个线程任务类，实现Runnable接口
 */
class MyRunnable implements Runnable {
    /**
     * 2. 重写run方法，定义线程的执行任务
     */
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("子线程执行输出: " + i);
        }
    }
}
```

扩展性更强，还可以继承

- 优缺点

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103152443507.png" alt="image-20230103152443507" style="zoom:50%;" />

#### 7.2.3 方式二：另外一种写法补充

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103152640897.png" alt="image-20230103152640897" style="zoom:50%;" />



#### 7.2.4 方式三：JDK5.0新增：实现Callable接口

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103152844041.png" alt="image-20230103152844041" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103152905530.png" alt="image-20230103152905530" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103153024848.png" alt="image-20230103153024848" style="zoom:50%;" />

```java
package com.curious.javasepro.d20_thread;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

/**
 * 目标：学会线程的创建方式三：实现Callable借楼，结合FutureTask完成
 */
public class ThreadCreateDemo3 {
    public static void main(String[] args) {
        // 3. 创建Callable任务对象
        Callable<String> call = new MyCallalbe(100);
        // 4. 把Callable对象 交给 FutureTask对象
        // FutureTask对象的作用：是Runnable的对象（实现了Runnable接口），可以交给Thread了
        // 作用：可以在线程执行完毕之后，调用其get方法得到线程执行完成的结果
        FutureTask<String> ft = new FutureTask<>(call);
        // 5. 交给线程处理
        Thread t = new Thread(ft);
        // 6. 启动线程
        t.start();

        // 再创建一个作为示例
        Callable<String> call2 = new MyCallalbe(200);
        FutureTask<String> ft2 = new FutureTask<>(call2);
        Thread t2 = new Thread(ft2);
        t2.sleep(500);
        t2.start();

        try {
            String res1 = ft.get();
            System.out.println("第一个线程的执行结果: " + res1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        try {
            String res2 = ft2.get();
            System.out.println("第二个线程的执行结果: " + res2);
        } catch (Exception e) {
            e.printStackTrace();
        }

    }

}

/**
 * 1. 定义一个人物类，实现Callable接口（因为里面要返回结果所以有泛型），应该声明线程任务执行完毕后的结果的数据类型
 */
class MyCallalbe implements Callable<String> {

    private int n;

    public MyCallalbe(int n) {  // 构造方法
        this.n = n;
    }

    /**
     * 2. 重写call方法
     */
    @Override
    public String call() throws Exception {
        int sum = 0;
        for (int i = 0; i < this.n; i++) {
            sum += i;
        }
        return "子线程的执行结果是: " + sum;
    }
}
```

### 7.3 Thread的常用方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103154606197.png" alt="image-20230103154606197" style="zoom:50%;" />

#### 7.3.1 获取和设置线程名称

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103155055142.png" alt="image-20230103155055142" style="zoom:50%;" />

#### 7.3.2 构造器

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103155019314.png" alt="image-20230103155019314" style="zoom:50%;" />

#### 7.3.3 线程休眠方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103155225611.png" alt="image-20230103155225611" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103155502854.png" alt="image-20230103155502854" style="zoom:50%;" />

### 7.4 线程安全问题

#### 7.4.1 线程安全问题是什么

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103155740981.png" alt="image-20230103155740981" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103155752310.png" alt="image-20230103155752310" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103155945645.png" alt="image-20230103155945645" style="zoom:50%;" />

#### 7.4.2 线程安全问题模拟案例——取钱

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103160735652.png" alt="image-20230103160735652" style="zoom:50%;" />

可以if小红，sleep5秒；

### 7.5 线程同步——解决线程安全问题

**共享资源加锁**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103160958026.png" alt="image-20230103160958026" style="zoom:50%;" />

#### 7.5.1 方式一：同步代码块

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103161221302.png" alt="image-20230103161221302" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103161232040.png" alt="image-20230103161232040" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103161347375.png" alt="image-20230103161347375" style="zoom:50%;" />

**小明小红、小黑小白，要使用上不同的锁，这个地方不能是一把锁锁住了千家万户**

**所以，建议使用共享资源作为锁对象！**

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103161651838.png" alt="image-20230103161651838" style="zoom:50%;" />

小明和小红的Account对象是一个acc，所以这个

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103161721142.png" alt="image-20230103161721142" style="zoom:50%;" />

#### 7.5.2 方式二：同步方法

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103161932018.png" alt="image-20230103161932018" style="zoom:50%;" />

只需要在取钱功能上加一个这个就行了

同步方法锁的范围更大，好用的感觉

#### 7.5.3 Lock锁

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103162429202.png" alt="image-20230103162429202" style="zoom:50%;" />

接口不能构建对象，使用实现类来构建对象Collection那边也是这个说法

在Account对象那块加锁，这样同一个对象就是同一把锁了

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103162703711.png" alt="image-20230103162703711" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103162734738.png" alt="image-20230103162734738" style="zoom:50%;" />

**为了保证解锁，加finally逻辑**

try...finally...

### 7.6 线程通信（了解）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103162907002.png" alt="image-20230103162907002" style="zoom:50%;" />

### 7.7 线程池（重点）

#### 7.7.1 线程池概述

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103165218106.png" alt="image-20230103165218106" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103165412323.png" alt="image-20230103165412323" style="zoom:50%;" />

#### 7.7.2 线程池实现的API、参数说明

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103165458317.png" alt="image-20230103165458317" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103165634810.png" alt="image-20230103165634810" style="zoom:50%;" />

#### 7.7.3 线程池处理Runnable任务

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103170953362.png" alt="image-20230103170953362" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230103171514267.png" alt="image-20230103171514267" style="zoom:50%;" />

线程池可以重复利用，但是如果再加一个变成这样，3个线程处理5个任务

```java
// 3个线程处理5个任务
pool.execute(target);  
pool.execute(target);
pool.execute(target);
pool.execute(target);
pool.execute(target);
```

#### 7.7.4 线程池处理Callable任务

暂时也跳过，未来开发有接触到进一步使用

### 7.8 线程的其他内容，未来随着面试进一步学习了

TODO

## 8. 网络通信TCP/UDP入门

TODO，这个地方也暂时跳过了，未来有接触到再进一步学习























