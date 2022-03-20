---
title: 23.合并K个升序链表-python
date: 2022-03-14 19:36:09
tags:
    - 链表
    - 分治
    - 堆
    - 优先队列
    - 归并排序
    - HARD
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

23.合并K个升序链表（困难）

题目大意：

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

示例1：
```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

示例2：
```
输入：lists = []
输出：[]
```

示例3：
```
输入：lists = [[]]
输出：[]
```

提示：
- ```k == lists.length```
- ```0 <= k <= 10^4```
- ```0 <= lists[i].length <= 500```
- ```10^4 <= lists[i][j] <= 10^4```
- ```lists[i]``` 按 **升序** 排列
- ```lists[i].length 的总和不超过 10^4```

# 分析和解答

这个题感觉实际真的明白了什么是优先队列，什么是堆的思想。python这个 ```heapq``` 是真的神，如果用正数就是小根堆，如果是用负数就可以转化为大根堆了！

只要先用 ```heapq``` 存储成一个堆，然后不断 ```heappop()```就可以了，这里推荐堆的命名可以直接用 ```heap```；

另注，这个题实际上有很多做法，但这个做法是最为简单的了，其他还需要进一步探究；

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def mergeKLists(self, lists):
        """
        :type lists: List[ListNode]
        :rtype: ListNode
        """
        import heapq  # 优先队列
        heap = []
        for i, t_list in enumerate(lists):
            head = t_list
            while head is not None:
                heapq.heappush(heap, head.val)
                head = head.next
        # 尾插法
        return_head = ListNode(val=0, next=None)
        p = return_head
        while len(heap) != 0:
            tmp = heapq.heappop(heap)
            p.next = ListNode(val=tmp)
            p = p.next

        return return_head.next
```
