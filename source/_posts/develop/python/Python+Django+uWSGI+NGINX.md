---
title: Python+Django+uWSGI+NGINX
date: 2022-12-7 22:25:50
tags:
    - python
categories:
	- develop
	- python
---

# Python+Django+uWSGI+NGINX

## 1. Python

### 1.1 Python的GIL问题，多进程与多线程 - 博客1

https://www.zhihu.com/search?type=content&q=Python%20GIL

再进一步了解多线程/多进程，uWSGI+NGINX这些之前，首先要明确Python的GIL问题

**经常听老手们说，python下多线程是鸡肋，提升效率推荐使用多进程，但是为什么这么说？**

#### 1.1.1 背景

1. GIL是什么，全称是Global Interpreter Lock（全局解释器锁），来源是python设计之初的考虑，为了数据安全（控制全局共享变量的访问。锁一定为了控制互斥变量的访问）
2. 在单核CPU下的多线程其实都是并发，不是并行。<font color='red'>并发和并行从宏观上来讲都是同时处理多路请求的概念，但并发和并行又有区别：并行是指两个或多个事件在同一时刻发生；并发则是两个或多个事件在同一时间间隔内发生</font> 

#### 1.1.2 线程的概念

并发执行机制原理：简单地说就是把一个处理器划分为若干个短的时间片，每个时间片依次轮流地执行处理各个应用程序，由于一个时间片很短，相对于一个应用程序来说，就好像是处理器在为自己单独服务一样，从而达到多个应用程序在同时进行的效果。

多线程就是把操作系统中并发的概念运用在一个程序之中，把一个程序划分为若干个子任务，多个子任务并发执行，每一个任务就是一个线程，这就是多线程程序；

#### 1.1.3 python多线程下，每个线程的执行方式

**case：**

这里联想python django框架，django框架以开发/调试的runserver模式启动是单进程，多线程的（可以使用参数配置成单线程的--nothreading）。这个是可以测试的，就是启动django后，有两个接口，第一个接口是直接返回，第二个接口是sleep20秒后返回。这时候先请求第二个接口，再请求第一个接口，还是第一个接口先返回内容；

**每个线程的执行方式如下：**

1. 获取GIL（全局解释器锁，Global Interpreter Lock）；

2. 执行代码直到sleep或者是python虚拟机将其挂起；
3. 释放GIL

**所以某个线程想要执行，必须先拿到GIL。可以把GIL看做“通行证”，并且在一个python进程中GIL只有一个，拿不到通行证的线程就不允许进入CPU执行；**

补充知识，GIL的释放逻辑：

- 在python2.x中GIL的释放逻辑：当线程遇到IO操作，或者ticks计数达到100（ticks可以看作是python自身的一个计数器，专门做用于GIL，每次释放后归零，可以通过`sys.setcheckinterval`来调整），进行释放

- 在python3.x中，GIL不使用ticks计数，而改用计时器（执行时间达到阈值后，当前线程释放GIL），可能相对的GIL切换与竞争消耗会少一些，对CPU密集型的程序更加友好，但依然没有解决GIL导致的同一时间只能执行一个线程的问题；

每次释放GIL锁，线程进行竞争、切换线程，会消耗资源。并且由于GIL锁的存在，python里一个进程永远只能同时执行一个线程（拿到GIL的线程才能执行）-> <font color='red'>导致**多核**CPU上，python的多线程效率不高</font> 

<font color='red'>不管分成多少事情，永远只有一个线程能在执行，但是多线程提供了多件事情之间竞争切换的可能。</font> 

**python不能用多线程实现并行，但是可以用多进程实现并行**

#### 1.1.4 是不是python的多线程就完全没用了

分类讨论

- CPU密集型代码（各种循环处理、计数等等）：ticks计数很快会达到阈值，触发GIL的释放与在竞争，多个线程之间来回切换和调度消耗资源，python下的多线程对CPU密集型代码是不友好的；
- IO密集型代码（文件处理、网络爬虫等）：多线程能够有效的提升效率（单线程下有IO操作会进行IO等待，造成不必要的浪费，而开启多线程后线程A在等待时-可看做sleep，自动切换到线程B，可以不浪费CPU的资源，从而能提升程序执行的效率），所以python的多线程对IO密集型代码比较友好；

