---
title: hexo图片路径的配置
date: 2022-11-19 16:19:33
tags: 
    - hexo
categories:
	- Crsenal
---

在使用hexo搭建博客的时候，`./source/images`路径是一个全局的路径，放在这里的图片可以被博客中的内容检测到

但是这样会导致所有博客的图片内容全都混在一块，同时在本地用typora等看图片的时候，因为路径配置的不一样也会有问题存在

所以，目标是统一这边的路径配置，让博客推到github上成功的同时，也能在本地typora看到，并方便迁移

<!--more-->

# hexo图片路径的配置

## 1. 安装插件hexo-asset-image

### 1.1 reference

https://blog.csdn.net/Strong997/article/details/97767929?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-97767929-blog-100524856.pc_relevant_multi_platform_whitelistv4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-97767929-blog-100524856.pc_relevant_multi_platform_whitelistv4&utm_relevant_index=2

### 1.2 操作

使用npm进行安装，命令如下

```shell
npm install https://github.com/CodeFalling/hexo-asset-image --save

npm install hexo-asset-image --save
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
