---
title: redis基本数据类型：“5种基础”数据类型+“3种特殊”数据类型
date: 2022-11-21 23:43:50
tags:
    - redis
categories:
	- develop
   - redis
---

该篇博客介绍redis的基本数据类型，以及基础类型的背后实现原理（底层）

“5种基础”数据类型：String、List、Set、Zset、Hash；

“3种特殊”数据类型：HyperLogLogs（基数统计），Bitmaps（位图）和geospatial（地理位置）；

- Redis基本数据类型；

- Redis底层数据结构；

<!--more-->

# redis基本数据类型：“5种基础”数据类型+“3种特殊”数据类型

reference: 

https://gogocoder.com/home

https://zhuanlan.zhihu.com/p/371375530

## 1. “5种基础”数据类型

redis所有的key（键）都是字符串。我们在谈基础数据结构时，讨论的是存储value（值）

主要包括常见的5种数据类型，分别是：String、List、Set、Zset、Hash

### 1.1 redis数据结构简介

首先对于redis来说，所有的key都是字符串。我们在谈基础数据结构时，讨论的是存储值的数据类型，主要包括常见的5种数据类型：分别是String、List、Set、Zset、Hash

![image-20221122004411738](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221122004411738.png)

![image-20221122004634498](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221122004634498.png)

- String字符串

结构存储的值：可以是字符串、整数或浮点数。

结构的读写能力：对整个字符串或字符串的一部分进行操作；对整数或者浮点数进行自增或自减操作；

- List列表

结构存储的值：一个链表，链表上的每个节点都包含一个字符串

结构的读写能力：对链表的两端进行push和pop操作，读取单个或多个元素；根据值查找或删除元素

- Set集合

结构存储的值：包含字符串的无序集合

结构的读写能力：字符串的集合，包含基础的方法有查看是否存在、获取、删除；还包含计算交集、并集、差集等；

- Zset集合

结构存储的值：包含键值对的散列表，有序

结构的读写能力：字符串成员与浮点数之间的有序映射；元素的排列顺序由分数的大小决定；包含方法有添加、获取、删除单个元素以及**根据分值范围或成员，来获取元素**

- Hash散列

结构存储的值：包含键值对的无序散列表

结构的读写能力：包含方法有添加、获取、删除单个元素

<font color='red'>数据场景设计，114为什么？</font> 

### 1.2 基础数据结构详解

**重点在于：这个结构怎么用，结构能用来做什么？**围绕图例、执行和场景来描述

#### 1.2.1 String

String是redis种最基本的数据类型，一个key对应一个value

redis的String可以包含任何数据，如数字、字符串、jpg图片或者序列化的对象

- **图例**：下图是一个String类型的实例，其中key为hello，值为world

<img src="https://pic1.zhimg.com/80/v2-4bb6237bead5da2e7e0bfe272cb05dcc_1440w.webp" alt="img" style="zoom:50%;" />

- **命令使用**

|  命令   |          简述          |       使用        |
| :-----: | :--------------------: | :---------------: |
|   GET   | 获取存储在给定键中的值 |     GET name      |
|   SET   | 设置存储在给定键中的值 |  SET name VALUE   |
|   DEL   | 删除存储在给定键中的值 |     DEL name      |
|  INCR   |       将键的值+1       |     INCR key      |
|  DECR   |       将键的值-1       |     DECR key      |
| INCRBY  |  将键存储的值加上整数  | INCRBY key amount |
| DECRYBY |  将键存储的值减去整数  | DECRBY key amount |

- **实战场景**

1. 缓存：经典使用场景，把常用信息，字符串，图片或者视频等信息放到redis中，redis作为缓存层，mysql做持久化层，降低mysql读写压力；<font color='red'>将内存中的数据保存到关系型数据库的过程成为持久化</font> 

<font color='red'>为什么不用redis做主数据库（持久化层），而只是用redis做缓存？</font> 

https://www.zhihu.com/question/384184784

redis本身就是内存型数据库，但缺点也很明显：

1）受限于内存大小；

2）不能支持关系型数据库等要求；

2. 计数器：redis是单进程单线程模型，一个命令执行完才会执行下一个，同时数据可以一步落地到其他的数据源；
3. session：常见方案spring session + redis实现session共享<font color='red'>（现在还不理解）</font> 

#### 1.2.2 List列表

redis的list实际上就就是链表（redis用双端队列实现list）

使用list结构，我们可以轻松实现最新消息排队功能（比如新浪微博的timeline）。

list的另一个应用就是消息队列，可以用list的push操作，将任务存放在list中，然后工作线程再用pop操作将任务取出进行执行

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221122112259957.png" alt="image-20221122112259957" style="zoom:50%;" />

- 命令使用

