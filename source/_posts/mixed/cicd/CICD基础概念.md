---
title: CICD基础概念
date: 2023-03-13 10:12:55
tags:
    - cicd
categories:
	- develop
	- mixed
	- cicd
---

# CI/CD基础概念

## 1. 什么是CI/CD

Reference：https://blog.csdn.net/Z1404686551/article/details/126629428

持续集成CI（Continuous Integration），持续交付CD（Continuous Delivery）

**理想目标：自动化流程、一次配置多次使用、补充自动测试的case、精力花在怎么把代码写好，代码部署的就自动完成**

上线操作：

第一步：从一个项目git clone到本地，修改几行代码，本地测试后，git commit & git push到代码仓库；

第二步：push的过程会触发CI流水线，比如gitlab CI，这样会去执行一些测试，编译 测试 扫描等，过一阵会收到case测试失败还是执行通过的消息，这个过程可能还触发一些比如docker镜像的打包，然后docker镜像向镜像库的推送等；

第三步：将功能交给测试&产品验收，发现问题或者收到建议，重新修改，当没有问题，手动触发预发部署流水线，自动生成部署交付产物，后面就不用管了；

第四步：持续部署流水线每几分钟自动检测交付产物包，发现更新后，自动将产物部署到线上；

![在这里插入图片描述](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/clip_image001.png)

## 2. CI/CD自己的实践

没有太执行过其他的流程，只执行过一个自动打包的流程，

