---
title: redis基本数据类型：“5种基础”数据类型+“3种特殊”数据类型
date: 2022-11-21 23:43:50
tags:
    - redis
categories:
	- develop
---

该篇博客介绍redis的基本数据类型，以及基础类型的背后实现原理（底层）

“5种基础”数据类型：String、List、Set、Zset、Hash；

“3种特殊”数据类型：HyperLogLogs（基数统计），Bitmaps（位图）和geospatial（地理位置）；

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













