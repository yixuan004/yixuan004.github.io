---
title: SpringBoot-7编写一个接口服务model+controller+service
date: 2023-06-08 23:19:28
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot-7编写一个接口服务model+controller+service

要编写一套model+controller+service才能实现http的接口服务，写好了基本就能实现http服务了，其中：

model文件夹下：编写接口请求的Request模板，接口输入的example以及接口校验，例如`NameFinderFindRequest.java`

controller文件夹下：编写接口，主要负责接口的校验以及把入参放入service层处理，处理完后封装成data再封装成Response返回

service文件夹下：编写接口的所对应方法的具体处理逻辑，分为service的接口和serviceimpl类

```shell
├── src
│   ├── main
│   │   ├── java
│   │   │   └── cn
│   │   │       └── edu
│   │   │           └── bupt
│   │   │               └── aiswitchboard
│   │   │                   ├── ...
│   │   │                   ├── controller
│   │   │                   │   ├── HelloController.java
│   │   │                   │   ├── NameFinderController.java
│   │   │                   │   └── TextClassifyController.java
│   │   │                   ├── model
│   │   │                   │   ├── HelloJsonInputRequest.java
│   │   │                   │   ├── NameFinderFindRequest.java
│   │   │                   │   ├── NameFinderInsertRequest.java
│   │   │                   │   ├── TextClassifyPredictRequest.java
│   │   │                   │   └── Worker.java
│   │   │                   ├── service
│   │   │                   │   ├── NameFinderService.java
│   │   │                   │   ├── NameFinderServiceImpl.java
│   │   │                   │   ├── TextClassifyService.java
│   │   │                   │   └── TextClassifyServiceImpl.java
...
```

## 1. model下的`NameFinderFindRequest`类

主要就是针对每种方法写一下接口请求结构，方便约定

```java
package cn.edu.bupt.aiswitchboard.model;

import io.swagger.annotations.ApiModelProperty;

public class NameFinderFindRequest {
    @ApiModelProperty(value = "需要查询的内容，如：找系统研发部的刘××", example = "系统集成研发部，杨过")
    private String content;
    @ApiModelProperty(value = "和数据库中相对应的企业id，如目前使用的20000001", example = "20000001")
    private String corpId;
    @ApiModelProperty(value = "任务标识字段，目前仅支持设置为test", example = "test")
    private String taskId;

    public NameFinderFindRequest() {
    }

    public NameFinderFindRequest(String content, String corpId, String taskId) {
        this.content = content;
        this.corpId = corpId;
        this.taskId = taskId;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }

    public String getCorpId() {
        return corpId;
    }

    public void setCorpId(String corpId) {
        this.corpId = corpId;
    }

    public String getTaskId() {
        return taskId;
    }

    public void setTaskId(String taskId) {
        this.taskId = taskId;
    }

    @Override
    public String toString() {
        return "NameFinderFindRequest{" +
                "content='" + content + '\'' +
                ", corpId='" + corpId + '\'' +
                ", taskId='" + taskId + '\'' +
                '}';
    }

    public boolean check() {
        // 非空检查
        if (this.content == null ||  this.corpId == null || this.taskId == null) {
            return false;
        }
        return true;
    }
}
```

## 2. controller下的`NameFinderController`类

主要就是写接口的，一个类可以算是一个namespace，里面再继续往下写

注意这里需要注入Service的Impl，然后就可以用Service的方法了，另外就是使用@RequestBody来修饰一下包装入参类

