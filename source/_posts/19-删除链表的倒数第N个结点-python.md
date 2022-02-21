---
title: 19.删除链表的倒数第N个结点-python
date: 2022-02-21 18:11:44
tags:
    - 链表
    - 双指针
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

19.删除链表的倒数第 N 个结点（中等）

题目大意：

给你一个链表，删除链表的倒数第 ```n``` 个结点，并且返回链表的头结点。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

题目大意：给你一个链表，删除链表的倒数第 ```n``` 个结点，并且返回链表的头结点。

示例1：
![](/images/2022-02-21-23-49-06.png)
```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

示例2：
```
输入：head = [1], n = 1
输出：[]
```

示例3：
```
输入：head = [1,2], n = 1
输出：[1]
```

提示：
- 链表中结点的数目为 ```sz```
- ```1 <= sz <= 30```
- ```0 <= Node.val <= 100```
- ```1 <= n <= sz```


# 分析和解答

这是一个经典的双指针题目了，保持pq指针之间的距离差是n，然后当p先到结尾后，把q.next那个地方删除了就可以。

这个题比较麻烦的地方在于很难一次性做对，需要自己构造一些相对极端的的case来测试下，例如只有一个节点的链表这样的。

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def removeNthFromEnd(self, head, n):
        """
        :type head: ListNode
        :type n: int
        :rtype: ListNode
        """
        # 注意自己构造一些badcase的例子，来把所有特殊情况覆盖掉
        # 双指针法，双指针之间的差距是n
        p = head
        q = head
        i = 0
        while i < n:
            q = q.next
            i += 1

        # 让q指向最后一个不为None的，有一个异常情况是q已经是None了，应该删除头结点
        if q is None:
            return head.next
        while q.next is not None:
            p = p.next
            q = q.next
        
        # 删除p
        p.next = p.next.next
        return head
```