|  命令  |                             简述                             |           使用            |
| :----: | :----------------------------------------------------------: | :-----------------------: |
| RPUSH  |                    将给定值推入到列表右端                    |      RPUSH key value      |
| LPUSH  |                    将给定值推入到列表左端                    |      LPUSH key value      |
|  RPOP  |           从列表的右端弹出一个值，并弹出被返回的值           |         RPOP key          |
|  LPOP  |           从列表的左端弹出一个值，并返回被弹出的值           |         LPOP key          |
| LRANGE |                 获取列表在给定范围上的所有值                 | LTRIM KEY_NAME START STOP |
| LINDEX | 通过索引获取列表中的元素，可以用负数下标，-1表示最后一个，-2表示倒数第二个 |     LINDEX key index      |
| LTRIM  | 对一个列表进行修剪(trim)，只保留指定区间内的元素，不在指定区间之内的元素都将删除 | LTRIM KEY_NAME START STOP |

<font color='red'>在智能114系统中，使用的是这种List列表的方法，每次通过LPUSH把一个dict给放进去，然后每次用LINDEX + 0的方式把这个给取出来，r.delete(requestParameters['callId'])，写上新的再给放回去</font> 

<font color='red'>反思来说，实际上使用那个简单的String就可以了，这里用list的话实际上也只用到了0，而且没有发挥双向链表这个left right的特点</font> 

- 使用列表的技巧

可以作为以下几种方案使用

1. lpush+lpop=Stack（栈）
2. lpush+rpop=Queue（队列）
3. lpush+ltrim=Capped Collection（有限集合）
4. lpush+brpop=Message Queue（消息队列，）

- 命令执行

```shell
127.0.0.1:6379> lpush mylist 1 2 ll ls mem
(integer) 5
127.0.0.1:6379> lrange mylist 0 -1
1) "mem"
2) "ls"
3) "ll"
4) "2"
5) "1"
127.0.0.1:6379> lindex mylist -1
"1"
127.0.0.1:6379> lindex mylist 10        # index不在 mylist 的区间范围内
(nil)
```

- 实战场景微博

1. timeline：有人发布微博，用lpush加入时间轴，展示新的列表信息
2. 消息队列

#### 1.2.3 Set集合

redis的Set是String类型的无序集合，集合成员是唯一的，这就意味着集合中不能出现重复的数据

redis种集合是通过哈希表实现的，所以添加、删除、查找的复杂度都是O1

- 图例

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221122143836992.png" alt="image-20221122143836992" style="zoom:50%;" />

- 命令使用

|   命令    |               简述                |         使用         |
| :-------: | :-------------------------------: | :------------------: |
|   SADD    |     向集合添加一个或多个成员      |    SADD key value    |
|   SCARD   |         获取集合的成员数          |      SCARD key       |
|  SMEMBER  |       返回集合中的所有成员        |  SMEMBER key member  |
| SISMEMBER | 判断member元素是否是集合key的成员 | SISMEMBER key member |

- 命令执行

```shell
127.0.0.1:6379> sadd myset hao hao1 xiaohao hao
(integer) 3
127.0.0.1:6379> smember myset
1) "xiaohao"
2) "hao1"
3) "hao"
127.0.0.1:6379> sismember myset hao
(integer) 1
```

- 实战场景

1. 标签（tag），给用户添加标签，或者用户给消息添加标签，这样有同一标签或者类似标签的可以给推荐关注的事或关注的人；
2. 点赞，或点踩，收藏等，可以放到set中实现；

<font color='red'>这里就是一个重复的判重，包含了判重，然后这些set之间可能也可以取交集并集这样的</font> 

#### 1.2.4 Zset有序集合

redis有序集合和集合一样，也是string类型元素的集合，且不允许重复的成员。

**不同的是每个元素都会关联一个double类型的分数，redis正是通过分数来为集合中的成员进行从小到大的排序**

有序集合的成员是唯一的，但分数（score）却可以重复。集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221122151156018.png" alt="image-20221122151156018" style="zoom:50%;" />

- 命令使用

```shell
127.0.0.1:6379> zadd myscoreset 100 hao 90 xiaohao
(integer) 2
127.0.0.1:6379> ZRANGE myscoreset 0 -1
1) "xiaohao"
2) "hao"
127.0.0.1:6379> ZSCORE myscoreset hao
"100"
```

- 实战场景

1. 排行榜：有序集合经典使用场景。例如小说视频等网站需要对用户上传的小说视频做排行榜，榜单可以按照用户的关注数、更新时间，字数等进行打分，做排行

<font color='red'>这里就是说，看起来一定是要一个[数值]的分数？</font> 

#### 1.2.5 Hash散列

redis hash是一个string类型的field（字段）和value（值）的映射表，hash特别适合用于存储对象

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221122152338290.png" alt="image-20221122152338290" style="zoom:50%;" />

