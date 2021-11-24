---
title: Leetcode热门一百题
date: 2021-11-21 20:20:31
tags:
---

python语言，leetcode热门100题

<!--more-->

# 1. 两数之和

解法一：
```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        idx = [i for i in range(len(nums))]
        idx.sort(key = lambda x: nums[x]) # 

        nums.sort()
        s, e  = (0, len(nums) - 1)
        while s != e:
            add_result = nums[s] + nums[e]
            if add_result == target:
                return [idx[s], idx[e]]
            elif add_result > target:
                e -= 1
            elif add_result < target:
                s += 1
        
        return [-1, -1]
```

解法二：
```
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        # 通过num索引到角标，这个索引的key-value很关键
        mapping = {}
        for i, num in enumerate(nums):
            mapping[num] = i

        # 找，通过hashmap的操作避免了排序等
        for i, num in enumerate(nums):
            temp_idx = mapping.get(target - num)
            if temp_idx is not None and temp_idx != i: # 使用get可以避免空值问题
                return [i, temp_idx]

        # 兜底
        return [-1, -1]
```

# 2. 


```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution(object):
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        head = ListNode(-1, None) # 创建一个初始的
        p = head

        jinwei = 0 # 初始化进位为0

        while (l1 is not None) and (l2 is not None): # 只要有一个为none就退出，之后全往后拼就可以了
            p.next = ListNode((l1.val + l2.val + jinwei) % 10)
            jinwei = (l1.val + l2.val + jinwei) // 10 # 留进位
            p = p.next
            l1 = l1.next
            l2 = l2.next

        # 经过上边的处理后，l1 l2必定有一个是none的
        while l1 is not None:
            p.next = ListNode((l1.val + jinwei) % 10)
            jinwei = (l1.val + jinwei) // 10
            p = p.next
            l1 = l1.next

        while l2 is not None:
            p.next = ListNode((l2.val + jinwei) % 10)
            jinwei = (l2.val + jinwei) // 10
            p = p.next
            l2 = l2.next

        if jinwei != 0:
            p.next = ListNode(jinwei)

        # 打印输出查看
        # while head is not None:
        #     print(head.val)
        #     head = head.next
        
        return head.next
```