#### 1.1.5 补充知识：创建多少线程合适

**其他语言的场景：**

https://blog.csdn.net/weixin_38333368/article/details/122313529

- 对于 CPU 密集型计算，多线程本质上是提升多核 CPU 的利用率，所以对于一个 4 核的 CPU，每个核一个线程，理论上创建 4 个线程就可以了，再多创建线程也只是增加线程切换的成本。所以 CPU 密集型的计算场景，理论上“线程的数量 =CPU 核数”就是最合适的。不过**在工程上，线程的数量一般会设置为“CPU 核数 +1”**，当线程因为偶尔的内存页失效或其他原因导致阻塞时，这个额外的线程可以顶上，从而保证 CPU 的利用率。
- 对于 I/O 密集型的计算场景，比如前面的例子中，如果 CPU 计算和 I/O 操作的耗时是 1:1，那么 2 个线程最合适。如果 CPU 计算和 I/O 操作的耗时是 1:2，那么 3 个线程最合适，如下图所示：CPU 在 A、B、C 三个线程之间切换，对于线程 A，当 CPU 从 B、C 切换回来时，线程 A 正好执行完 I/O 操作。这样 CPU 和 I/O 设备的利用率都达到了 100%。

**python语言的场景：**

Q：

何老板我来问一手问题，就是这个多线程这块的

说是python有这个GIL全局解释器锁，导致在多线程的时候不能充分利用多核CPU的资源

但是python的多线程也不是完全没用的，比如说提供了一种调度的可能，就是说会让各个线程来切换的释放和竞争锁，比如IO密集型似乎多线程就可以有用

上面不知道理解对不对，但是从我现在的感觉来说，那我python设置线程数是不是就不是很有用了？就是只有单线程和多线程的区别，而不太区分线程数是2 5 10还是其他这样的？

A：

python 多线程这一块我也不太懂。以前都说python的多线程是假的。不过貌似新版本有真的多线程了，我也不懂结果

#### 1.1.6 总结1

多核多线程，比单核多线程更差（<font color='red'>非常深入的问题了</font> ）：

单核下多线程，每次释放GIL，唤醒的那个线程都能获取到GIL锁，所以能够无缝执行，但多核下，CPU0释放GIL后，**其他CPU上的线程都会进行竞争，但GIL可能会马上又被CPU0拿到，导致其他几个CPU上被唤醒后的线程会醒着等待到切换时间后又进入待调度状态**，这样会造成线程颠簸(thrashing)，导致效率更低

回到最开始的问题，老手说，python下想要充分利用多核CPU，就用多进程，原因是什么？

- 每个进程各自有独立的GIL，互不干扰，就可以真正意义上的**并行**执行，所以在python中，多进程的执行效率优于多线程（多核CPU场景下）

#### 1.1.7 引申总结2

