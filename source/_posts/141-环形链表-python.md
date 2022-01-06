---
title: 141.环形链表-python
date: 2022-01-05 23:16:03
tags:
    - 哈希表
    - 链表
    - 双指针 
    - EASY
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

141.环形链表（简单）

题目大意：

给你一个链表的头节点```head```，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪```next```指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数```pos```来表示链表尾连接到链表中的位置（索引从 ```0```开始）。如果```pos```是```-1```，则在该链表中没有环。注意：```pos```不作为参数进行传递，仅仅是为了标识链表的实际情况。

如果链表中存在环，则返回```true```。 否则，返回```false```。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/lru-cache/)

题目大意：

给你一个链表的头节点```head```，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪```next```指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数```pos```来表示链表尾连接到链表中的位置（索引从 ```0```开始）。如果```pos```是```-1```，则在该链表中没有环。注意：```pos```不作为参数进行传递，仅仅是为了标识链表的实际情况。

如果链表中存在环，则返回```true```。 否则，返回```false```。

示例1：
![](/images/2022-01-05-23-41-23.png)
```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

示例2：
![](/images/2022-01-05-23-41-35.png)
```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

示例3：
![](/images/2022-01-05-23-41-45.png)
```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

提示：
- 链表中节点的数目范围是 [0, 104]
- -105 <= Node.val <= 105
- pos 为 -1 或者链表中的一个 有效索引 。

进阶：你能用 O(1)（即，常量）内存解决此问题吗？

# 分析和解答

```python
# Definition for singly-linked list.
class ListNode(object):
    def __init__(self, x):
        self.val = x
        self.next = None
```

## 方法1，普通的字典法

这个node对象是可哈希化的，可以用来当做键，所以设置一个字典就知道了

```python
class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        save_dict = {}
        while head != None:
            if save_dict.get(head) is not None:
                return True
            save_dict[head] = 1
            head = head.next
        
        return False
```

## 方法2，巧妙的修改值法

妙！

```python
class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        while head != None:
            if head.val == "啊哈哈哈哈":
                return True
            else:
                head.val = "啊哈哈哈哈"
            head = head.next
        return False
```

## 方法3，快慢指针

一个慢的乌龟和一个快的兔子，兔子跑得快，如果有环那兔子是总能追上乌龟的

快慢指针需要注意不能条件的判断，要把例如head和head.next这种的一起来判断！

```python
class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """

        # 先要把异常判断做好了
        if head is None or head.next is None:
            return False

        slow_turtle = head
        fast_rabbit = head.next

        while slow_turtle != fast_rabbit:
            if fast_rabbit is None or fast_rabbit.next is None:
                return False
            else:
                slow_turtle = slow_turtle.next
                fast_rabbit = fast_rabbit.next.next
        
        return True

```