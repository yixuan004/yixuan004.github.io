---
title: 107场LeetCode双周赛
date: 2023-06-25 22:40:25
tags:
	- EASY
	- MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

被t3直接坐牢住的周赛，t2能贪+大模拟写出来说明状态还是在线的，虽然上分了但还是不要太在意分数继续冲吧（好像受外服的unrate故障影响了，预测不准）

![image-20230626003901712](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230626003901712.png)

![image-20230626003943094](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230626003943094.png)

第一题：直接暴力就行了，python可以用`[::-1]`这个reverse方法；

第二题：这题能做出来实际就挺运气的了，自己写了个大模拟不知道会不会被rejudge，自己是状态机那种思路，加上比赛时候就没写函数来封装了，基本就是`AA->BB`，`BB->AA、AB`，`AB->AB、AA`这样的，然后后来赛后群里大佬说，AB可以自旋所以肯定都能用上，然后AA BB就看谁少，然后再看最后能不能补上一个这样的思路；

第三题：一上来看错题了，以为可以不连续选择，再加上最近正好看到了状压dp的题，直接一下<<1000去了哈哈哈，后来发现是连续的写记忆化dfs，一步步优化，最后还是没能把length这个维度去掉，根据群里大佬说的：参数是记忆状态的，然后要什么就返回什么，想一下其实就是不能用global这样的，题目详解里面继续写了；

第四题：比赛的时候没注意到是个medium题，t3有思路就一直写下去了，找机会补一下t4；

<!--more-->

# 第一题：6892.最大字符串配对数目

https://leetcode.cn/contest/biweekly-contest-107/problems/find-maximum-number-of-string-pairs/

## 题目大意

![image-20230626004350344](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230626004350344.png)

## 分析和解答

直接暴力就行了，python可以用`[::-1]`这个reverse方法

```python
class Solution:
    def maximumNumberOfStringPairs(self, words: List[str]) -> int:
        res = 0
        for i in range(len(words)):
            for j in range(i+1, len(words)):
                if words[i] == words[j][::-1]:
                    res += 1
        
        return res
```

# 第二题：6895.构造最长的新字符串

https://leetcode.cn/contest/biweekly-contest-107/problems/construct-the-longest-new-string/

## 题目大意

![image-20230626004551121](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230626004551121.png)

## 分析和解答

这题能做出来实际就挺运气的了，自己写了个大模拟不知道会不会被rejudge，自己是状态机那种思路，加上比赛时候就没写函数来封装了，基本就是`AA->BB`，`BB->AA、AB`，`AB->AB、AA`这样的，然后后来赛后群里大佬说，AB可以自旋所以肯定都能用上，然后AA BB就看谁少，然后再看最后能不能补上一个这样的思路

```python
class Solution:
    def longestString(self, xx: int, yy: int, zz: int) -> int:
        res = 0
        
        
        
        # 从x开始
        x, y, z = xx, yy, zz
        prev_res = ""
        status = ""
        if x > 0:
            now_res = "AA"  # 从x开始
            status = "x"
            x -= 1
            while prev_res != now_res:
                prev_res = now_res
                if status == "x":
                    if y > 0:
                        y -= 1
                        now_res += "BB"
                        status = "y"
                    else:
                        break
                elif status == "y":
                    flag = False
                    if z > 0:
                        z -= 1
                        now_res += "AB"
                        status = "z"
                        flag = True
                    elif not flag and x > 0:
                        x -= 1
                        now_res += "AA"
                        status = "x"
                    else:
                        break
                elif status == "z":
                    flag = False
                    if z > 0:
                        z -= 1
                        now_res += "AB"
                        status = "z"
                    elif not flag and x > 0:
                        x -= 1
                        now_res += "AA"
                        status = "x"
                    else:
                        break
            print("x: now_res", now_res)
            res = max(res, len(now_res))
        
        # 从y开始
        x, y, z = xx, yy, zz
        prev_res = ""
        status = ""
        if y > 0:
            now_res = "BB"  # 从y开始
            status = "y"
            y -= 1
            while prev_res != now_res:
                prev_res = now_res
                if status == "x":
                    if y > 0:
                        y -= 1
                        now_res += "BB"
                        status = "y"
                    else:
                        break
                elif status == "y":
                    flag = False
                    if z > 0:
                        z -= 1
                        now_res += "AB"
                        status = "z"
                        flag = True
                    elif not flag and x > 0:
                        x -= 1
                        now_res += "AA"
                        status = "x"
                    else:
                        break
                elif status == "z":
                    flag = False
                    if z > 0:
                        z -= 1
                        now_res += "AB"
                        status = "z"
                    elif not flag and x > 0:
                        x -= 1
                        now_res += "AA"
                        status = "x"
                    else:
                        break
            print("y: now_res", now_res)
            res = max(res, len(now_res))
        
        # 从z开始
        x, y, z = xx, yy, zz
        prev_res = ""
        status = ""
        if z > 0:
            now_res = "AB"  # 从z开始
            status = "z"
            z -= 1
            while prev_res != now_res:
                prev_res = now_res
                if status == "x":
                    if y > 0:
                        y -= 1
                        now_res += "BB"
                        status = "y"
                    else:
                        break
                elif status == "y":
                    flag = False
                    if z > 0:
                        z -= 1
                        now_res += "AB"
                        status = "z"
                        flag = True
                    elif not flag and x > 0:
                        x -= 1
                        now_res += "AA"
                        status = "x"
                    else:
                        break
                elif status == "z":
                    flag = False
                    if z > 0:
                        z -= 1
                        now_res += "AB"
                        status = "z"
                    elif not flag and x > 0:
                        x -= 1
                        now_res += "AA"
                        status = "x"
                    else:
                        break
            print("z: now_res", now_res)
            res = max(res, len(now_res))
                
        return res
```

