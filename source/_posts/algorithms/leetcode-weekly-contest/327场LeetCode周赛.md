---
title: 327场LeetCode周赛
date: 2023-01-08 16:12:42
tags:
	- EASY
	- MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

今天睡过了昏昏沉沉迟到了半小时，t4好像难度很高，把前三题给搞定了，没有花太多时间，感觉也没有遇到太大的坑点，最后的排名好像还能看

![image-20230108162531890](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108162531890.png)

第一题：直接遍历过去计数就行了；

第二题：贪心+堆，能想到贪心的话，这个题目的特点还是很像堆数据结构的特点的；

第三题：用Counter+模拟就可以搞定，关键是字母只有26位，这样时间复杂度是一个O(26^2)的；

第四题：咕咕了

<!--more-->

# 第一题：6283. 正整数和负整数的最大计数

https://leetcode.cn/contest/weekly-contest-327/problems/maximum-count-of-positive-integer-and-negative-integer/

## 题目大意

![image-20230108163508855](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108163508855.png)

## 分析和解答

直接遍历过去计数就行了；

```python
class Solution:
    def maximumCount(self, nums: List[int]) -> int:
        tmp1 = 0
        tmp2 = 0
        for i in range(len(nums)):
            if nums[i] > 0:
                tmp1 += 1
            elif nums[i] < 0:
                tmp2 += 1
        
        return max(tmp1, tmp2)
```


# 第二题：6285. 执行 K 次操作后的最大分数

https://leetcode.cn/contest/weekly-contest-327/problems/maximal-score-after-applying-k-operations/

## 题目大意

![image-20230108163446611](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108163446611.png)

## 分析和解答

贪心+堆，能想到贪心的话，这个题目的特点还是很像堆数据结构的特点的；

```python
class Solution:
    def maxKelements(self, nums: List[int], k: int) -> int:
        import heapq
        pq = []
        for i in range(len(nums)):
            heapq.heappush(pq, -nums[i])
            
        res = 0
        for i in range(k):
            tmp = heapq.heappop(pq)
            res += -tmp
            heapq.heappush(pq, tmp//3)
        
        return res
```


# 第三题：6284. 使字符串总不同字符的数目相等

https://leetcode.cn/contest/weekly-contest-327/problems/make-number-of-distinct-characters-equal/

## 题目大意

![image-20230108163426461](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230108163426461.png)

## 分析和解答

用Counter+模拟就可以搞定，关键是字母只有26位，这样时间复杂度是一个O(26^2)的；

```python
class Solution:
    def isItPossible(self, word1: str, word2: str) -> bool:
        from collections import Counter
        cnt1 = Counter(word1)
        cnt2 = Counter(word2)
        import copy
        cntt1, cntt2  = copy.deepcopy(cnt1), copy.deepcopy(cnt2)
        
        
        diff_cnt1 = len(cnt1.keys())
        diff_cnt2 = len(cnt2.keys())
        # print("diff_cnt1, diff_cnt2: ", diff_cnt1, diff_cnt2)
        
        
        
        for k1, v1 in cnt1.items():
            for k2, v2 in cnt2.items():
                org1, org2 = diff_cnt1, diff_cnt2
                
                # 这两个k交换一下，然后判断不同的数字是不是相等
                ## 删除
                cntt1[k1] -= 1
                cntt2[k2] -= 1
                if cntt1[k1] <= 0:
                    diff_cnt1 -= 1
                if cntt2[k2] <= 0:
                    diff_cnt2 -= 1
                    
                ## 添加
                if cntt1[k2] != 0:
                    cntt1[k2] += 1
                else:
                    diff_cnt1 += 1
                    cntt1[k2] = 1
                
                if cntt2[k1] != 0:
                    cntt2[k1] += 1
                else:
                    diff_cnt2 += 1
                    cntt2[k1] = 1
                
                if diff_cnt1 == diff_cnt2:
                    return True
                
                # 换回来
                diff_cnt1, diff_cnt2 = org1, org2
                cntt1[k2] -= 1
                cntt1[k1] += 1
                
                cntt2[k1] -= 1
                cntt2[k2] += 1
                    
        
        return False
```