- 命令使用

|  命令   |                   简述                   |             使用              |
| :-----: | :--------------------------------------: | :---------------------------: |
|  HSET   |                添加键值对                | HSET hash-key sub-key1 value1 |
|  HGET   |            获取指定散列键的值            |      HGET hash-key key1       |
| HGETALL |        获取散列种包含的所有键值对        |       HGETALL hash-key        |
|  HDEL   | 如果给定键存在于散列种，那么就移除这个键 |    HDEL hash-key sub-key1     |

- 命令执行

```shell
127.0.0.1:6379> hset user name1 hao
(integer) 1
127.0.0.1:6379> hset user email1 hao@163.com
(integer) 1
127.0.0.1:6379> hgetall user
1) "name1"
2) "hao"
3) "email1"
4) "hao@163.com"
127.0.0.1:6379> hget user user
(nil)
127.0.0.1:6379> hget user name1
"hao"
127.0.0.1:6379> hset user name2 xiaohao
(integer) 1
127.0.0.1:6379> hset user email2 xiaohao@163.com
(integer) 1
127.0.0.1:6379> hgetall user
1) "name1"
2) "hao"
3) "email1"
4) "hao@163.com"
5) "name2"
6) "xiaohao"
7) "email2"
8) "xiaohao@163.com"
```

- 实战场景

1. 缓存：能直观，相比string更节省空间的维护缓存信息，如用`<typo id="typo-2936" data-origin="能" ignoretag="true">户</typo>信息，视频信息`等

<font color='red'>如果114系统重构，将可能会使用这个来替代现有的list，可能是更加符合设计逻辑的</font>

<font color='red'>整体来说，如果重构114那个系统，那么每次读取信息也可以用list，来读-1，最后来把这个redis当成消息队列，把日志写到mysql数据库里面去</font> 

## 2. “5种基础”数据类型背后的实现原理（底层数据结构）

reference: https://xiaolincoding.com/redis/data_struct/data_struct.html#%E9%94%AE%E5%80%BC%E5%AF%B9%E6%95%B0%E6%8D%AE%E5%BA%93%E6%98%AF%E6%80%8E%E4%B9%88%E5%AE%9E%E7%8E%B0%E7%9A%84

redis为什么那么快，除了他是内存型数据库，使得所有操作都在内存上进行之外，还有一个重要因素，**它实现的数据结构，使得我们对数据进行增删改查操作的时候，redis能高效的处理**

redis数据结构并不是指String、List、Set、ZSet、Hash这些对象，因为这些是redis键值对中值的数据类型，也就是数据的保存形式，这些对象的底层实现方式就用到了数据结构

**String：底层为SDS**

**List：底层为双向链表和压缩列表；新版底层为quicklist**

**Set：底层为哈希表和整数集合**

**Zset：底层为压缩列表和跳表**

**hash：底层为压缩列表和哈希表；新版底层为listpack**



![image-20221124102453627](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124102453627.png)

从上表可以看出来，一共有9种数据结构：

**<font color='red'>SDS（简单动态字符串），双向链表，压缩列表，哈希表，跳表，整数集合，quicklist，listpack</font>** 

### 2.1 key-value键值对数据库是怎么实现的

再开始讲数据结构之前，先介绍下redis是怎么样实现键值对（key-value）数据库的

redis的键值对中的key就是字符串对象，而value可以是字符串对象，也可以是集合数据类型的对象，比如List Hash Set Zset Hash对象

博客举例，列出几种redis新增键值对的命令：

```shell
# name是一个字符串键，因为键的值是一个字符串对象
> SET name "xiaolincoding"
OK

# person是一个哈希表的键，因为键的值是一个包含两个键值对的哈希表对象
> HSET person name "xiaolincoding" age 18
0

# stu是一个列表键，因为键的值是一个包含两个元素的列表对象
> RPUSH stu "xiaolin" "xiaomei"
(integer) 4
```

**这些键值对是如何保存在redis中的呢？**

Redis是使用了一个「哈希表」保存所有键值对，哈希表最大的好处就是可以用O(1)的时间复杂度来快速查找到键值对，哈希表其实就是一个数组，数组中的元素叫做哈希桶。

哈希桶存放的是指向键值对数据类型的指针（dictEntry*），通过指针就能找到键值对数据。然后因为键值对的值是可以保存String/List/Set/Zset/hash数据类型的对象，所以键值对的数据结构并不 直接保存值本身，而是保存了`void *key`和`void *value`指针，分别指向了实际的键对象和值对象，这样一来，即使值是集合数据，也可以通过`void * value`指针找到；

![image-20221124104517943](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124104517943.png)

