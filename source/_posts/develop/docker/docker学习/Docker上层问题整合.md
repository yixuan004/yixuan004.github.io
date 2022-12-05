---
title: Docker上层问题整合
date: 2022-12-03 21:09:18
tags:
  - docker
categories:
	- develop
	- docker
	- docker学习
---

# Docker上层问题整合

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221203212303162.png" alt="image-20221203212303162" style="zoom:25%;" /><img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221203212319604.png" alt="image-20221203212319604" style="zoom:25%;" /><img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221203212345547.png" alt="image-20221203212345547" style="zoom:25%;" />

Reference：

什么是Docker？看这一篇干货文章就够了！https://www.zhihu.com/search?type=content&q=docker

Docker官网：https://www.docker.com/

## 1. 容器技术的起源 （container）

假设公司正在开发一套系统，程序员自己从**头到尾搭建了一套环境**开始写代码

写完代码后程序员要把代码交给测试同学测试，这时候测试同学开始**从头到尾搭建这套环境**，测试过程中出现问题程序员也不用有问题，把锅直接甩给测试同学，因为明明在我的环境上是可以运行的hhhhhhh

测试同学测试完成后，终于可以上线了，这时候运维同学又要**重新从头到尾搭建这套环境**，废了很大的力气搭建好环境开始上线，上线系统就崩溃了，也是很有可能的

复盘上述过程：不仅重复搭建了多套环境，同时还有软件、代码版本不同步的问题，**容器技术应运而生**



在以往，先搭好一套虚拟机环境（VMware）然后给测试和运维clone出来就可以了，在容器技术产生之前确实还是好办法，但是这个方法没有那么好

现在云计算及其底层就是虚拟机技术，云计算厂商买回来一堆硬件，搭建好数据中心后使用虚拟机技术就可以将硬件资源进行切分了，比如切分出100台虚拟机，这样就可以卖给很多用户了，那么虚拟机技术有什么不足呢？

## 2. 容器技术 vs 虚拟机

我们知道和一个单纯的应用程序相比，**操作系统是一个很重而且很笨重的程序**

操作系统需要占用很多资源，大家对此肯定深有体会，刚装好的系统什么都没有部署，磁盘占用几十G，内存占用几G。使用虚拟机技术来说，需要部署3个应用，虚拟机技术可以这样划分：开三个虚拟机，VM1占用2GB内存，VM2占用1GB内存，VM3占用4GB内存：

![image-20221203214444105](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221203214444105.png)

这样虚拟机本身内存就占了7GB（2+1+4），我们没有办法划分出更多虚拟机，从而部署更多的应用程序，我们部署的是应用程序，要用的也是应用程序而不是操作系统

如果有一种技术，可以避免把内存等资源浪费在“无用”的操作系统上岂不太香？**这是问题一，主要原因在于操作系统太重了**

**还有另一个问题，就是启动的问题，操作系统启动是非常慢的**，因为操作系统要从头到尾把该检测了的检测了、该加载的加载了，这个过程非常缓慢，还是太慢了

**<font color='red'>容器技术的提出，获得了虚拟机运行应用程序的好处，而改善了操作系统存在的上述两个问题</font>** 

## 3. 什么是容器

容器一词的英文是container，其实container还有集装箱的意思，集装箱绝对是商业史上一项伟大的发明，降低了海洋贸易运输成本，来看看集装箱的好处

- **集装箱之间相互隔离**
- **长期反复使用**
- **快速装载和卸载**
- **规格标准，在港口和船上都可以摆放**

回到软件中的容器，容器和集装箱在概念上很相似的！

现代软件开发的一大目的就是隔离，应用程序在运行时相互独立互不干扰，这种隔离其实实现起来很不容易的，一种解决方案是上面提到的虚拟机技术，通过将应用程序部署在不同的虚拟机中实现隔离，图例如下

![image-20221203220720058](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221203220720058.png)

虚拟机有上述提到的各种缺点，容器技术则一定程度上克服了这些缺点

与虚拟机通过操作系统实现隔离不同，容器技术之隔离应用程序的运行时环境，但容器之间可以共享同一个操作系统，这里的运行时环境指的是应用程序运行依赖的各种库以及配置

![image-20221203223346199](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221203223346199.png)

从图中可以看到，**容器更加轻量级而且占用的资源更少**，与操作系统动辄几G的内存占用相比，容器技术只需要数M的空间，因此我们可以在同样规格的硬件上**大量部署容器**，这是虚拟机所不能比拟的，而且不同于操作系统分钟级的启动，几乎瞬时启动，容器技术为打包服务栈提供了一种更加高效的方式

那么我们该怎么使用容器呢？就要讲到docker了

注意，容器是一种通用技术，docker只是其中的一种实现

## 4. 什么是docker 

docker是一个使用Go语言实现的开源项目，可以让我们方便的创建和使用容器，docker将程序以及程序所有的依赖都打包到docker container，**这样程序在任何环境都会有一致的表现**

这里的程序运行依赖的容器（container）好比集装箱，容器所处的操作系统环境就好比货船/港口，程序的表现只和集装箱（容器）有关系，和集装箱放在哪个货船或哪个港口（操作系统）没有关系。

因此我们看到docker是可以屏蔽环境差异的，也就是说，只要程序打包到了docker中，那么无论在什么环境下程序的行为都是一致的，不会再有“在我的环境上可以运行”的程序员鬼话了hhhhh，真正实现**“build once, run every where”**