# 第三题：2746.字符串连接删减字母

https://leetcode.cn/contest/biweekly-contest-107/problems/decremental-string-concatenation/

## 题目大意

![image-20230626004729458](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230626004729458.png)

![image-20230626004804825](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230626004804825.png)

## 分析和解答

一上来看错题了，以为可以不连续选择，再加上最近正好看到了状压dp的题，直接一下<<1000去了哈哈哈，后来发现是连续的写记忆化dfs，一步步优化，最后还是没能把length这个维度去掉，根据群里大佬说的：参数是记忆状态的，然后要什么就返回什么，想一下其实就是不能用global这样的

1）先是没有看到是从str0一直到strn-1这个条件，以为可以跳着来，再加上最近看的状压dp很多，直接给了个这个思路

```python
class Solution:
    def minimizeConcatenatedLength(self, words: List[str]) -> int:
        global res
        res = 10 ** 9
        
        
        @cache
        def dfs(i, j, now_str):
            if i == 0:  # 计算一个str的长度
                global res
                res = min(len(now_str), res)
                return 
            
            for k, word in enumerate(words):
                if (i >> k) & 1:
                    if len(now_str) == 0: 
                        dfs(i^(1<<k), k, now_str+word)
                    else:
                        if now_str[-1] == word[0]:
                            dfs(i^(1<<k), k, now_str+word[1:])
                        else:
                            dfs(i^(1<<k), k, now_str+word)

            return
    
        
        n = len(words)
        u = (1<<n) - 1
        
        for i in range(n):
            dfs(u^(1<<i), i, words[i])
        return res
```

还没有到RuntimeError的时候，就被这个case卡了，又仔细看了下题+群里讨论了下case才知道要连续的

![image-20230626005255346](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230626005255346.png)

2）于是乎转向普通dfs的思路，先写了一版把now_str作为参数传递的，这个是RE了又，因为可能记录这个就太多了

```python
class Solution:
    def minimizeConcatenatedLength(self, words: List[str]) -> int:
        global res
        res = 10 ** 9
        
        
        @cache
        def dfs(i, now_str):
            if i == len(words):
                global res
                res = min(len(now_str), res)
                return 
            
            if len(now_str) and now_str[-1] == words[i][0]:
                dfs(i+1, now_str+words[i][1:])
            if len(now_str) and now_str[0] == words[i][-1]:
                dfs(i+1, words[i]+now_str[1:])
            
            
            dfs(i+1, words[i]+now_str)
            dfs(i+1, now_str+words[i])
                
            return 
        
        
        dfs(0, "")
        return res
```

3）优化写成只存s和e，但是这里把length带在参数里面，还是爆了（记忆化只记忆状态，而不是要记录值的！）

```python
class Solution:
    def minimizeConcatenatedLength(self, words: List[str]) -> int:
        global res
        res = 10 ** 9
        
        
        words_len = [len(words[i]) for i in range(len(words))]
        
        
        @cache
        def dfs(i, start, end, length):
            if i == len(words):
                global res
                res = min(length, res)
                return 
            
            if end == words[i][0]:
                dfs(i+1, start, words[i][-1], length+words_len[i]-1)
            else:
                dfs(i+1, start, words[i][-1], length+words_len[i])
            if start == words[i][-1]:
                dfs(i+1, words[i][0], end, length+words_len[i]-1)
            else:
                dfs(i+1, words[i][0], end, length+words_len[i])
                
            return 
        
        
        dfs(1, words[0][0], words[0][-1], words_len[0])
        return res
```

4）最后这里，就是把length记录到返回值，求什么返回什么是记忆化dfs的关键

```python
class Solution:
    def minimizeConcatenatedLength(self, words: List[str]) -> int:
        words_len = [len(words[i]) for i in range(len(words))]
        
        @cache
        def dfs(i, start, end):
            if i == len(words):
                return 0
            res = 10 ** 9
            if end == words[i][0]:

                # 当前位置的贡献：words_len[i]-1
                # 下一个位置的贡献：dfs(i+1, start, words[i][-1])
                res = min(res, dfs(i+1, start, words[i][-1]) + words_len[i]-1)
            else:
                res = min(res, dfs(i+1, start, words[i][-1]) + words_len[i])
            if start == words[i][-1]:
                res = min(res, dfs(i+1, words[i][0], end) + words_len[i]-1)
            else:
                res = min(res, dfs(i+1, words[i][0], end) + +words_len[i])
                
            return res


        return dfs(1, words[0][0], words[0][-1]) + words_len[0]
```