详解一下博客给出的这个图，内部的String这些对象的内部数据结构先不展开来说

- redisDb结构，表示Redis数据库的结构，结构体里存放了指向dict结构的指针；
- dict结构，结构体里存放了两个哈希表（dictht ht[2]），正常情况都用「哈希表1」，「哈希表2」只有在rehash的时候采用，具体什么是rehash，在本文的哈希表数据结构会讲；
- dictht结构，表示哈希表的结构，结构体里存放了哈希表数组，数组中的每个元素都是指向一个哈希表节点结构（dictEntry）的指针；
- dictEntry结构，表示哈希表节点的结构，结构体里存放了key和value指针，key指向的是String对象，而value则可以指向String对象，也可以指向集合类型的独享

<font color='red'>这个流程内部该怎么理解一手，我多个dictEntry是咋搞的，那不还是O(n)的</font> 

特别说明，key和value指针指向的是redis对象，redis中的每个对象都由redisObject结构表示，如下图：

对象结构里包含的成员变量：

- type：标识该对象是什么类型的对象（string list set zset hash）
- encoding：表示该对象使用了哪种底层数据结构
- ptr：指向底层数据结构的指针

![image-20221124105739899](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124105739899.png)

![image-20221124105940813](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124105940813.png)

### 2.2 SDS（simple dynamic string）

字符串在redis中是很常用的，键值对中的键是字符串类型，值有时候也是字符串类型

redis是用C语言实现的， 但是没有直接用C语言的char*字符串数组来实现字符串，**而是自己封装了一个名为简单动态字符串（SDS）的数据结构来表示字符串**，也就是redis的String数据类型的底层数据结构时SDS。

既然redis设计了SDS结构来表示字符串，肯定是C语言的char*字符数组存在一些缺陷，这里就来看看char *字符数组的结构

#### 2.2.1 C语言字符串的缺陷

C语言的字符串其实就是一个字符数组，即数组中每个元素是字符串中的一个字符。比如，下图就是字符串"xiaolin"的char *字符数组的结构：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124140415424.png" alt="image-20221124140415424" style="zoom:50%;" />

字符数组的结尾位置用\0表示，意思是指字符串的结束。因此，C语言标准库中的字符串操作函数就通过判断字符是不是\0来决定要不要停止操作，如果当前字符串不是\0，说明字符串可以继续操作，如果是\0就代表要停止操作；

C语言函数的`strlen()`，就是统计字符数组中的每一个字符，并进行计数，遇到\0后就会停止遍历，然后返回已经统计到的字符个数，即为字符串长度。因此很明显，这个统计的过程时间复杂度是O(n)的**<font color='red'>（这是一个可以改进的地方）</font>** 

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124141040548.png" alt="image-20221124141040548" style="zoom:50%;" />

此外，使用\0标记字符结尾也是有一定问题的，假设字符串中间有个\0字符，这时在操作这个字符串就会提早结束，如下图所示，计算字符串的长度会是4：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124141153203.png" alt="image-20221124141153203" style="zoom:50%;" />

因此，除了字符串的末尾之外，字符串里面不能有\0字符，否则最先被读入程序的\0字符将被误认为是字符串结尾，这个限制使得C语言的字符串只能保存文本数据，不能保存图像、音频、视频文件的二进制数据。**<font color='red'>（这是另一个可以改进的地方）</font>** 

另外，C语言标准库中字符串的操作函数是很不安全的，对程序员很不友好，稍微一不注意，就会导致缓存区溢出。举例子来说，strcat函数是可以将两个字符串拼接在一起：

```c
//将 src 字符串拼接到 dest 字符串后面
char *strcat(char *dest, const char* src);
```

C语言的字符串是不会记录自身缓冲区大小的，所以`strcat`函数假定程序员在执行这个函数的时候，已经为dest分配了足够多的内存，可以容纳src字符串中所有的内容，一旦假设不成立，就可能会发生缓冲区溢出，造成程序运行终止。**<font color='red'>这是一个可以改进的地方</font>** 

此外，`strcat`函数和`strlen`函数类似，时间复杂度也很高，要先遍历dest字符串到末尾，然后再遍历src字符串追加，对字符串操作效率不高。

> **<font color='red'>总结来说，C语言的字符串的缺陷主要概括为这几点：</font>** 
>
> - 使用`strlen`函数获取字符串长度的时间复杂度为O(N)
> - 字符串的结尾是以\0标识，字符串不能包含有\0字符因此不能保存二进制数据（音视频等）；
> - 字符串操作函数不搞笑且不安全，有缓存区溢出的风险，有可能会造成程序运行终止；

redis实现的SDS结构，就把上面这些问题解决了！

#### 2.2.2 SDS结构设计

