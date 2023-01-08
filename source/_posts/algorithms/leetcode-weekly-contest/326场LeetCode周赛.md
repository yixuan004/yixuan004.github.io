---
title: 326场LeetCode周赛
date: 2023-01-01 11:15:40
tags:
	- EASY
	- MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

之前断断续续的参加了一些周赛，但是好久没有把周赛内容整理到博客上了，也用这次的AK开启自己2023年的第一篇博客吧！希望2023年用自己的奋斗让一切都顺利！

![image-20230101113535233](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101113535233.png)

第一题：迟到了两分钟，读了下题，就是一个按位来的操作，类型转化一下就可以；

第二题：全乘起来再套质因数分解模板超界了一回（作为t2以为全乘起来不会爆的），之后就是每个数字每个数字的处理就好了；

第三题：这种按位截取分解字符串的，感觉属于代码比较恶心需要精调一下的题，不烦躁把题目逻辑梳理好就可以了；

第四题：套了个素数埃式筛的模板，因为要求区间就是两个set相减（没有魔改板子），但是如果以0作为板子输入越界了，把这里优化了一下就好了；

<!--more-->

# 第一题：6278. 统计能整除数字的位数

https://leetcode.cn/contest/weekly-contest-326/problems/count-the-digits-that-divide-a-number/

## 题目大意

![image-20230101113226104](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101113226104.png)

## 分析和解答

迟到了两分钟，读了下题，就是一个按位来的操作，类型转化一下就可以；

```python
class Solution:
    def countDigits(self, num: int) -> int:
        res = 0
        for i in range(len(str(num))):
            if num % int(str(num)[i]) == 0:
                res += 1
        
        return res
```

# 第二题：6279. 数组乘积中的不同质因数数目

https://leetcode.cn/contest/weekly-contest-326/problems/distinct-prime-factors-of-product-of-array/

## 题目大意

![image-20230101113201301](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101113201301.png)

## 分析和解答

全乘起来再套质因数分解模板超界了一回（作为t2以为全乘起来不会爆的），之后就是每个数字每个数字的处理就好了

```python
class Solution:
    def distinctPrimeFactors(self, nums: List[int]) -> int:
        # 对total进行质因数分解
        def breakdown(N):
            result = []
            for i in range(2, int(sqrt(N)) + 1):
                if N % i == 0: # 如果 i 能够整除 N，说明 i 为 N 的一个质因子。
                    while N % i == 0:
                        N = N // i
                        result.append(i)
            if N != 1: # 说明再经过操作之后 N 留下了一个素数
                result.append(N)
            return result
        
        st = set()
        for i in range(len(nums)):
            bd = breakdown(nums[i])
            for j in range(len(bd)):
                st.add(bd[j])
        
        
        
        return len(st)
```

# 第三题：6196. 将字符串分割成值不超过 K 的子字符串

https://leetcode.cn/contest/weekly-contest-326/problems/partition-string-into-substrings-with-values-at-most-k/

## 题目大意

![image-20230101113412063](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101113412063.png)

## 分析和解答

这种按位截取分解字符串的，感觉属于代码比较恶心需要精调一下的题，不烦躁把题目逻辑梳理好就可以了；

```python
class Solution:
    def minimumPartition(self, s: str, k: int) -> int:
        
        # 先判断是不是每个数都小于k
        for i in range(len(s)):
            if int(s[i]) > k:
                return -1
        
        i = 0
        start = 0
        res = 0
        
        ttmp = []
        
        while i < len(s):
            if int(s[start: i+1]) <= k:
                i += 1
            else:
                ttmp.append(s[start: i])
                start = i
                res += 1
        
        # print(s[start: i])
        if int(s[start: i]) <= k:
            res += 1
        
        
        return res
```

# 第四题：6280. 范围内最接近的两个质数

https://leetcode.cn/contest/weekly-contest-326/problems/closest-prime-numbers-in-range/

## 题目大意

![image-20230101113353311](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230101113353311.png)

## 分析和解答

套了个素数埃式筛的模板，因为要求区间就是两个set相减（没有魔改板子），但是如果以0作为板子输入越界了，把这里优化了一下就好了；

```python
class Solution:
    def closestPrimes(self, left: int, right: int) -> List[int]:
        def sieve_of_eratosthenes(n):
            pick = [1] * (n+1)
            pick[0] = pick[1] = 0
            for i in range(n+1):
                if pick[i] != 0:
                    for j in range(i*i, n+1, i):
                      pick[j] = 0
            return [x for x in range(n+1) if pick[x]]
        
        if left == 1:
            tmp_st1 = set()
        else:
            tmp_st1 = set(sieve_of_eratosthenes(left-1))
        
        if right == 1:
            tmp_st2 = set()    
        else:
            tmp_st2 = set(sieve_of_eratosthenes(right))
        st_to_list = sorted(list(tmp_st2 - tmp_st1))
        
        res = [-1, -1]
        if len(st_to_list) < 2:
            return res
        
        res = [st_to_list[0], st_to_list[1]]
        tmp = st_to_list[1] - st_to_list[0]
        for i in range(2, len(st_to_list)):
            if st_to_list[i] - st_to_list[i-1] < tmp:
                tmp = st_to_list[i] - st_to_list[i-1]
                res = [st_to_list[i-1], st_to_list[i]]
        
        return res
```
