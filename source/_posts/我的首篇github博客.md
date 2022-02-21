---
title: 我的首篇github博客!
date: 2021-09-13 08:01:04
tags: 
    - hexo相关
categories:
	- Crsenal
---

**何老版推荐的github.io+hexo好用啊**

**个人代码管理+笔记博客什么的都可以写在github这里了**

主要写了hexo + github.io的双分支部署说明和步骤，以及本地运维和新建修改，切换主题等操作。
<!--more-->

References：
> 简易部署相关：https://www.jianshu.com/p/390f202c5b0e
> 多分支部署相关：https://blog.csdn.net/sherlockyb/article/details/94180548
> 切换主题相关：https://github.com/stkevintan/hexo-theme-material-flow 

## 1. hexo + github.io的双分支部署的说明

简易部署带来的问题：

如果使用简单的hexo配置，那么本地的一些md等文件将丢失，所以现在有master（存储hexo生成的）和coding-pages（静态的）两个分支；

master用来存储hexo生成的+yixuan004.github.io页面显示的内容（html），而coding-pages存放了一些原本的markdown文件，还有一些_config.yml等，这样如果未来某一天本地环境崩溃了，还可以很快的git clone恢复；

## 2. hexo + github.io的双分支部署步骤

1. 在github建立一个包含README.md的项目，命名为 username.github.io，主分支为master分支；

2. 直接在github branch的位置处新建一个coding-pages分支，并将其设置为默认分支。此时master分支和coding-pages分支应该都会有一个README.md文件，但实际上是不需要的，在后边可能需要用强制推分支的方法把这个README覆盖掉

3. 在本地的一个文件夹中（例如yixuan004.github.io文件夹）中初始化一个hexo项目
```shell
$ hexo init
```

4. 将本地的hexo项目与远程仓库关联，在本地的那个地方使用git init生成隐藏的.git文件
```shell
$ git init
``` 

在之后通过如下命令将远程的那个git关联上的感觉
```shell
$ git remote add origin https://github.com/yixuan004/yixuan004.github.io.git
```

在之后，由于远程的默认分支是coding-pages，所以本地也需要新建一个coding-pages分支（**注：本地的操作因为仅需要向coding-pages同步，所以本地工作也要切换到这个分支的感觉**）
```shell
$ git checkout -b coding-pages
```

由于远程之前可能有一个没什么用处的README，这里可以使用强制推的方法解决，但这个应该不是合理的解决办法，只是自己用来省事解决一下了
```shell
# 把本地的所有内容都加入git（hexo会自动生成一个git init，那些内容每次可能hexo clean会clean掉，所以要忽略那些）
$ git add . 
$ git commit -m "update ..."
$ git push -u origin coding-pages -f
```

5. 安装hexo向git推送的包（在文件夹下操作）
```shell
$ npm install hexo-deployer-git --save
```

6. 修改hexo项目中的_config.yml，这里要写上master分支，因为貌似hexo在github.io上默认显示master分支的内容
```yml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo: https://github.com/yixuan004/yixuan004.github.io
  branch: master
```

7. 推送相关（联合第4步使用）
```shell
$ git add .
$ git commit -m "update ..."
$ git push origin coding-pages
$ hexo clean # 这个步骤放在第一步也可以，但是感觉有gitignore在应该这样就可以了
$ hexo g
$ hexo d
```
**个人感觉远程的master分支是通过这个hexo自动推送，而远程的coding-pages分支是自己手工推送的感觉**

## 3. hexo + github.io的本地操作说明

### 3.1 新增博客文章
```shell
hexo new [文章名称]
```
在这之后，会在source/_posts目录下生成[文章名称].md文件，对其进行编写即可进行博客书写，注意在最开始会自动生成如下内容，可以通过自己编辑增加tags和categories
```markdown
---
title: 我的首篇github博客!
date: 2021-09-13 08:01:04
tags: 
    - hexo相关
categories:
    - 编程开发
---
```

### 3.2 本地修改运维操作
在本地完成一些修改后，建议顺序执行
```shell
$ git add .
$ git commit -m "update ..."
$ git push origin coding-pages
$ hexo clean
$ hexo g
$ hexo d
```

## 4. 切换hexo主题
可参考：
> https://github.com/stkevintan/hexo-theme-material-flow