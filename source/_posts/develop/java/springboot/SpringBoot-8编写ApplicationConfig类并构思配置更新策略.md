---
title: SpringBoot-8编写ApplicationConfig类并构思配置更新策略
date: 2023-06-09 00:21:46
tags:
	- springboot
categories:
	- develop
	- java
	- springboot
---

# SpringBoot-8编写ApplicationConfig类并构思配置更新策略

首先在application.yml中添加一些配置的配置，然后在config目录下编写ApplicationConfig类，@Value的形式把yml的注入到private元素中，最后在Controller或者ServiceImpl中注入ApplicationConfig，就可以使用

## 1. ApplicationConfig类相关

application.yml

```yaml
# ApplicationConfig
weight:
  char: 0.25
  pinyin-tone: 0.25
  pinyin-without-tone: 0.25
  set: 0.25
```

ApplicationConfig.java

```java
package cn.edu.bupt.aiswitchboard.config;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;


@Configuration
public class ApplicationConfig {
    @Value("${weight.char}")
    private double weightChar;

    @Value("${weight.pinyin-tone}")
    private double weightPinyinTone;

    @Value("${weight.pinyin-without-tone}")
    private double weightPinyinWithoutTone;

    @Value("${weight.set}")
    private double weightSet;

    public double getWeightChar() {
        return weightChar;
    }

    public void setWeightChar(double weightChar) {
        this.weightChar = weightChar;
    }

    public double getWeightPinyinTone() {
        return weightPinyinTone;
    }

    public void setWeightPinyinTone(double weightPinyinTone) {
        this.weightPinyinTone = weightPinyinTone;
    }

    public double getWeightPinyinWithoutTone() {
        return weightPinyinWithoutTone;
    }

    public void setWeightPinyinWithoutTone(double weightPinyinWithoutTone) {
        this.weightPinyinWithoutTone = weightPinyinWithoutTone;
    }

    public double getWeightSet() {
        return weightSet;
    }

    public void setWeightSet(double weightSet) {
        this.weightSet = weightSet;
    }
}
```

在controller或者service里面的使用，NameFinderServiceImpl.java（可能还有简化方法）

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

    @Override
    public Object find(NameFinderFindRequest requestParameters) {
      	...
        double totalScore = applicationConfig.getWeightChar() * scoreChar + applicationConfig.getWeightPinyinTone() * scorePinyinTone +
                                applicationConfig.getWeightPinyinWithoutTone() * scorePinyinWithoutTone + applicationConfig.getWeightSet() * scoreSet;
        ...
        return data;
    }
}
```

## 2. 更新策略相关

对于系统中的一些配置项，我们很有可能在系统上线的时候需要进行更新，从设计角度来说有很多种更新策略的选择，可以分为：

| 更新策略                                                     | 好处                                                         | 不足                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 1）把待更新参数写在配置文件里面，然后编写几个数据更新的接口，当向接口请求的时候可以完成数据的更新 | 相对是一种比较简单的方式                                     | 这种方案的不足之处其实非常多：首先在分布式系统中如果有反向代理如NGINX这样的前面封装存在，那么一个接口请求只能做到一个后面被代理服务器的更新，其他的还是会是老版数据。此外，如果一次性要修改很多参数，还需要把接口编写很多个。这里有一种自己实现的基于redis记录时间的多进程同步方式，在一个机器上完成修改后，修改时间会变，其他机器读到了改变后的修改时间将更新。 |
| 2）每次接口请求的时候都重新读取配置文件                      | 比较简单，而且保证肯定可以生效，不会受到反向代理多台服务器的影响 | 首先会影响接口的效率，即使不是更新的接口请求也会重新加载一遍配置文件（在python中通过读json实现），在java这边可能也需要有类似的操作，但是不知道怎么能每次都修改ApplicationConfig然后完成一些注入 |
| 3）将配置文件写在mysql中                                     | 当使用配置文件的时候，从mysql那边去取，这样能保证实时读到的是最新的 | 和mysql有交互，如果mysql宕机会不会有问题，小数据量应该还好   |

暂时能想到的是以上这些更新策略，这还是一个非常常用的问题，感觉是有多种方式可以实现的