Redis5.0 SDS的数据结构如下所示：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124143105321.png" alt="image-20221124143105321" style="zoom:50%;" />

具体介绍如下：

- **len，记录了字符串长度**：这样获取字符串长度的时候，只需要返回这个成员变量值就行，时间复杂度O1；
- **alloc，分配给字符数组的空间长度：**这样在修改字符串的时候，可以通过`alloc - len`计算出剩余的空间大小，可以用来判断空间是否满足修改需求，如果不满足的话，就会自动将SDS的空间扩展至执行修改所需的大小，然后才执行实际的修改操作，所以使用SDS既不需要手动修改SDS的空间大小，也不用担心缓冲区溢出的问题；
- **flags，用来表示不同类型的SDS：**一共设计了5种类型，分别是sdshdr5，sdshdr8，sdshdr16，sdshdr32和sdshdr64，后面再进行区别；
- **buf[]，字符数组，用来保存实际数据：**不仅可以保存字符串，也可以保存二进制数据

##### 2.2.2.1 O(1)时间复杂度获取字符串长度

C语言的strlen函数，通过遍历的方式计算字符串长度，时间复杂度是O(N)

而Redis的SDS结构因为加入了len成员变量，获取字符串长度的时候，直接返回这个成员变量的值就行，所以复杂度只有O(1)；

##### 2.2.2.2 二进制安全

SDS不需要用\0字符来标识字符串结尾了，而是由个专门的len长度来记录长度，所以可以存储包含\0的数据（但是SDS为了兼容部分C语言标准库的函数，SDS字符串结尾还是会加上\0字符）；

因此，SDS的API都是以处理二进制的方式来处理SDS存放在buf[]里的数据，程序不会对其中的数据做任何限制，数据写入的时候是什么样的，被读取就是什么样的；

这种二进制安全，使得redis的字符串不仅可以保存文本数据，也可以保存任意格式的二进制数据；

##### 2.2.2.3 不会发生缓冲区溢出

C语言的字符串标准库提供的字符串操作函数，大多数比如strcat都是不安全的，因为这些函数把缓冲区大小是否满足操作需求的工作交给卡发着来完成，程序内部并不会判断缓冲区大小是否足够用，缓冲区溢出了可能就直接程序退出了；

所以，redis的SDS结构中引入了alloc和len成员变量，这样SDS API通过计算`alloc - len`就可以算出剩余空间大小，对字符串进行修改的时候就可以判断缓冲区是否足够用；

**而且，当判断缓冲区大小不可用的时候，redis会自动将扩大SDS的空间大小，以满足所需修改的大小；**所以，使用SDS既不需要手动修改SDS的空间大小，也不会出现缓冲区移除的问题；

##### 2.2.2.4 节省内存空间

SDS结构中有个flags成员变量，表示的是SDS类型。

redis一共设计了5种类型，分别是sdshdr5、sdshdr8、sdshdr16、sdshdr32和sdshdr64

这5种类型的主要区别在于，他们数据结构中的len和alloc成员变量的数据类型不同，比如sdshdr16和sdshdr32：

```C
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len;
    uint16_t alloc; 
    unsigned char flags; 
    char buf[];
};


struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len;
    uint32_t alloc; 
    unsigned char flags;
    char buf[];
};
```

- sdshdr16类型的len和alloc类型都是uint16_t，表示字符数组长度和分配空间大小不能超过2的16次方；
- sdshdr32类型的len和alloc类型都是uint32_t，表示字符数组长度和分配空间大小不能超过2的32次方；

<font color='blue'>SDS设计不同类型的结构体，是为了能灵活保存不同大小的字符串，从而有效节省内存空间。</font> 比如，在保存小字符串的时候，结构头占用的空间也比较少



redis在编程上还使用了专门的编译优化来节省内存空间，在struct声明了`__attribute__((packed))`，它的作用是：<font color='blue'>告诉编译器取消结构体在编译过程中的优化对其，按照实际占用字节数进行对齐</font> 

比如，sdshdr16类型的SDS，默认情况下，编译器会按照2字节对齐的方式给变量分配内存<font color='red'>(1byte字节=8bit位)</font> ，这意味着，即使一个变量的大小不到2个字节，变化一起也会给它分配2个字节

- 举个例子，假设下面这个结构体，有两个成员变量char和int：

```C
#include <stdio.h>

struct test1 {
    char a;
    int b;
 } test1;
 
int main() {
     printf("%lu\n", sizeof(test1));
     return 0;
}
```

这个结构体的大小默认是8，这是因为默认情况下，编译器是使用「字节对齐」的方式分配内存，虽然char类型只占一个字节，但是由于成员变量里面有int类型，它占用4个字节（2^32，2147483648），所以在成员变量为char类型分配内存的时候，会分配4个字节，其中多于3个为对其而分配的，也就被浪费掉了；

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124150610769.png" alt="image-20221124150610769" style="zoom:50%;" />

