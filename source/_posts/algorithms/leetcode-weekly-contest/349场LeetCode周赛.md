---
title: 349场LeetCode周赛
date: 2023-06-11 21:02:01
tags:
	- EASY
	- MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

https://leetcode.cn/contest/weekly-contest-349/

再次保分大师is me，只做两个题居然都有1000名出头，还没来得及分析第三题的难度，前两个题t1很无脑，t2就是想找到不是a的，然后想到"aaaaaa"这样的全a case

![image-20230612004544984](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612004544984.png)

第一题：排序后返回nums[1]就行，如果长度小于等于2，则return -1

第二题：找到第一个非a字符，然后找到下一个a，把这个中间的给-1，注意python的语法`chr(ord(x)-1)`

第三题：g TODO

第四题：g

<!--more-->

# 第一题：6470. 既不是最小值也不是最大值

https://leetcode.cn/contest/weekly-contest-349/problems/neither-minimum-nor-maximum/

## 题目大意

![image-20230612004903451](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612004903451.png)

## 分析和解答

排序后返回nums[1]就行，如果长度小于等于2，则return -1

```python
class Solution:
    def findNonMinOrMax(self, nums: List[int]) -> int:
        nums.sort()
        
        if len(nums) <= 2:
            return -1
        else:
            return nums[1]
```

# 第二题：6465. 执行子串操作后的字典序最小字符串

https://leetcode.cn/contest/weekly-contest-349/problems/lexicographically-smallest-string-after-substring-operation/

## 题目大意

![image-20230612004819359](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612004819359.png)

## 分析和解答

找到第一个非a字符，然后找到下一个a，把这个中间的给-1，注意python的语法`chr(ord(x)-1)`

```python
class Solution:
    def smallestString(self, s: str) -> str:
        
        # 找到第一个非a的字符
        p = -1
        for i in range(len(s)):
            if s[i] != 'a':
                p = i
                break
        
        if p == -1:
            return s[:-1] + 'z'
        
        
        
        # 从p开始，一路找到下一个a
        q = len(s)
        for i in range(p+1, len(s)):
            if s[i] == 'a':
                q = i - 1
                break
                
        # 主要就是把p q之间的字符串减小了
        res = []
        for i in range(len(s)):
            if i >= p and i <= q:
                res.append(chr(ord(s[i])-1))
            else:
                res.append(s[i])
        
        
        return ''.join(res)
```