此外docker的一个好处就是实现快速部署，当前公司的一个常见应用场景，一个原因是容器启动速度非常快，另外一个原因就是确保一个容器中的程序正确运行，那么就能确信无论在生产环境部署多少都能正确运行！

## 5. 如何使用docker

docker中有几个这样的概念：

- Dockerfile
- image
- container

实际上可以把image理解为可执行程序，container就是运行起来的进程

那么写程序需要源代码，写image就需要Dockerfile，Dockerfile就是image的源代码，docker就是“编译器”

因此我们只需要在Dockerfile中指定需要哪些程序、依赖什么样的配置，之后把Dockerfile交给“编译器”docker进行“编译”，这就是docker build命令，生成的可执行程序就是image，之后就可以运行这个image了，这就是docker run命令，image运行起来之后就是docker container；

### 5.1 补充问题：为什么在编写dockerfile的过程中，需要减小层数，把RUN中的所有命令用&&连接到一起？

Reference：

https://blog.csdn.net/liumiaocn/article/details/103175638

https://blog.csdn.net/liumiaocn/article/details/100922774

https://blog.csdn.net/weixin_42309691/article/details/123016159

使用Dockerfile进行镜像构建，离不开RUN命令，相较于docker run的run命令，Dockerfile中的RUN是镜像创建阶段使用的命令，而docker run则是使用镜像阶段启动的命令，主要介绍RUN命令常见的技巧和使用注意事项；

#### 5.1.1 基本原则1：尽量减少一个Dockerfile中RUN命令的个数

RUN命令在构建的时候会创建一个新层，如非特殊的必要，建议一个Dockerfile在需要使用RUN命令的时候，尽可能只用一个RUN命令，将多条RUN命令进行合并，可以有效降低构建镜像的层数

<font color='red'>层数指什么？</font> 

比如说有多个镜像都从相同的base镜像构建而来，那么Docker Host只需要在磁盘上保存一份base镜像，同时内存中也只需要加载一份base镜像，就可以为所有的容器服务了。而且镜像的每一层都可以被共享。

## 6. docker是如何工作的？

实际上docker使用了常见的Client-Server架构，也就是Client-server模式，docker client负责处理用户输入的各种命令，比如docker build、docker run，真正工作的其实是server，也就是docker demon，值得注意的是，docker client和dcoker demon可以运行在同一台机器上

讲解一下docker的工作流程

### 6.1 docker build

当我们写完dockerfile，交给docker“编译”的时候使用这个命令，在client接收到后把请求转发给docker daemon，接着docker daemon根据dockerfile创建出“可执行程序”image

![image-20221204000446589](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221204000446589.png)

自己来理解一下这个图，dockker build命令从Client发给Docker daemon，然后构建出images来，images也可以直接使用别人的images，比如这里图里画的redis

### 6.2 docker run

有了“可执行程序”image后就可以运行程序了，接下来使用命令docker run，docker daemon接收到了该命令后找到具体的image，然后加载到内存开始执行，image执行起来就是所谓的container

![image-20221204000957700](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221204000957700.png)

自己再来理解一下，就是从images那边，再执行变成进程container，一个image可以启动多份进程

<font color='red'>（便于进行A/B test？）</font> 

### 6.3 docker pull

docker build和docker run是两个最核心的命令，会有这两个命令基本上docker就可以用起来了，剩下就是一些补充，那么docker pull是什么意思

docker中的image类似于“可执行程序”，我们可以从哪里下载到别人写好的应用程序呢？很简单，那就是比如App Store，即应用商店。与之类似，既然image也可以被看作是一种“可执行程序”，那么有没有“Docker Image Store”呢？答案是肯定的，这就是Docker Hub，docker官方的“应用商店”，你可以在这里下载到别人编写好的image，这样就不用自己编写dockerfile了（或者说，可以在别人镜像的基础上来进一步构建）

docker registry可以用来存放各种image，公共的可以攻任何人下载image的仓库就是Docker Hub，那么该怎么从Docker Hub中下载image呢，就要用到docker pull命令了

因此，这个命令的实现也很简单，就是用户通过docker client发送命令，docker daemon接收到命令后向docker registry发送image下载请求，下载后存放在本地，这样我们就可以使用image了

![image-20221204145529900](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221204145529900.png)

## 7. Docker的底层实现

docker基于linux内核提供这样几项功能实现的：

- NameSpace

我们知道Linux中的PID、IPC、网络等资源是全局的，而NameSpace机制是一种资源隔离方案，在该机制下这些资源就不再是全局的了，而是属于某个特定的NameSpace，各个NameSpace下的资源互不干扰，这就使得每个NameSpace看上去就像一个独立的操作系统一样，但是只有NameSpace还是不够

- Control groups

虽然有了NameSpace技术可以实现资源隔离，但进程还是可以不受控的访问系统资源，比如CPU、内存、磁盘、网络等，为了控制容器中进程对资源的访问，docker采用control groups技术（也就是cgroup），有了cgroup就可以控制容器中进程对系统资源的消耗了，比如你可以限制某个容器使用内存的上限、可以在哪些CPU上运行等

## 8. 总结

![image-20221204150854246](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221204150854246.png)

docker是目前非常流行的技术，很多公司在生产环境中使用，但是docker依赖的底层技术实际上很早就出现了， 以docker的形式重新焕发活力，并且能很好的解决面临的问题