如果不想编译器使用字节对齐的方式进行分配内存，可以采用上面那个`__attribute__((packed))`属性定义结构体，这样一来，结构体实际占用了多少内存空间，编译器就分配多少内存空间

```C
#include <stdio.h>

struct __attribute__((packed)) test2  {
    char a;
    int b;
 } test2;
 
int main() {
     printf("%lu\n", sizeof(test2));
     return 0;
}
```

这时打印的结果是 5（1 个字节 char + 4 字节 int）。

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124150930524.png" alt="image-20221124150930524" style="zoom:50%;" />

可以看得出，这是按照实际占用字节数进行分配内存的，这样可以节省内存空间。

### 2.3 链表（双向链表）

链表也是和数组一样，是一种非常常用的数据结构

redis的List对象，底层实现之一就是链表，C语言是本身没有链表这个结构的，所以redis自己设计了一个链表数据结构

#### 2.3.1 链表节点设计

先来看看「链表节点」结构的样子，prev next val，是经典的双向链表节点结构

```C
typedef struct listNode {
    //前置节点
    struct listNode *prev;
    //后置节点
    struct listNode *next;
    //节点的值
    void *value;
} listNode;
```

![image-20221124203958165](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124203958165.png)

#### 2.3.2 链表结构设计

redis在listNode的基础上，又封装了list这个数据结构，这样操作起来更方便，链表结构如下：

```C
typedef struct list {
    //链表头节点
    listNode *head;
    //链表尾节点
    listNode *tail;
    //节点值复制函数
    void *(*dup)(void *ptr);
    //节点值释放函数
    void (*free)(void *ptr);
    //节点值比较函数
    int (*match)(void *ptr, void *key);
    //链表节点数量
    unsigned long len;
} list;
```

list结构为链表提供了链表头指针head，链表尾节点tail，链表节点数量len，以及可以自定义实现的dup free match函数，下面是一个例子，由list结构和3个listNode组成的链表

![image-20221124204605463](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124204605463.png)

#### 2.3.3 链表的优势与缺陷

**redis的链表实现优点如下：**

- listNode链表节点带有prev和next指针，获取某个节点的前置节点或后置节点的时间复杂度只需要O(1)，而且这两个指针都可以指向null，所以链表是无环链表；
- list结构因为提供了表头指针head和表尾节点tail，所以获得链表的表头节点和表尾节点的时间复杂度只需要O1<font color='red'>（这个是lpush rpush lpop rpop）</font> 

- list结构因为提供了链表节点数量len，所以获取链表中**节点数量**的时间复杂度只需要O(1)；
- listNode链表使用void*指针保存节点值，并且可以通过list结构的dup，free，match函数指针为节点设置该节点类型的函数，因此链表节点可以保存不同类型的值；

<font color='purple'>那么实际上列表可以用lindex来获取，这个是如何实现的？通过链表的话，可以实现lindex吗？</font> 

**链表的缺陷也是有的：**

- 链表的每个节点之间的内存都是不连续的，意味着无法很好的利用CPU缓存。能很好利用CPU缓存的数据结构是数组，因为数组的内存是连续的，这样就可以充分利用CPU缓存来加速访问；
- 保存一个链表节点的值都需要一个链表节点结构头的分配，内存开销较大



上述问题带来，redis3.0的list对象**在数据量比较少的情况下**，会采用「压缩列表」作为底层数据结构的实现，它的优势是节省内存空间，并且内存紧凑型的数据结构<font color='red'>（内存紧凑带来的好处？）</font> 

然而压缩列表存在性能问题，所以redis3.2设计了新的我数据结构quicklist，并将list对象的底层数据结构改由quicklist实现

然后redis5.0设计了新的数据结构liskpack，沿用了压缩列表紧凑型的内存布局，在最新的redis版本，将hash对象和zset对象的实现之一的压缩列表，替换由listpack实现；



**补充知识：数组比链表更加可以利用CPU的缓存**

```shell
CPU 寄存器 – immediate access (0-1个CPU时钟周期)
CPU L1 缓存 – fast access (3个CPU时钟周期)
CPU L2 缓存 – slightly slower access (10个CPU时钟周期)
内存 (RAM) – slow access (100个CPU时钟周期)
硬盘 (file system) – very slow (10,000,000个CPU时钟周期)
```

CPU缓存会把一片连续的内存空间读入，因为数组的数据结构是连续的内存地址，所以数组全部或者部分元素被连续存在CPU缓存里面，这样读取每个元素只需要3个CPU时钟周期；

而链表节点是分散在堆空间里面的，这时候CPU缓存帮不上忙，只能去读取内存，平均读取需要100个时钟周期；



