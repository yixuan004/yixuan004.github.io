---
title: SpringBoot-3整合HanLP1.x
date: 2023-04-10 19:49:12
tags:
---

# SpringBoot-3整合HanLP1.x

## 1. 配置（hanlp把程序和数据分离，可以提供一些自定义的操作）

参考：https://github.com/hankcs/HanLP/tree/1.x

https://blog.csdn.net/u010992313/article/details/104224712?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166322073416782395373625%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166322073416782395373625&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-3-104224712-null-null.142^v47^pc_rank_34_default_23,201^v3^control_1&utm_term=hanlp%20jar&spm=1018.2226.3001.418

1. 首先导入坐标pom.xml

```xml
<!--hanlp1.x-->
<dependency>
    <groupId>com.hankcs</groupId>
    <artifactId>hanlp</artifactId>
    <version>portable-1.7.8</version>
</dependency>
```

2. 下载data和release

http://nlp.hankcs.com/download.php?file=data

http://nlp.hankcs.com/download.php?file=jar

把hanlp.properties移动到：src/main/resources/hanlp.properties

把data移动到：src/main/resources/static/data

修改hanlp.properties中的root路径，是data的父目录：

```
root=src/main/resources/static
```

3. jar包如果使用了坐标的方式导入，就不再需要移动了

## 2. 测试

随便找一个接口加入，即可查看到输入

```java
List<Term> lst = HanLP.segment("你好，欢迎使用HanLP汉语处理包！");
或者
System.out.println(HanLP.segment("你好，欢迎使用HanLP汉语处理包！"));
```

> [你好/l, ，/w, 欢迎/v, 使用/v, HanLP/nx, 汉语/nz, 处理/v, 包/v, ！/w]

## 3. TODO 这里他默认下载下来的data和和hanlp.properties还是配置比较简单的，如果能根据业务场景不断优化数据，将能够达到更好的效果

