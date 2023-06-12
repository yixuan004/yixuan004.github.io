---
title: 106场LeetCode双周赛
date: 2023-06-11 21:01:52
tags:
	- EASY
	- MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

https://leetcode.cn/contest/biweekly-contest-106/

再次保分大师is me（吃wa吃的有点多555），整体还是体会比较好的一次周赛，移动机器人这个t3，LR往左往右移动的，其实是一个经典的问题，因为可以看做两只蚂蚁（机器人）完成了身份替换，而不是碰撞后往两边走了，那么就是直接记录最后状态，然后积累一个板子，就是数轴上所有点，求所有两两点之间的距离和，可以使用前缀和的思路在On的时间复杂度内解决问题。板子原题：https://leetcode.cn/problems/sum-of-absolute-differences-in-a-sorted-array/

![image-20230612010551871](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612010551871.png)

第一题：数字和字符串之间的操作，在python上很好实现

第二题：这是那个经典题，就是codetop里面排名很靠前的，找到前一个位置，两次max的一个题“无重复字符的最长子串”，原题：https://leetcode.cn/problems/longest-substring-without-repeating-characters/

第三题：LR往左往右移动的，其实是一个经典的问题，因为可以看做两只蚂蚁（机器人）完成了身份替换，而不是碰撞后往两边走了，那么就是直接记录最后状态，然后积累一个板子，就是数轴上所有点，求所有两两点之间的距离和，可以使用前缀和的思路在On的时间复杂度内解决问题。（最后忘了取模还被hidden case wa了，人有点麻）板子原题：https://leetcode.cn/problems/sum-of-absolute-differences-in-a-sorted-array/

第四题：g

<!--more-->

# 第一题：6461. 判断一个数是否迷人

https://leetcode.cn/problems/check-if-the-number-is-fascinating/

## 题目大意

![image-20230612011310384](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612011310384.png)

## 分析和解答

数字和字符串之间的操作，在python上很好实现

```python
class Solution:
    def isFascinating(self, n: int) -> bool:
        nn = n * 2
        nnn = n * 3
        
        str_n = str(n) + str(nn) + str(nnn)
        from collections import Counter
        
        cnt = Counter(str_n)
        
        print(cnt)
        for i in range(10):
            if i == 0:
                if cnt[str(i)] != 0:
                    return False
            else:
                if cnt[str(i)] != 1:
                    return False
        
        return True
```

# 第二题：2730. 找到最长的半重复子字符串

https://leetcode.cn/contest/biweekly-contest-106/problems/find-the-longest-semi-repetitive-substring/

## 题目大意

![image-20230612011403538](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612011403538.png)

## 分析和解答

这是那个经典题，就是codetop里面排名很靠前的，找到前一个位置，两次max的一个题“无重复字符的最长子串”，原题：https://leetcode.cn/problems/longest-substring-without-repeating-characters/

```python
class Solution:
    def longestSemiRepetitiveSubstring(self, s: str) -> int:
        
        if len(s) == 1:
            return 1
        
        
        p = 0  # 上次重复字符串出现的前一个位置
        pp = 0  # 上次重复字符串出现的位置
        res = 0
        
        for i in range(1, len(s)):
            if s[i] == s[i-1]:
                p = pp
                pp = max(pp, i)
            # print(p, pp)
            res = max(res, i-p+1)
        
        return res
```

# 第三题：6426. 移动机器人

https://leetcode.cn/contest/biweekly-contest-106/problems/movement-of-robots/

## 题目大意

![image-20230612011428235](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612011428235.png)

![image-20230612011438693](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612011438693.png)

## 分析和解答

LR往左往右移动的，其实是一个经典的问题，因为可以看做两只蚂蚁（机器人）完成了身份替换，而不是碰撞后往两边走了，那么就是直接记录最后状态，然后积累一个板子，就是数轴上所有点，求所有两两点之间的距离和，可以使用前缀和的思路在On的时间复杂度内解决问题。（最后忘了取模还被hidden case wa了，人有点麻）板子原题：https://leetcode.cn/problems/sum-of-absolute-differences-in-a-sorted-array/

```python
class Solution:
    def sumDistance(self, nums: List[int], s: str, d: int) -> int:
        for i in range(len(nums)):
            if s[i] == 'R':
                nums[i] += d
            else:
                nums[i] -= d
        
        nums.sort()
        n = len(nums)
        
        res = 0
        
        prev = 0
        curr = sum(nums) - nums[0] * n    
        res = curr
        
        for i in range(1, n):
            d = nums[i] - nums[i - 1]
            prev = curr
            curr = prev -  (n - i * 2) * d
            res += curr
        return (res // 2) % (10 ** 9 + 7)
```