### 2.4 压缩列表

压缩列表的特点，就是它被设计成一种内存紧凑型的数据结构，占用一块连续的内存空间，不仅可以利用CPU缓存，而且针对不同长度的数据进行相应的编码，这种方法可以有效的解锁内存开销

缺陷在于：

- 不能保存过多的元素，否则查询效率就会降低；
- 新增或者修改某个元素的时候，压缩列表的内存空间需要重新分配，甚至可能引发连锁更新的问题

因此，redis对象（List Zset hash）包含的元素数量较少，或者元素值不大的时候，才会使用压缩列表作为底层的数据结构

#### 2.4.1 压缩列表结构的设计

压缩列表是redis为了节约内存而开发的，它是<font color='blue'>由连续内存块组成的顺序型数据结构</font> ，有点类似于数组

![image-20221124212310177](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124212310177.png)

压缩列表（ziplist，zl）在表头由三个字段组成：

- zlbytes：记录整个压缩列表占用对内存字节数

- zltail：记录压缩列表「尾部」节点距离其实地址有多少字节，也就是列表尾部的偏移量
- zllen：记录压缩列表包含的节点数量；
- zlend：标记压缩列表的结束点，固定值0xFF（十进制255）

在压缩列表中，如果查找定位第一个和最后一个元素，可以通过表头这些字段的长度直接定位，复杂度是O1。<font color='blue'>但是查找其他元素时，就没有这么高效了，只能逐个查找，此时的复杂度就是O(N)了，不适合保存过多的元素</font> 

#### 2.4.2 压缩列表节点（entry）的结构

压缩列表节点（entry）的构成如下：

![image-20221124213108222](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221124213108222.png)

压缩列表节点包含三部分内容：

- prevlen：记录了「前一个节点」的长度，目的是为了实现从后向前遍历；
- encoding：记录了当前节点实际数据的「类型和长度」，类型主要是字符串和整数
- data：记录了当前节点的实际数据，类型和长度都由encoding决定







## 3. “3种特殊”数据类型

redis除了上文提到的5种基础数据类型（String，List，Set，Zset，Hash）以外，还有3种特殊的数据类型，分别是HyperLogLogs（基数统计），Bitmaps（位图）和geospatial（地理位置）

### 3.1 HyperLogLogs（基数统计）

Redis2.8.9更新了这个数据结构

- **什么是基数？**

举个例子，A={1,2,3,4,5}，B={3,5,6,7,9}；那么基数（不重复的元素）=1,2,4,6,7,9

**允许容错，即可以接受一定的误差**

- **hyperloglogs基数统计用来解决什么问题？**

这个结构可以**非常省内存的去统计各种计数**，比如注册IP数、每日访问ip数的页面实时uv（unique visitor，实时访客）、在线用户数、共同好友数等。

- **它的优势体现在哪？**

一个大型的网站，每天IP比如有100万，粗算一个IP消耗15字节，那么100万个ip就是15M。

而HyperLogLog在redis中每个键占用的内容都是12k，理论存储接近2^64个值，不管存储的内容是什么，它一个基于基数估算的算法，只能比较准确的估算出基数，可以使用少量固定的内存去存储并识别集合中的唯一的元素。

这个估算的基数不一定准确，是一个带有0.81%标准错误的近似值，对于可以接受一定容错的业务场景，比如IP数统计，UV等，是可以忽略不计的

- 相关命令的使用

```shell
127.0.0.1:6379> pfadd key1 a b c d e f g h i    # 创建第一组元素
(integer) 1
127.0.0.1:6379> pfcount key1                    # 统计元素的基数数量
(integer) 9
127.0.0.1:6379> pfadd key2 c j k l m e g a      # 创建第二组元素
(integer) 1
127.0.0.1:6379> pfcount key2
(integer) 8
127.0.0.1:6379> pfmerge key3 key1 key2          # 合并两组：key1 key2 -> key3 并集
OK
127.0.0.1:6379> pfcount key3
(integer) 13
```

### 3.2 Bitmap（位存储）

bitmap即位图数据结构，都是操作二进制位来进行记录，只有0和1两个状态

- 用来解决什么问题？

比如：统计用户信息：活跃/不活跃，登录/未登录，打卡/未打卡，两个状态都可以使用bitmaps

如果存储一年的打卡状态需要多少内存？365天=365bit，1byte=8bit，那么就只要46个字节左右

- 相关命令的使用

使用bitmap来记录周一到周日的打卡！周一：1 周二：0 周三：0 周四：1 ......

```shell
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 1
(integer) 0
127.0.0.1:6379> setbit sign 2 0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 0
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> setbit sign 6 1
(integer) 0
```

