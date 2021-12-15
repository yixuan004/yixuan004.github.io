---
title: 146.LRU缓存机制-python
date: 2021-12-15 11:43:06
tags:
    - 设计
    - 哈希表
    - 链表
    - 双向链表
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

146.LRU缓存机制（中等）

题目大意：

运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制 。
实现 LRUCache 类：

- LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存
- int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
- void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

进阶：你是否可以在 O(1) 时间复杂度内完成这两种操作？

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/lru-cache/)

运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制 。
实现 LRUCache 类：

- LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存
- int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
- void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

进阶：你是否可以在 O(1) 时间复杂度内完成这两种操作？

示例：
```
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```

提示：
- 1 <= capacity <= 3000
- 0 <= key <= 10000
- 0 <= value <= 105
- 最多调用 2 * 105 次 get 和 put

# 解答与分析

这个题实在是太经典了，而且是自己面试过程中真正被问到而没有做出来的题目，首先第一步要根据题目所给出的内容自定义LRUCache类，也借此进一步熟悉语法。


## 自定义LRUCache类
```python
class LRUCache(object):
    def __init__(self, capacity):
        self.capacity = capacity
    
    def get(self, key):
        """
        rtype: int
        """
        pass
    
    def put(self, key, value):
        """
        rtype: None
        """
```


## 使用双向链表来完成题目要求的操作时间复杂度要求
题目中要求的O(1)时间复杂度下完成各个操作，主要靠的是cache词典和双向链表中的一些操作，缓存（cache）在这个题中十分关键

首先搞起来双向链表的基本模板

```python
class BiLinkedList(object):
    def __init__(self, key=-1, val=-1, prev=None, next=None):
        self.key = key
        self.val = val
        self.prev = prev
        self.next = next
```


## LRUCache __init__中需要初始化的内容
按照题目一步步的来分析，首先分析需要哪些内容放在LRUcatch的init中，首先是题目中给了的capacity代表这个LRU cache的最大容量，之后是一个为0的current_size代表当前LRU cache的容量，之后可以根据做一些特殊的判断。还有就是一个词典的cache，代表有哪些已经在LRUcache中了，这里最关键的是记住这个cache中存储的是key-node的mapping。还有就是初始化一个双向链表，注意这个双向链表的头和尾虽然直接初始化了，但是这两个节点并没有什么含义，所以head.next和tail.prev中间这些的才感觉是有用的。

```python
class LRUCache(object):
    def __init__(self, capacity):
        self.capacity = capacity
        self.current_size = 0
        self.cache = {}

        self.head = BiLinkedList()
        self.tail = BiLinkedList()
        self.head.next = self.tail
        self.tail.prev = self.head
```


## 分析LRUCache中的插入（put）操作和查找（get）操作

### 插入（put）

插入给了一个key和一个value。

首先判断key是不是已经在cache中了

- 如果已经在cache中了
  - 在cache中删除这个key对应的node，然后把这个节点的更新值头插法搞到链表的最前边去。**这里体现了双向链表的作用还有cache中key-node mapping的作用，，mapping找到一个node后，基于双向链表可完成O(1)的删除**

- 如果不在的话
  - 首先判断目前的self.current_size和self.capacity的关系，
    - 如果相等了，则删除目前双向链表尾部的，之后用头插法在前边插入上。
    - 如果self.current_size小于self.capacity，直接在头部插入后，self.capacity++就可以了


### 查找（put）

查找只给了一个key，返回这个key对应的value，如果找不到就返回-1

这个直接从cache中找就可以了，但是注意查找到的同样要删除，并且头插法到最前边来


## 双向链表操作函数的编写

综上所述，在该题中需要双向链表的三种操作：（注意这里只管链表的操作，是否能执行这些操作的判断在其他地方试）

①：头插法

②：尾部删除

③：中间删除


下边这些实现还要经常复习，主要是涉及顺序
```python
def add_to_head(self, node):
    """
    双向链表的头插法，current_size 和 cache的变化不在这里做，这里只管插入
    注意这里，是插入到虚拟head之后的一个
    """
    node.next = self.head.next
    node.prev = self.head
    self.head.next.prev = node
    self.head.next = node
    

def remove_tail(self):
    """
    双向链表的删除尾，current_size 和 cache的变化不在这里做，这里只管删除
    注意这里，是删除虚拟尾结点前边的一个！
    """
    temp = self.tail.prev
    self.tail.prev = self.tail.prev.prev
    self.tail.prev.next = self.tail
    temp.next = None
    temp.prev = None
    return temp


def remove_inter(self, node):
    """
    双向链表的中间节点删除
    """
    node.prev.next = node.next
    node.next.prev = node.prev
```

## 工具都造好了，最终整体上的代码实现如下，还要经常复习啊

```python
"""
使用双向链表的原因是因为双向链表的删除可以做到O(1)？也或许还有其他特性
！注意题目中的关键字key存在于缓存中，要拿到关键字的值，也就是一个key-value的关系，一定要注意这个
"""
class BiLinkedList(object):
    def __init__(self, key=-1, value=-1, prev=None, next=None):
        self.key = key
        self.value = value
        self.prev = prev
        self.prev = next


class LRUCache(object):

    def __init__(self, capacity):
        """
        :type capacity: int
        """
        self.capacity = capacity
        self.current_size = 0
        self.cache = {} # 这个mapping是key -> node！！
        # 创建一个初始的头和一个初始的尾
        self.head = BiLinkedList()
        self.tail = BiLinkedList()
        self.head.next = self.tail
        self.tail.prev = self.head

    def add_to_head(self, node):
        """
        双向链表的头插法，current_size 和 cache的变化不在这里做，这里只管插入
        注意这里，是插入到虚拟head之后的一个
        """
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node
        

    def remove_tail(self):
        """
        双向链表的删除尾，current_size 和 cache的变化不在这里做，这里只管删除
        注意这里，是删除虚拟尾结点前边的一个！
        """
        temp = self.tail.prev
        self.tail.prev = self.tail.prev.prev
        self.tail.prev.next = self.tail
        temp.next = None
        temp.prev = None
        return temp

    def remove_inter(self, node):
        """
        双向链表的中间节点删除
        """
        node.prev.next = node.next
        node.next.prev = node.prev


    def get(self, key):
        """
        :type key: int
        :rtype: int
        # 注意get的时候，要把这个地方挪到最前边，因为LRU访问的时候要挪到前边来
        """
        if self.cache.get(key) is None:
            return -1
        else:
            node = self.cache.get(key)
            self.remove_inter(node)
            self.add_to_head(node)
            return node.value


    def put(self, key, value):
        """
        :type key: int
        :type value: int
        :rtype: None
        """
        if self.cache.get(key) is None:
            """
            需要头插入，如果插入后长度超过了capacity，就要进行尾部删除
            cache需要变动
            """
            node = BiLinkedList(key=key, value=value)
            self.add_to_head(node)
            self.current_size += 1
            self.cache[key] = node
            if self.current_size > self.capacity:  # 超出容量了，要删除，同时cache也要删除
                temp = self.remove_tail()
                del self.cache[temp.key]
        else:
            """
            已经有了，拿到一个node，需要先删除，然后挪到最前边
            cache不需要变动
            """
            node = self.cache.get(key)
            self.remove_inter(node)
            node = BiLinkedList(key=key, value=value)  # 更新val值
            self.cache[key] = node
            self.add_to_head(node)



# Your LRUCache object will be instantiated and called as such:
# obj = LRUCache(capacity)
# param_1 = obj.get(key)
# obj.put(key,value)
```