```java
package cn.edu.bupt.aiswitchboard.controller;

import cn.edu.bupt.aiswitchboard.common.Response;
import cn.edu.bupt.aiswitchboard.common.ResponseCode;
import cn.edu.bupt.aiswitchboard.common.ResponseMessage;
import cn.edu.bupt.aiswitchboard.exceptions.NotImplementedException;
import cn.edu.bupt.aiswitchboard.model.HelloJsonInputRequest;
import cn.edu.bupt.aiswitchboard.model.NameFinderFindRequest;
import cn.edu.bupt.aiswitchboard.model.NameFinderInsertRequest;
import cn.edu.bupt.aiswitchboard.service.NameFinderServiceImpl;
import com.hankcs.hanlp.HanLP;
import com.hankcs.hanlp.seg.common.Term;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.dao.DuplicateKeyException;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
@Api("namefinder这个namespace主要用来进行部门人名查找接口的相关操作")
@RequestMapping("/namefinder")
public class NameFinderController {

    @Autowired
    private NameFinderServiceImpl nameFinderService;

    @ApiOperation("部门人名查找find接口，输入一句话和各种依赖资源，输出查找得到的人名-部门对应")
    @PostMapping("/match")
    public Response<Object> find(@RequestBody NameFinderFindRequest nameFinderFindRequest) {
        Response<Object> resp = new Response<>();  // 初始化返回值
        // 在Controller层检查接口参数是否满足要求
        if (!nameFinderFindRequest.check()) {
            resp.update(ResponseCode.UNKNOWNERROR.getCode(), ResponseMessage.UNKNOWNERROR.getMessage(), "输入字段不符合要求，请检查");
            return resp;
        }
        // 进入Service层处理
        Object data = nameFinderService.find(nameFinderFindRequest);
        resp.update(ResponseCode.SUCCESS.getCode(), ResponseMessage.SUCCESS.getMessage(), data);
        return resp;
    }

    @ApiOperation("部门人名查找insert接口，可以将新用户的名称执行插入并更新数据库")
    @PostMapping("/insert")
    public Response<Object> insert(@RequestBody NameFinderInsertRequest nameFinderInsertRequest) {
        Response<Object> resp = new Response<>();  // 初始化返回值
        // 在Controller层检查接口参数是否满足要求
        if (!nameFinderInsertRequest.check()) {
            resp.update(ResponseCode.UNKNOWNERROR.getCode(), ResponseMessage.UNKNOWNERROR.getMessage(), "输入字段不符合要求，请检查");
            return resp;
        }
        // 进入Service层处理
        try {
            Object data = nameFinderService.insert(nameFinderInsertRequest);
        } catch (DuplicateKeyException e) {
            resp.update(ResponseCode.UNKNOWNERROR.getCode(), ResponseMessage.UNKNOWNERROR.getMessage(), e.getMessage());
            return resp;
        } catch (NotImplementedException e) {
            resp.update(ResponseCode.UNKNOWNERROR.getCode(), ResponseMessage.UNKNOWNERROR.getMessage(), e.getMessage());
            return resp;
        }
        // resp.update(ResponseCode.SUCCESS.getCode(), ResponseMessage.SUCCESS.getMessage(), data);
        return resp;
    }
}
```

## 3. service下的`NameFinderService`接口和`NameFinderServiceImpl`类

`NameFinderService`接口主要就是定义有哪些方法（函数），这里返回值就写Object类，因为Response类的data是个Object什么都能往里面装

```java
package cn.edu.bupt.aiswitchboard.service;

import cn.edu.bupt.aiswitchboard.model.NameFinderFindRequest;
import cn.edu.bupt.aiswitchboard.model.NameFinderInsertRequest;

public interface NameFinderService {
    Object find(NameFinderFindRequest requestParameters);
    Object insert(NameFinderInsertRequest requestParameters);
}
```

`NameFinderServiceImpl`类就是具体的实现逻辑，这里可以注入Config等，Dao也可以注入

