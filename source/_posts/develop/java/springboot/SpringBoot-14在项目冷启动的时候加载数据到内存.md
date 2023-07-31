---
title: SpringBoot-14在项目冷启动的时候加载数据到内存
date: 2023-06-28 22:06:06
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot-14在项目冷启动的时候加载数据到内存

在项目中，有的时候需要在冷启动的时候进行一些初始化操作，例如读取json配置文件，或者像是在这里可以在项目冷启动的时候把数据库中的全部数据加载到内存中一样，需要在冷启动的时候进行加载，这篇博客记录这里的内容

**需要注意的是如果要对这个内存的数据进行更新，在反向代理的情况下只请求一次接口进行数据更新是不行的，因为只会更新背后的一个机器，所以多机器可能要设置其他数据一致性策略，或者说就是选择从数据库里面拿数据的方案**

## 1. 编写InitWorkerData类

目录存放位置如下，注意这里的@PostConstruct注解

```
.
├── AiswitchboardApplication.java
├── client
├── common
├── config
├── controller
├── dao
├── exceptions
├── init 这个放在了这个文件夹里面
├── model
├── service
├── thrift
└── utils
```

```java
package cn.edu.bupt.aiswitchboard.init;

import cn.edu.bupt.aiswitchboard.dao.WorkerDao;
import cn.edu.bupt.aiswitchboard.model.Worker;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import java.util.List;

@Component
public class InitWorkerData {
    public static List<Worker> workerList;

    // 注入Dao
    @Autowired
    private WorkerDao workerDao;

    @PostConstruct
    public void init() {
        System.out.println("冷启动加载数据库数据到内存中...");
        workerList =  workerDao.selectList(null);
    }
    @PreDestroy
    public void destroy() {
        System.out.println("系统启动成功，workerList加载完成！");
    }
}
```

## 2. 调用

在别的地方可以使用如下方式调用`InitWorkerData.workerList`

```java
for (Worker worker : InitWorkerData.workerList) {
	// do sth
}
```

## 3. 简单编写一段update的

直接加载过来就可以了，但是需要注意反向代理的时候启动多份的问题

```java
public Object update(NameFinderUpdateRequest requestParameters) {
    // 制作初始值，并添加透传信息
    Map<String, Object> data = new HashMap<>();

    // 把数据库中的数据重新加载
    InitWorkerData.workerList =  workerDao.selectList(null);

    return data;
}
```