**1/**对于一组cpu密集型任务(即假设了他们都在几乎100%用cpu无IO阻塞)，总运行时间就是 每个任务的cpu执行时间求和，没有其他的。【要降低总时间，必须给更多的cpu核心，所有任务并行跑，至于选择多进程或多线程，都可以，区别是多线程开销更小多进程更安全，本来应该选多线程。但是python特殊点，由于gil, 所以多核心多线程近似等价于多核心单线程，还有额外的线程调度颠簸的开销。所以python选多进程】
【但是从用户角度】
即使是单核使用多进程，不能减少总cpu执行时间，但是时间片轮转能让所有任务都能得到调度，用户体验较好(因为如果是单线程，那下一个任务就只能等待上一个任务全部完成才能开始，)
如果是多核，每个核上跑一个任务，如果假设这些任务之间几乎是独立的(就是很少有竞争共享变量这种交互)，那么就是纯粹意义上的并行。所以大家建议 设置进程数小于等于核心数，避免进程大于核心数 需要做cpu切换进程调度开销。

**2/**对于一组io密集型任务，每个任务的运行时间是 cpu计算时间+io时间(io时完全不需要cpu, 它就是纯粹等待数据传输直至完成，所以将它挂起剥夺cpu它还是在io等待不影响它io)。
所以一个最简单的改进方案是，让A任务的io时间和B任务的cpu时间重叠，即A任务做io等待时让出cpu给B任务做cpu计算，等B任务io等待时让出cpu给C任务做cpu计算，诸如此类。并发任务数越多，时间重叠越多，纯粹io空等的时间浪费就被减少了。
如果用是单核 多进程或多线程，都可以。但是多线程的线程切换开销更小，以及多进程内存开销大，所以更考虑多线程。(gil? 无所谓，本来每个时刻就只能运行一个线程，如果A等待，就不仅让出cpu也让出gil, 让B开始运行)。这里的本质还是减少了io空等时间，让这部分空等时间给了其他进程运行。
如果是多核的多进程或多线程。多进程，其实是OK的，因为一个核上一个进程，互不影响。但是多进程的内存成本高啊，一个进程一个内存空间，而速度和单核的多线程又是一样的。。。所以不选多进程。如果选多线程。但是，由于gil影响，所有核心同时只能运行一个线程。。。这就把每个核上单独运行一个线程 变成 所有核上只能运行一个线程。没有线程级别的多核并行了。而且还有多核调度gil的颠簸(cpu0刚放出gil,其他核也去抢但是肯定没有cpu0成功率高，结果其他核心上的线程开始抢又没抢到，线程状态颠簸，性能开销 )。

**3/**画个2*2*2表格可能更直观。8种情况
cpu密集型，io密集型
单核，多核
多进程，多线程

综上，
cpu密集型，多核的多进程并行(不是并发)；io密集型，单核的多线程并发(不是并行，其他语言其实应该选多核的多线程并行加并发，但python应该选单核并发)

几个关键知识点
(1)减少cpu运行时间只能是多核并行跑多个子任务
(2)GIL全局锁影响的是并行而不是并发。它会导致多核上面的多线程并行 等价于 多线程并发，以及额外的线程颠簸，所以python多核选多进程
(3)cpu密集型，唯一的办法是需要用多核并行来减小总cpu计算时间，因此多核多进程并行。
(4)io密集型，最合理的办法是让多任务并发，让A任务io阻塞时间和B任务cpu计算时间重叠而不是串行先后，从而【单个任务总运行时间没有减少，但是时间重叠，导致多个任务总运行时间减少了，画张overlap图一目了然】。因此选择单核多线程。(因为线程相比进程，内存占用少 ，切换开销也小。这个时候本来就是并发的，所以gil不影响单核多线程的并发)

**再提一点。**
**这种cs基础，一定先讲通理论，以及普适于各种编程语言的情况，再具体到python有啥特殊性。这才是正确的触类旁通。**

### 1.2 python多线程/多进程全面整理

https://article.itxueyuan.com/oJDLgn

进程是资源分配的最小单位，线程是CPU调度的最小单位

### 1.3 Python包相关

项目部署主要是用的离线部署，不太熟悉docker的各种操作

先把虚拟环境所有的打包成requirements.txt，然后按照requirements.txt的内容下载离线安装包到一个文件夹中。这里注意下载的离线包可能有linux操作系统不同的问题，所以和离线部署那边使用的是相同的操作系统，测试环境下线上安装，然后部署到现网系统

安装的时候注意先把pip升级一下，这个感觉还是个挺大的大坑，容易导致后续的安装出各种问题

https://www.cnblogs.com/ibgo/p/11441942.html

```shell
# 0. 建立虚拟环境
python -m venv MyApp

# 1. 输出虚拟环境中已安装的包的名称，以及版本号，饼记录到requirements.txt文件中
(MyApp)xxx> pip freeze > requirements.txt

# 2. 将安装的包保存到文件夹，如这里指定的packages文件夹
(MyApp) xxx>  pip download -r requirements.txt -d packages   

————————————————————————————————————————————————————————————————————————

# 3. 在另一台电脑新建虚拟环境
python -m venv MyApp

# 4. 将requirements.txt和packages复制到虚拟环境里，激活虚拟环境后安装包
(MyApp) yyy> pip install --no-index --find-links=packages -r requirements.txt
```

对这里的`--no-index`和`--find-links`来解释一下

`--no-index`忽略软件包默认的索引位置（pypi.org），仅查看`--find-links`这个目录的内容

`--find-links`会使pip在指定目录检索是否有依赖包并安装，就不用一个个顺序的pip install了

**自己还尝试过花式的部署方式，就是写个脚本一直在那块装，然后直到把所有的装上**

```shell
while read requirement; do pip install --no-index $requirement; done < requirements.txt 
```

### 1.4 Python八股文

#### 1.4.1 python中类方法、类实例方法、静态方法有何区别？

- 类方法：定义时加入修饰符`@classmethod`，形参为`cls`，表示为类对象，类对象和实例对象都可以使用，加入后，可以用cls调用该类中的其他方法 <font color='red'>（加cls，加classmethod修饰，可以调用该类中的其他cls.xxx，然后类对象和实例都可以用）</font> 

- 类实例方法：是类实例化对象的方法，只有实例对象可以调用，形参为self <font color='red'>（加self，不需要修饰，只有实例化才能用）</font> 

- 静态方法：是任意一个函数，在其上方使用@staticmethod进行调用，可以用对象直接调用，实际上和这个类没什么关系<font color='red'>（不加self和cls，用staticmethod修饰）</font> 

#### 1.4.2 python深拷贝和浅拷贝的区别

- 浅拷贝将**对象的引用**存储在新的内存位置。对新位置所做的更改也会反映在以前的位置上。它比深拷贝更快；

- 深拷贝将**对象的值**存储在新位置。对新位置所做的任何更改都不会反应在以前的位置上；

#### 1.4.3 is和==的区别

is是看地址是否一样

==看的是值

```python
>>> a = [1,2,3,4,5]
>>> b = [1,2,3,4,5]
>>> a == b
True
>>> a is b
False
```

#### 1.4.4 python中list和tuple的区别

list可被修改（append pop...），而元组不可修改。元祖效率更高，并且可以当做哈希映射中的key

#### 1.4.5 什么是`__init__`

`__init__`方法是python中的方法或者结构，在创建类的新对象/实例的时候，将自动调用该方法来分配内存

## 2. Django+uWSGI

### 2.1 Django的开发环境与生产环境

在开发/调试环境下，可以直接使用Django的runserver的模式运行Django框架的http服务

```shell
python manage.py runserver 0.0.0.0:8080
```

而在生产环境下，需要用NGINX+uWSGI+Django的模式，这是**由于性能的关系**

### 2.2 Django+uWSGI的多进程多线程

- python manage.py runserver默认的是多线程（Django1.x版本后），--nothreading是单线程

- Flask框架默认的是单线程
- 不给uWSGI加进程，uWSGI默认的是单进程单线程，通过如下命令可以给uWSGI加进程和线程

```shell
--processes 4 --threads 2
```

根据上面1.1和1.2的解释，python应当使用多进程，而不是太care线程，进程数可以等于CPU核数或者CPU核数-1，避免进程调度带来的消耗；

### 2.3 测试Django Server默认多线程

单线程的意思是，当一个请求没有完成的时候，第二个请求会阻塞，直到第一个请求完成，第二个请求才会执行

所以，使用django封装两个http接口，第一个接口直接返回内容，第二个接口sleep20秒后返回内容。

这时候先请求第二个接口，紧接着再请求第一个接口，可以发现第一个接口返回数据，第二个接口在20秒之后返回数据，证明了django的server是默认多线程；

**单线程的情况是说：当一个请求没有完成时，第二个请求会阻塞，直到第一个请求完成，第二个请求才会执行**

### 2.4 Django多线程带来的问题/踩坑

https://www.jianshu.com/p/4476664c21ea

默认的runserver方式启动，多个请求一块过来的时候，前一个请求的全局变量被后一个请求篡改了，所以django默认的是单进程多线程，多线程共享一份进程的数据，所以在用django实现服务器的时候，一定注意全局变量的使用

踩坑的过程是：把返回的那个json的dict写成全局的了，在压测批测的时候就发现返回的数据串了，后来发现是全局变量这个问题

简单的测试可以通过如下方式来证明：

先访问test1接口，test1对应函数中有sleep，不会马上返回。再访问test2接口。发现10s后，test1接口返回数值为2。可验证全局变量a被test2修改

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221110113747232.png" alt="image-20221110113747232" style="zoom:50%;" />

## 3. NGINX - 基本了解

https://zhuanlan.zhihu.com/p/152526491

### 3.1 nginx产生

NGINX是一种Web服务器，基于REST架构风格。<font color='red'>REST风格架构见计算机基础笔记</font> 

REST架构风格就是说，以统一资源描述符（Uniform Resources Indentifier, URI）或统一资源定位符(Uniform Resources Locator, URL)作为沟通依据，通过HTTP协议提供各种网络服务；

Apache可以算成NGINX的上一代技术？Apache不支持高并发的服务器，在Apache上运行数以万计的并发访问，会导致服务器消耗大量内存，总结来说，Apache不可能成为高性能web服务器，**轻量级高并发服务器NGINX就应运而生！**

基于以下几点，nginx火了

1. nginx使用基于事件驱动的架构，使得其可以支持数百万级别的tcp连接；
2. 高度的模块化和自由软件许可证，可使得第三方模块层出不穷；<font color='red'>电信可能有自己的魔改版？</font> 
3. nginx是一个跨平台服务器，可以运行在linux windows macos等等操作系统上；
4. 优秀的设计带来极大的稳定性；

### 3.2 nginx的用武之地

关于代理

1. 说到代理，首先要明确一个概念，所谓代理就是**一个代表、一个渠道**；此时就涉及**两个角色**，一个是**被代理角色**，一个是**目标角色**

2. 被代理角色通过这个代理访问目标角色，完成一些任务的过程，被称作代理操作过程。
3. 举例如生活中的专卖店：adidas厂家、adidas专卖店、用户，**专卖店就是代理**、**用户就是目标角色**、**厂家就是被代理角色**

#### 3.2.1 正向代理 - 传说中的翻墙

从**软件方面**和**生活方面**来解释一下什么叫正向代理

如果需要去访问国外的某些网站，那么位于国外的网站，通过浏览器是没有办法直接访问的

此时可能会用一个操作翻墙访问——找到一个可以访问国外网站的代理服务器，我们将请求发送给代理服务器，代理服务器去访问国外网站，然后将访问到的数据传递给我们。<font color='red'>这个流程还是比较顺畅的理解，就是我们去找代理，代理去把东西买回来给我们</font> 

上述这样的代理模式称为**正向代理**，最大的特点是客户端非常明确要访问服务器地址；服务器只清楚请求来自哪个代理服务器，而不清楚来自哪个具体的客户端，正向代理屏蔽或者隐藏了真实客户端信息。

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221110210633587.png" alt="image-20221110210633587" style="zoom:50%;" />

客户端（Client）必须设置正向代理服务器，当然前提要知道代理服务器的IP地址，还有代理程序的端口

**总结来说：**

正向代理，他代理的是客户端（Client），是一个位于客户端和原始服务器之间的服务器。为了从原始服务器获得内容，客户端向代理发送一个请求并指定**目标（原始服务器）**

然后代理向原始服务器转交请求，并将获得的内容返回给客户端。客户端必须要进行一些特别的设置，才能使用正向代理

**正向代理的用途：**

1. 访问原来无法访问的资源，如google；
2. 可以做缓存，加速访问资源；
3. 对客户端访问授权，上网进行认证（VPN）；
4. 代理可以记录用户访问记录（上网行为管理），对外隐藏用户信息；

#### 3.2.2 反向代理 - 客户端向代理服务器请求，但不知道真正去哪台服务器

继续看关于反向代理的处理方式，例如淘宝网站，每天同时连接到网站的人数已经爆表，单个服务器远远承受不住

出现了名词：**分布式部署**，通过部署多台服务器，来解决访问人数限制的问题

反向代理具体是通过什么方式实现分布式的集群操作呢，我们先看一个示意图（服务器和反向代理框在一块，同属于一个环境）：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221110212634061.png" alt="image-20221110212634061" style="zoom:50%;" />

多个客户端给服务器发送的请求，nginx服务器接收到之后，按照一定的规则分发给了后端的业务处理服务器进行处理了

此时请求的来源，也就是客户端是明确的，但**具体由哪台服务器进行处理并不明确了**，nginx扮演的就是一个反向代理的角色

客户端是无感知代理的存在的，反向代理对外都是透明的（这里是说不可见的），访问者并不知道自己访问的是一个代理，因为客户端不需要任何配置就可以访问

<font color='red'>**反向代理，他代理的是服务端，主要用于服务器集群分布式部署的情况下，反向代理隐藏了服务器的信息（而不是正向代理隐藏了用户的信息）**</font> 

**反向代理的作用：**

通常情况下，我们在实际操作项目时，正向代理和反向代理很有可能会存在于同一个应用场景中，正向代理代理客户端去访问目标服务器，目标服务器是一个反向代理服务器，反向代理了多台真实业务处理服务器

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221110214255820.png" alt="image-20221110214255820" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221110214316866.png" alt="image-20221110214316866" style="zoom:50%;" />

图解：

**在正向代理中，Client和Proxy同属于一个LAN（局域网）（图中方框内），隐藏了客户端信息；**

**在反向代理中，Proxy和Server同属于一个LAN，隐藏了服务端信息**

正向代理和反向代理区别总结：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221110214711585.png" alt="image-20221110214711585" style="zoom:50%;" />

#### 3.2.3 负载均衡

明确了代理服务器的概念，那么NGINX扮演了反向代理服务器的角色，他是依据什么规则进行请求分发的呢？不同的项目应用场景，分发的规则是否可以控制呢？

这里客户端向nginx反向代理服务器发送的，nginx反向代理服务器接收的请求量，就是我们说的负载量。

**请求数量按照一定的规则进行分发，到不同服务器处理的规则，就是一种均衡规则。**

**所以将服务器接收到的请求按照规则分发的过程，称为负载均衡。**

负载均衡在实际项目操作过程中，有硬件负载均衡和软件负载均衡两种：

- 硬件负载均衡也称为硬负载，如F5负载均衡（这是一种硬件的负载均衡），相对造价昂贵成本较高。但是数据的稳定性安全性等等有非常好的保障，如移动、联通、银行等等公司才会进行硬负载操作；
- 考虑成本原因，更多公司会用软件负载均衡，软件负载均衡是利用现有的技术结合主机硬件实现的一种消息队列分发机制

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221111094046536.png" alt="image-20221111094046536" style="zoom:50%;" />

### 3.3 nginx支持的负载均衡调度算法

① weight轮训（默认）：接收到的请求按照顺序逐一分配到不同的后端服务器，即使在使用过程中，某一台后端服务器宕机，nginx会自动将该服务器踢出队列，请求受理情况不会受到任何影响

这种方式下，可以给不同的后端服务器设置一个权重值（weight），用于调整不同的服务器上请求的分配率。

权重数据越大，被分配到请求的几率越大。该权重值，主要是针对实际工作环境中不同的后端服务器硬件配置进行调整的

② ip_hash：每个请求按照客户端ip的hash结果进行匹配，这样的算法下，一个固定ip地址的客户端总会访问到同一个后端服务器，一定程度上解决了集群部署环境下，session共享的问题；

③ fair：智能调度算法，动态的根据后端服务器的请求处理到响应的时间，进行均衡分配。

响应时间短处理效率高的服务器分配到的请求概率高，响应时间长处理效率低的服务器分配到的请求少。

nginx默认不支持fair调度算法，需要安装upstream_fair模块；

④ url_hash：按照访问的URL的hash结果分配请求，每个请求的URL会指向后端固定某个服务器，可以在nginx作为静态服务器的情况下提高缓存效率。

同样要注意nginx默认不支持这种调度算法，要使用的话需要安装nginx的hash软件包；

### 3.5 集群产生的session共享问题

<font color='red'>cookie和session的说明，详见计算机网络笔记</font> https://blog.csdn.net/ajiaxiaohua/article/details/126943111

### 3.6 nginx是一种消息队列中间件

kafka也是一种消息队列

## 4. NGINX+uWSGI+Django一个联合起来的说明

Django项目上线使用nginx+uwsgi提供高并发：https://zhuanlan.zhihu.com/p/496354827

uWSGI、uwsgi、WSGI、之间的关系，为何要用nginx加uWSGI部署Django：https://blog.51cto.com/u_14575624/4836128

### 4.1 背景

在做django项目的时候，一般开发和测试的时候，直接用Django内嵌的web server即可（单进程，多线程，并发能力不足）。但是如果项目要上生产环境的时候，考虑并发性能的时候就需要加上uwsgi和nginx，带来了高并发的性能

### 4.2 uWSGI、uwsgi、WSGI之间的关系，为何要用nginx+uWSGI部署Django

uWSGI、uwsgi、WSGI、之间的关系，为何要用nginx加uWSGI部署Django：https://blog.51cto.com/u_14575624/4836128

#### 4.2.1 WSGI：Web Service Gateway Interface，一种协议规范

WSGI不是服务器、python模块、框架、API或者软件中的一种，只是一种规范。描述web server如何与web application通信的规范。

要实现WSGI协议，必须同时实现**web server（uWSGI）**和**web application（Django）**，当前运行在WSGI协议之上的web框架有Flask Django；



是一种协议规范，起到参数规范的作用，就和高速公路一样，规定车道靠右行，速度不低于90km/h，这一些都是双方规定好的。WSGI沟通的双方是wsgi server（uWSGI）和wsgi application（Django）

**实现WSGI协议规范的web服务器叫做WSGI服务器，也就是uWSGI服务器（gunicorn也是一种web server，实现了WSGI协议规范）**

**实现WSGI协议规范的应用（application），叫做WSGI应用，比如Django，Flask**

#### 4.2.2 uwsgi

与WSGI一样是一种通信**协议**，是**uWSGI服务器的独占协议**，用于定义传输信息的类型；

#### 4.2.3 uWSGI

是一个**web服务器**，实现了WSGI协议，uwsgi协议、http协议等。

也可以称作**WSGI server服务器**，任务就是接受用户请求，因为用户请求是经过网络发过来的，其中用户到服务器端之间用的是http协议，因此uWSGI想要接受而且正确解出相关信息，就**必须要uWSGI实现http协议。**没错，uWSGI里就实现了http协议，因此uWSGI能接收到用户请求，而且读出信息。

而如今咱们的uWSGI服务器需要把信息发给Django，就需要用到WSGI协议，uWSGI实现了WSGI协议。因此，uWSGI把接收到的信息做一次简单封装传递给Django，再通过一层层的中间件对信息做进一步的处理，最后匹配url，传递给相应的views.py那个函数，这个函数做逻辑处理，然后将处理后的数据经过中间件一层层返回，到达Django的最外层，而后，经过WSGI协议将返回数据返回给uWSGI服务器，uWSGI服务器将http协议将数据传递给用户，这就是整个流程。

<font color='red'>上述过程用到了WSGI协议和http协议，而还没有用到uwsgi协议，那个是和nginx交互过程中用的</font> 

使用`python manage.py runserver 0.0.0.0`可以启动Django自带的服务器，是单进程多线程的（runserver起来的HTTPServer就是Python自带的simple_server）

用uwsgi这个WSGI server服务器启动的时候，可以多进程的效果

#### 4.2.4 为何有了uWSGI还要用NGINX

由于nginx有很好的静态内容处理能力，而后将动态内容转发给uWSGI服务器，这样就能够达到很好的客户端相应。支持的并发量更高，方便管理多进程，发挥多核的优点，提高性能。**这时候nginx和uWSGI之间的沟通就要用到uwsgi协议**

django并发能力弱->使用nginx+uWSGI来提供高并发

nginx并发能力高，单台并发能力过万，在纯静态的web服务中更是突出其优越的地方，因为其底层使用 epoll 异步IO模型进行处理，使其深受欢迎服务器。<font color='red'>这里可能之后要去dfs一下</font> 

**<font color='red'>静态请求 vs 动态请求</font>** 

1. 静态请求

客户端访问服务器的静态网页, 不涉及任何数据的处理, 如下面的URL:

http://localhsot/login.html

2. 动态请求

客户端会将数据提交给服务器

​	使用get方式提交数据得到的url

​	http://localhost/login?user=zhang3&passwd=123456&age=12&sex=man

uWSGI作为一款优秀的python应用服务器，更擅长处理动态请求，实际开发中一般用nginx处理静态请求，动态请求转发给uWSGI服务器处理<font color='red'>从自己的理解来说，就是uWSGI起起来了多个进程，nginx负载均衡这样的？</font> 

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221111152757194.png" alt="image-20221111152757194" style="zoom:50%;" />

nginx作为web服务器有以下特点：


它处理静态文件和索引文件效果非常高，配合uWSGI服务器会更加安全；
它的设计非常注重效率，最大支持 5 万个并发连接，但只占用很少的内存空间；
稳定性高，配置简洁
强大的反向代理和负载均衡功能，平衡集群中各个服务器的负载压力应用

而uWsgi服务器虽然也能够处理静态请求，但效率远不如nginx，并且从安全性和可扩展性方面来讲，使用nginx+uwsgi是最佳方式。所以，一般python后端开发一般采取nginx+uwsgi+Django/Flask应用的方式部署。



**Python须要使用nginx + uwsgi 提供静态页面访问，和高并发多线程**

php 须要使用 nginx + fastcgi 提供高并发，

java 须要使用 nginx + tomcat 提供 web 服务



Python由于GIL的存在，在一个进程中，只容许一个线程工做，致使单进程多线程没法利用多核

多进程的线程之间不存在抢GIL的状况，每一个进程有一个本身的线程锁，多进程多GIL



https://blog.csdn.net/ieeso/article/details/105040234?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-105040234-blog-124815686.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-105040234-blog-124815686.pc_relevant_aa&utm_relevant_index=2

WSGI（Web Server Gateway Interface），翻译为Python web服务器网关接口，即Python的Web应用程序（如Flask）和Web服务器(如Nginx)之间的一种通信协议。也就是说，如果让你的Web应用在任何服务器上运行，就必须遵循这个协议。

那么实现WSGI协议的web服务器有哪些呢？就比如uWSGI与gunicorn。两者都可以作为Web服务器。可能你在许多地方看到的都是采用Nginx + uWSGI（或gunicorn）的部署方式。实际上，直接通过uWSGI或gunicorn直接部署也是可以让外网访问的，那你可能会说，那要Nginx何用？别急，那么接来下介绍另一个Web服务器——Nginx

**Nginx作为一个高性能Web服务器，具有负载均衡、拦截静态请求、高并发...等等许多功能**，你可能要问了，这些功能和使用Nginx + WSGI容器的部署方式有什么关系？

首先是负载均衡，如果你了解过OSI模型的话，其实负载均衡器就是该模型中4~7层交换机中的一种，它的作用是**能够仅通过一个前端唯一的URL访问分发到后台的多个服务器**，**这对于并发量非常大的企业级Web站点非常有效**。在实际应用中我们通常会让Nginx监听（绑定）80端口，通过多域名或者多个location分发到不同的后端应用。<font color='red'>单机场景下，有必要使用nginx+uWSGI吗？</font> 

其次是拦截静态请求，简单来说，Nginx会拦截到静态请求（静态文件，如图片），并交给自己处理。而动态请求内容将会通过WSGI容器交给Web应用处理;

Nginx还有其他很多的功能，这里便不一一介绍。那么前面说了，直接通过uWSGI或gunicorn也可以让外网访问到的，但是鉴于Nginx具有高性能、高并发、静态文件缓存、及以上两点、甚至还可以做到限流与访问控制，所以选择Nginx是很有必要的；

这里可以说明，如果你选择的架构是：Nginx + WSGI容器 + web应用，WSGI容器相当于一个中间件；如果选择的架构是uWSGI + web应用，WSGI容器则为一个web服务器

#### 4.2.5 为什么NGINX可以直接部署，还要uWSGI、gunicorn等中间件？

https://www.zhihu.com/question/342967945

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221111154658215.png" alt="image-20221111154658215" style="zoom:67%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221111154718344.png" alt="image-20221111154718344" style="zoom:50%;" />



