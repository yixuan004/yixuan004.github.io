---
title: hexo图片及图床配置相关
date: 2022-11-19 16:19:33
tags: 
  - hexo
categories:
	- develop
	- mixed
	- hexo
---

本来是打算使用各种插件，使得图片文件上传到github的时候，图片就也保存在github上

但是后来发现这么配置实在太麻烦了，第一次配置成功后，发现迁移了一个博客主题，就又不行了

在有了PicGo、阿里云等图床比较方便的环境后，开始考虑图床的方式

自己的阿里云图床：`http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img`

<!--more-->

# hexo图片及图床配置相关

## 1. 安装插件hexo-asset-image（已作废）

### 1.1 reference

https://blog.csdn.net/Strong997/article/details/97767929?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-97767929-blog-100524856.pc_relevant_multi_platform_whitelistv4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-97767929-blog-100524856.pc_relevant_multi_platform_whitelistv4&utm_relevant_index=2

### 1.2 操作

使用npm进行安装，命令如下

```shell
npm install https://github.com/CodeFalling/hexo-asset-image --save
```

注意，这里很多其他博客提供的安装命令如下，但实际尝试的时候可能由于版本等问题，导致无法生效，这些命令可能不好使

```shell
npm install hexo-renderer-marked --save
npm install hexo-asset-image --save
```

之后，将`_config.yml`文件的做如下修改（注：后面的marked可能是不需要的，可能是对应`hexo-render-marked`的配置）

```shell
_config.yml
post_asset_folder: true
marked:
  prependRoot: true
  postAsset: true
```

在上述配置后，在执行`hexo new [FILENAME]`的时候，会自动生成这个文件名字的md和对应的文件名字的文件夹，这里可以在文件夹后加入`.assets`，和目前我自己本机的typora环境对应上

至此操作完成

<font color='red'>后续发现，使用该种方法，在切换博客主题后，产生了一些问题</font> 

## 2. 基于图床上传图片（PicGo+阿里云）

主要参考该篇知乎：https://zhuanlan.zhihu.com/p/346410333

PicGo方便的地方主要在于，可以自动将剪切保存并粘贴到markdown文件中的图片，上传到云图床中；

根据自己的测试，感觉确实是只有粘贴到markdown（typora里面配置了），才会上传到云图床中，这样也保证了图片的安全性，不会随便截个图就被上传到PicGo中了；

PicGo的下载：

https://github.com/Molunerfinn/PicGo

https://github.com/Molunerfinn/PicGo/releases

### 2.1 PicGo的配置

其中各项内容需要根据知乎中的内容，到阿里云那边获取

![image-20221125092942570](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221125092942570.png)

#### 2.1.1 阿里云注册及配置具体步骤

1）前往网站进行注册（支付宝可以直接登录）：https://www.aliyun.com/?spm=5176.12818093.nav-right.dofficial.2d5016d0w77TOO

2）进入对象存储OSS服务页面https://www.aliyun.com/product/oss?spm=5176.19720258.J_3207526240.14.3b7876f4UXZmY8，直接点击折扣套餐：

![image-20221125140003933](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221125140003933.png)

3）该界面使用默认配置就可以，点击立即购买（这里是因为之前已经买过了，所以没法购买了）

![image-20221125140057120](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221125140057120.png)

4）进入OSS管理控制台https://oss.console.aliyun.com/bucket，点击创建Bucket

![image-20221125140501639](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221125140501639.png)

5）在创建的时候，注意权限的控制

- 读写权限选择**公共读**
- 选择地域后，需要记录下来：oss-cn-hangzhou.aliyuncs.com

#### 2.2.2 获取accessKeyId和accessKeySecret

点击右上角头像的AccessKey管理

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221125140840317.png" style="zoom:50%;" />

点击进去后，安全提示，选择继续使用AccessKey

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221125140940235.png" alt="image-20221125140940235" style="zoom:50%;" />

这里就可以查到AccessKey ID和Secret了

![image-20221125141033307](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221125141033307.png)

### 2.2 typora的配置

![image-20221125093022191](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221125093022191.png)