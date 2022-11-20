---
title: 2055.蜡烛之间的盘子-python
date: 2022-03-08 20:22:04
tags:
    - 数组
    - 字符串
    - 前缀和
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode每日一题
---

2055.蜡烛之间的盘子（中等）

每日一题的话就不在这里完整粘贴题目的题面了，换用自己的想法写法说一说

这个题感觉和周赛第三题非常接近？是一个感觉可以积累的很经典的前缀和题目，每个盘子位置先存到左右蜡烛的索引，然后再存前缀，实际上两个位置之间的差，就是【左边那个位置的右边蜡烛位置】，和【右边那个位置的左边蜡烛位置】之间的差了，感觉是一种角标索引（哈希表）背景下的前缀和问题，这种区间多次查询感觉确实不少前缀和问题

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/plates-between-candles/)

题目大意：

给你一个长桌子，桌子上盘子和蜡烛排成一列。给你一个下标从 **0** 开始的字符串 ```s``` ，它只包含字符 ```'*'``` 和 ```'|'``` ，其中 ```'*'``` 表示一个 **盘子** ，```'|'``` 表示一支 **蜡烛** 。

同时给你一个下标从 **0** 开始的二维整数数组 ```queries``` ，其中 ```queries[i] = [lefti, righti]``` 表示 **子字符串** ```s[lefti...righti]``` **（包含左右端点的字符）**。对于每个查询，你需要找到 **子字符串中** 在 **两支蜡烛之间** 的盘子的 数目 。如果一个盘子在 **子字符串中** 左边和右边 **都** 至少有一支蜡烛，那么这个盘子满足在 **两支蜡烛之间** 。

- 比方说，```s = "||**||**|*"``` ，查询 ```[3, 8]``` ，表示的是子字符串 ```"*||**|"``` 。子字符串中在两支蜡烛之间的盘子数目为 ```2``` ，子字符串中右边两个盘子在它们左边和右边 **都** 至少有一支蜡烛。

请你返回一个整数数组 ```answer``` ，其中 ```answer[i]``` 是第 ```i``` 个查询的答案。


示例1：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-08-21-16-07.png)
```
输入：s = "**|**|***|", queries = [[2,5],[5,9]]
输出：[2,3]
解释：
- queries[0] 有两个盘子在蜡烛之间。
- queries[1] 有三个盘子在蜡烛之间。
```

示例2：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-08-21-16-18.png)
```
输入：s = "***|**|*****|**||**|*", queries = [[1,17],[4,5],[14,17],[5,11],[15,16]]
输出：[9,0,0,0,0]
解释：
- queries[0] 有 9 个盘子在蜡烛之间。
- 另一个查询没有盘子在蜡烛之间。
```

提示：
- ```3 <= s.length <= 10^5```
- ```s``` 只包含字符 ```'*'``` 和 ```'|'``` 。
- ```1 <= queries.length <= 10^5```
- ```queries[i].length == 2```
- ```0 <= left_i <= right_i < s.length```


# 分析和解答

这个题感觉和周赛第三题非常接近？是一个感觉可以积累的很经典的前缀和题目，每个盘子位置先存到左右蜡烛的索引，然后再存前缀，实际上两个位置之间的差，就是【左边那个位置的右边蜡烛位置】，和【右边那个位置的左边蜡烛位置】之间的差了，感觉是一种角标索引（哈希表）背景下的前缀和问题，这种区间多次查询感觉确实不少前缀和问题

```python
class Solution(object):
    def platesBetweenCandles(self, s, queries):
        """
        :type s: str
        :type queries: List[List[int]]
        :rtype: List[int]
        """
        # 经典前缀题目思路
        # 先遍历两边，找到每个位置的左边和右边第一次碰到蜡烛的位置
        # 然后再看前缀和，截止到当前位置处，出现过几个*了
        left_candle_idx = [-1 for _ in range(len(s))]
        right_candle_idx = [-1 for _ in range(len(s))]
        plate_prefix_sum = [-1 for _ in range(len(s))]


        # 先正着走一遍，找到每个index位置处左边的那个candle的角标
        left = -1
        for i in range(len(s)):
            if s[i] == '|':
                left = i
                left_candle_idx[i] = i
            elif s[i] == '*':
                left_candle_idx[i] = left
        print("left_candle_idx: ", left_candle_idx)


        # 倒着走一遍，找到每个index位置处右边的那个candle的角标
        right = -1
        for i in range(len(s)-1, -1, -1):
            if s[i] == '|':
                right = i
                right_candle_idx[i] = i
            else:
                right_candle_idx[i] = right
        print("right_candle_idx: ", right_candle_idx)

        # 正着走找prefix
        prefix = 0
        for i in range(len(s)):
            if s[i] == '*':
                prefix += 1
            plate_prefix_sum[i] = prefix
        print("plate_prefix_sum: ", plate_prefix_sum)

        res = []
        for i in range(len(queries)):
            l, r = queries[i][0], queries[i][1]
            # 特殊处理
            if l == r:
                res.append(0)
                continue

            tmp = plate_prefix_sum[left_candle_idx[r]]-plate_prefix_sum[right_candle_idx[l]]
            # 这里可能还会有异常，一下不好想感觉只能通过case看一下
            res.append(tmp) if tmp >= 0 else res.append(0)
        return res
```