```shell
# 查看某一天是否有打卡！
127.0.0.1:6379> getbit sign 3
(integer) 1
127.0.0.1:6379> getbit sign 5
(integer) 0
```

```shell
# 统计操作，统计 打卡的天数！
127.0.0.1:6379> bitcount sign # 统计这周的打卡记录，就可以看到是否有全勤！
(integer) 3
```

### 3.3 geospatial（地理位置）

redis的geo在3.2版本就推出了！这个功能可以推算地理位置的信息：两地之间的距离，方圆几里的人

#### 3.3.1 geoadd

添加地理位置

```shell
127.0.0.1:6379> geoadd china:city 118.76 32.04 manjing 112.55 37.86 taiyuan 123.43 41.80 shenyang
(integer) 3
127.0.0.1:6379> geoadd china:city 144.05 22.52 shengzhen 120.16 30.24 hangzhou 108.96 34.26 xian
(integer) 3
```

- 规则

可以下载城市数据：http://www.jsons.cn/lngcode

有效的经度从-180度到180度。

有效的纬度从-85.05112878度到85.05112878度。

超出这个范围的时候，会返回一个错误

#### 3.3.2 geopos

获取指定成员的精度和纬度，是一个坐标值

```shell
127.0.0.1:6379> geopos china:city taiyuan manjing
1) 1) "112.54999905824661255"
   1) "37.86000073876942196"
2) 1) "118.75999957323074341"
   1) "32.03999960287850968"
```

#### 3.3.3 geodist

单位分为：m米/km千米/mi英里/ft英尺

<font color='red'>这个是个很神奇的操作，就是说，根据两个经纬度获取距离，自己腰酸还麻烦</font> 

```shell
127.0.0.1:6379> geodist china:city taiyuan shenyang m
"1026439.1070"
127.0.0.1:6379> geodist china:city taiyuan shenyang km
"1026.4391"
```

#### 3.3.4 georadius

附近的人，获取附近的人的地址、定位，通过半径来查询

`参数 key 经度 纬度 半径 单位 [显示结果的经度和纬度] [显示结果的距离] [显示的结果的数量]`

```shell
127.0.0.1:6379> georadius china:city 110 30 1000 km         以 100,30 这个坐标为中心, 寻找半径为1000km的城市
1) "xian"
2) "hangzhou"
3) "manjing"
4) "taiyuan"
127.0.0.1:6379> georadius china:city 110 30 500 km
1) "xian"
127.0.0.1:6379> georadius china:city 110 30 500 km withdist
1) 1) "xian"
   2) "483.8340"
127.0.0.1:6379> georadius china:city 110 30 1000 km withcoord withdist count 2
1) 1) "xian"
   2) "483.8340"
   3) 1) "108.96000176668167114"
      2) "34.25999964418929977"
2) 1) "manjing"
   2) "864.9816"
   3) 1) "118.75999957323074341"
      2) "32.03999960287850968"
```

#### 3.3.5 georadiusbymember

显示与指定成员一定半径范围内的其他成员

`参数 key 经度 纬度 半径 单位 [显示结果的经度和纬度] [显示结果的距离] [显示的结果的数量]`

```shell
127.0.0.1:6379> georadiusbymember china:city taiyuan 1000 km
1) "manjing"
2) "taiyuan"
3) "xian"
127.0.0.1:6379> georadiusbymember china:city taiyuan 1000 km withcoord withdist count 2
1) 1) "taiyuan"
   2) "0.0000"
   3) 1) "112.54999905824661255"
      2) "37.86000073876942196"
2) 1) "xian"
   2) "514.2264"
   3) 1) "108.96000176668167114"
      2) "34.25999964418929977"
```

#### 3.3.6 geohash

该命令返回11个字符的hash字符串，将二维的经纬度转换为一维的字符串，如果两个字符越接近，则距离越近

```shell
127.0.0.1:6379> geohash china:city taiyuan shenyang
1) "ww8p3hhqmp0"
2) "wxrvb9qyxk0"
```

#### 3.3.7 geospatial的底层

底层是Zset（有序的set）

```shell
127.0.0.1:6379> type china:city
zset
```

```shell
127.0.0.1:6379> zrange china:city 0 -1 withscores
 1) "xian"
 2) "4040115445396757"
 3) "hangzhou"
 4) "4054133997236782"
 5) "manjing"
 6) "4066006694128997"
 7) "taiyuan"
 8) "4068216047500484"
 9) "shenyang"
1)  "4072519231994779"
2)  "shengzhen"
3)  "4154606886655324"
127.0.0.1:6379> zrem china:city manjing
(integer) 1
127.0.0.1:6379> zrange china:city 0 -1
1) "xian"
2) "hangzhou"
3) "taiyuan"
4) "shenyang"
5) "shengzhen"
```