```java
package cn.edu.bupt.aiswitchboard.service;

import cn.edu.bupt.aiswitchboard.config.ApplicationConfig;
import cn.edu.bupt.aiswitchboard.dao.WorkerDao;
import cn.edu.bupt.aiswitchboard.model.NameFinderFindRequest;
import cn.edu.bupt.aiswitchboard.model.NameFinderInsertRequest;
import cn.edu.bupt.aiswitchboard.model.Worker;
import cn.edu.bupt.aiswitchboard.utils.ScoreUtils;
import cn.edu.bupt.aiswitchboard.utils.PinyinUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.*;

@Service
public class NameFinderServiceImpl implements NameFinderService{

    // 注入Config
    private final ApplicationConfig applicationConfig;
    public NameFinderServiceImpl(ApplicationConfig applicationConfig) {
        this.applicationConfig = applicationConfig;
    }

    // 注入Dao
    @Autowired
    private WorkerDao workerDao;

    @Override
    public Object find(NameFinderFindRequest requestParameters) {
        // 制作初始值，并添加透传信息
        Map<String, Object> data = new HashMap<>();
        data.put("corpId", requestParameters.getCorpId());
        data.put("taskId", requestParameters.getTaskId());

        // 开始执行处理
        String content = requestParameters.getContent();
//        System.out.println("content: " + content);
//        System.out.println(content.getClass());

        // 分词，过滤，并制作对于传入文字的分词（传入很可能是更长的）
        String mandarinContent = PinyinUtils.getPinyin(content, "withTone");  // 含拼音的分词
        String mandarinStripContent = PinyinUtils.getPinyin(content, "withoutTone");  // 不含拼音的分词
        String mandarinSetContent = PinyinUtils.toMandarinSet(mandarinStripContent);

        // 方法1：从数据库里select把所有的select出来，然后完成逐条的进行匹配，并制作逐条匹配的返回结果
        List<Worker> workerList = workerDao.selectList(null);
        List<Map<String, Object>> matchRes = new ArrayList<>();
//        System.out.println(workerList);
//        System.out.println(workerList.toString());

        for (Worker worker : workerList) {
            String dbPer = worker.getPer();
            String dbDep = worker.getDep();
            String dbMandarinPer = worker.getMandarinPer();
            String dbMandarinStripPer = worker.getMandarinStripPer();
            String dbMandarinDep = worker.getMandarinDep();
            String dbMandarinStripDep = worker.getMandarinStripDep();
            String dbMandarinSet = worker.getMandarinSet();

            double scoreChar = ScoreUtils.basicEditDistance(content, dbPer+dbDep);  // 原字符级别的编辑距离
            double scorePinyinTone = ScoreUtils.strHashEditDistance(mandarinContent, dbMandarinPer+dbMandarinDep);  // 含拼音级别的编辑距离
            double scorePinyinWithoutTone = ScoreUtils.strHashEditDistance(mandarinStripContent, dbMandarinStripPer+dbMandarinStripDep);  // 不含拼音级别的编辑距离
            double scoreSet = ScoreUtils.setScore(mandarinSetContent, dbMandarinSet);

//            System.out.println("scoreChar: " + scoreChar);
//            System.out.println("scorePinyinTone: " + scorePinyinTone);
//            System.out.println("scorePinyinWithoutTone: " + scorePinyinWithoutTone);
//            System.out.println("scoreSet: " + scoreSet);

            double totalScore = applicationConfig.getWeightChar() * scoreChar + applicationConfig.getWeightPinyinTone() * scorePinyinTone +
                                applicationConfig.getWeightPinyinWithoutTone() * scorePinyinWithoutTone + applicationConfig.getWeightSet() * scoreSet;

            Map<String, Object> mp = new HashMap<>();
            mp.put("per", dbPer);
            mp.put("dep", dbDep);
            mp.put("score", totalScore);
            matchRes.add(mp);
        }

        // 按照score字段进行排序
        Collections.sort(matchRes, new Comparator<Map<String, Object>>() {
            @Override
            public int compare(Map<String, Object> o1, Map<String, Object> o2) {
                // 降序排序
                double score1 = (double) o1.get("score");
                double score2 = (double) o2.get("score");
                return Double.compare(score2, score1);
            }
        });
        data.put("match", matchRes);
//        for (Map<String, Object> mr : matchRes) {
//            System.out.println(mr.toString());
//        }
        return data;
    }

    public Object insert(NameFinderInsertRequest requestParameters) {
        // 制作初始值，并添加透传信息
        Map<String, Object> data = new HashMap<>();

        // 提取参数
        String corpId = requestParameters.getCorpId();
        String dep = requestParameters.getDep();
        String per = requestParameters.getPer();

        // 执行转化为拼音的一些处理，返回用空格分割的String可行
        String mandarinPer = PinyinUtils.getPinyin(per, "withTone");
        String mandarinStripPer = PinyinUtils.getPinyin(per, "withoutTone");
        String mandarinDep = PinyinUtils.getPinyin(dep, "withTone");
        String mandarinStripDep = PinyinUtils.getPinyin(dep, "withoutTone");

        // 执行转化为音节的一些处理
        String mandarinSet = PinyinUtils.toMandarinSet(mandarinStripPer) + PinyinUtils.toMandarinSet(mandarinStripDep);
        System.out.println("mandarinSet: " + mandarinSet);

        // 插入数据库
        Worker worker = new Worker(corpId, dep, per, "12345678", "12345678", mandarinPer, mandarinStripPer, mandarinDep, mandarinStripDep, mandarinSet, "", "");
        workerDao.insert(worker);

        return data;
    }
}
```





