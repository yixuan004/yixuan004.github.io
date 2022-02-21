---
title: 160.相交链表-python
date: 2022-01-17 15:57:26
tags:
    - 
    - EASY
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

160.相交链表（简单）

题目大意：

给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 null 。

进阶：你能否设计一个时间复杂度 O(m + n) 、仅用 O(1) 内存的解决方案？

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

给你两个单链表的头节点```headA```和```headB```，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回```null```。

图示两个链表在节点```c1```开始相交：

![](/images/2022-01-17-16-20-00.png)

题目数据 保证 整个链式结构中不存在环。

注意，函数返回结果后，链表必须 保持其原始结构 。

自定义评测：

评测系统 的输入如下（你设计的程序 不适用 此输入）：

- ```intersectVal``` - 相交的起始节点的值。如果不存在相交节点，这一值为```0```
- ```listA``` - 第一个链表
- ```listB``` - 第二个链表
- ```skipA``` - 在```listA```中（从头节点开始）跳到交叉节点的节点数
- ```skipB``` - 在```listB```中（从头节点开始）跳到交叉节点的节点数

评测系统将根据这些输入创建链式数据结构，并将两个头节点 headA 和 headB 传递给你的程序。如果程序能够正确返回相交节点，那么你的解决方案将被 视作正确答案 。

示例1：
![](/images/2022-01-17-16-28-41.png)
```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
输出：Intersected at '8'
解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,6,1,8,4,5]。
在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

示例2：
![](/images/2022-01-17-16-28-58.png)
```
输入：intersectVal = 2, listA = [1,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Intersected at '2'
解释：相交节点的值为 2 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [1,9,1,2,4]，链表 B 为 [3,2,4]。
在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
```

示例3：
![](/images/2022-01-17-16-29-05.png)
```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。
由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
这两个链表不相交，因此返回 null 。
```


提示：
- ```listA```中节点数目为```m```
- ```listB```中节点数目为```n```
- ```1 <= m, n <= 3 * 104```
- ```1 <= Node.val <= 105```
- ```0 <= skipA <= m```
- ```0 <= skipB <= n```
- 如果```listA```和```listB```没有交点，```intersectVal```为```0```
- 如果```listA```和```listB```有交点，```intersectVal == listA[skipA] == listB[skipB]```


# 分析和解答

这个题在面试的时候曾经被问过，判断两个链表是否有交有很多种方法，如果只是判断是否有交的话，最简单的方法就是两个链表都分别走到结尾处，如果结尾处是一样的，那么就判断两个链表在之前一定有交了；

这个题要求判断并找到两个链表相交的位置，一种非常简单的思路是把每个地方都存到一个字典里，这样找一下就行了，但是题目的附加要求是O(1)的时间复杂度，所以说不能这么做；

下边这个图也是看了题解后才明白的，这样最多走a+b这个长度的次数，两个走的长度是一样的，如果有交集则必定能相交！

![](/images/2022-01-17-16-16-56.png)

```python
class Solution(object):
    def getIntersectionNode(self, headA, headB):
        """
        :type head1, head1: ListNode
        :rtype: ListNode
        """
        if headA is None or headB is None:
            return None

        # 循环结束的两种条件，一种是pq交到一个位置了，另外一种就是pq都是null，所以都是
        # 只走一遍就可以了，a+b的长度
        p = headA
        q = headB
        while p != q:
            if p is None:
                p = headB
            else:
                p = p.next
            if q is None:
                q = headA
            else:
                q = q.next

        return p
```


复习单链表的一个写法
```python
class ListNode(object):
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```