---
title: SpringBoot-9抛出自定义异常+捕获异常
date: 2023-06-09 00:34:27
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot-9抛出自定义异常+捕获异常

在一个项目中，抛出异常还是很关键的，在不同的地方抛出合适的异常，并且避免接口直接报错，而是把异常包装在Response类的data地方进行返回是很关键

## 1. 自定义异常类

在exceptions这个文件夹下进行操作，exceptions和controller service这些文件夹都是平级的

`aiswitchboard/src/main/java/cn/edu/bupt/aiswitchboard/exceptions/NotImplementedException.java`

`NotImplementedException.java`

```java
package cn.edu.bupt.aiswitchboard.exceptions;

public class NotImplementedException extends RuntimeException {
    public NotImplementedException() {
        super("This method or feature is not implemented yet.");
    }
}
```

## 2. 异常类的合适捕获/抛出时机try-catch

### 2.1 在controller层catch异常

由于希望把异常包装在Response类的data地方进行返回，而不是直接让接口不可用，所以把try-catch语句加在controller层是最合理的，这样所有这个接口下不管是哪里抛出了异常，都能在controller这一层被接收到，以`NameFinderController`为例

```java
...
@RestController
@Api("namefinder这个namespace主要用来进行部门人名查找接口的相关操作")
@RequestMapping("/namefinder")
public class NameFinderController {

    @Autowired
    private NameFinderServiceImpl nameFinderService;

    ...

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

### 2.2 在内部throw异常

在任何地方都可以throw出来，如果是自定义的就new一个

```java
if (type.equals("withTone")) {
    ...
} else if (type.equals("withoutTone")){
    ...
} else {
    throw new NotImplementedException();
}
```
