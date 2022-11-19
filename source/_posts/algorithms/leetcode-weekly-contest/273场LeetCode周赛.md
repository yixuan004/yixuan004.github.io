---
title: 273场LeetCode周赛
date: 2021-12-26 16:16:43
tags:
    - 数学
    - 字符串
    - 模拟
    - 数组
    - 哈希表
    - 前缀和
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

第一题：感觉比较水，123400倒过来是004321，再倒回去是1234和原来的数字不一样了，这样其实可以直接判断末尾是不是0，还有是不是单独一个0就可以了；

第二题：矩阵内简单的模拟，up down left right，经过这四个操作后，不能越过矩阵的边界就行了，后缀在python里```[i:]```好像比较简单；

第三题：在当时做的时候何老板就在想是不是和数学有关的，后来听了卿哥的解答后感觉最关键的是abcde这个思路，这样比较容易可能能想到前缀和后缀的问题了，对于一个数字2，比如其角标在```[a, b, c, d, e]```五个位置，那么根据题意可以模拟下每个位置下的和，这样能看出来之间的关系，详见下文解答；

第四题：未来有机会再试了；

<!--more-->

# 第一题：2119.反转两次的数字

[题目链接](https://leetcode-cn.com/problems/a-number-after-a-double-reversal/)

## 题目大意

**反转**一个整数意味着倒置它的所有位。

例如，反转```2021```得到```1202```。反转```12300```得到```321```，**不保留前导零**。
给你一个整数```num```，**反转**```num```得到```reversed1```，**接着反转**```reversed1```得到```reversed2```。如果```reversed2```等于```num```，返回```true```；否则，返回```false```。

示例1：
```
输入：num = 526
输出：true
解释：反转 num 得到 625 ，接着反转 625 得到 526 ，等于 num 。
```

示例2：
```
输入：num = 1800
输出：false
解释：反转 num 得到 81 ，接着反转 81 得到 18 ，不等于 num 。 
```

示例3：
```
输入：num = 0
输出：true
解释：反转 num 得到 0 ，接着反转 0 得到 0 ，等于 num 。
```

提示：
- 0 <= num <= 106

## 分析和解答

这个题感觉不能被题面唬住了，还是判断后边有没有0就可以了，然后单独0的单独处理下；

```python
class Solution(object):
    def isSameAfterReversals(self, num):
        """
        :type num: int
        :rtype: bool
        """
        if len(str(num)) == 1:
            return True
        
        if str(num)[-1] == "0":
            return False
        else:
            return True
```

# 第二题：2120.执行所有后缀指令

[题目链接](https://leetcode-cn.com/problems/execution-of-all-suffix-instructions-staying-in-a-grid/)

## 题目大意

现有一个```n x n```大小的网格，左上角单元格坐标```(0, 0)```，右下角单元格坐标```(n - 1, n - 1)```。给你整数```n```和一个整数数组```startPos```，其中```startPos = [startrow, startcol]```表示机器人最开始在坐标为```(startrow, startcol)```的单元格上。

另给你一个长度为```m```、下标从```0```开始的字符串```s```，其中```s[i]```是对机器人的第```i```条指令：```'L'```（向左移动），```'R'```（向右移动），```'U'```（向上移动）和 ```'D'```（向下移动）。

机器人可以从```s```中的任一第```i```条指令开始执行。它将会逐条执行指令直到```s```的末尾，但在满足下述条件之一时，机器人将会停止：

下一条指令将会导致机器人移动到网格外。
没有指令可以执行。
返回一个长度为```m```的数组```answer```，其中```answer[i]```是机器人从第```i```条指令 开始 ，可以执行的 指令数目 。

示例1：
```
输入：n = 3, startPos = [0,1], s = "RRDDLU"
输出：[1,5,4,3,1,0]
解释：机器人从 startPos 出发，并从第 i 条指令开始执行：
- 0: "RRDDLU" 在移动到网格外之前，只能执行一条 "R" 指令。
- 1:  "RDDLU" 可以执行全部五条指令，机器人仍在网格内，最终到达 (0, 0) 。
- 2:   "DDLU" 可以执行全部四条指令，机器人仍在网格内，最终到达 (0, 0) 。
- 3:    "DLU" 可以执行全部三条指令，机器人仍在网格内，最终到达 (0, 0) 。
- 4:     "LU" 在移动到网格外之前，只能执行一条 "L" 指令。
- 5:      "U" 如果向上移动，将会移动到网格外。
```

示例2：
```
输入：n = 2, startPos = [1,1], s = "LURD"
输出：[4,1,0,0]
解释：
- 0: "LURD"
- 1:  "URD"
- 2:   "RD"
- 3:    "D"
```

示例3：
```
输入：n = 1, startPos = [0,0], s = "LRUD"
输出：[0,0,0,0]
解释：无论机器人从哪条指令开始执行，都会移动到网格外。
```

提示：
- m == s.length
- 1 <= n, m <= 500
- startPos.length == 2
- 0 <= startrow, startcol < n
- s 由 'L'、'R'、'U' 和 'D' 组成

## 分析和解答

比较水，实际上是简单题，执行就可以了，后缀执行用```s[:i]```截断就可以了，边界的判断就像dfs couting lakes的判断一样

```python
class Solution(object):
    def executeInstructions(self, n, startPos, s):
        """
        :type n: int
        :type startPos: List[int]
        :type s: str
        :rtype: List[int]
        """
        matrix = [[0 for i in range(n)] for j in range(n)]
        cnt = [0 for i in range(len(s))]
        
        for i in range(len(s)):
            startX = startPos[0]
            startY = startPos[1]
            tmp_seq = s[i:]
            # print(cnt)
            for j, c in enumerate(tmp_seq):
                if c == 'L':
                    if startY - 1 >= 0:
                        startY = startY - 1
                        cnt[i] += 1
                    else:
                        break
                elif c == 'R':
                    if startY + 1 < n:
                        startY = startY + 1
                        cnt[i] += 1
                    else:
                        break
                elif c == 'U':
                    if startX - 1 >= 0:
                        startX = startX - 1
                        cnt[i] += 1
                    else:
                        break
                elif c == 'D':
                    if startX + 1 < n:
                        startX = startX + 1
                        cnt[i] += 1
                    else:
                        break
            
        return cnt
```


# 第三题：2121.相同元素的间隔之和

[题目链接](https://leetcode-cn.com/problems/intervals-between-identical-elements/)

## 题目大意

给你一个下标从**0**开始、由```n```个整数组成的数组```arr```。

arr 中两个元素的 间隔 定义为它们下标之间的**绝对差**。更正式地，```arr[i]```和```arr[j]```之间的间隔是```|i - j|```。

返回一个长度为```n```的数组```intervals```，其中```intervals[i]```是```arr[i]```和```arr```中每个相同元素（与```arr[i]```的值相同）的**间隔之和**。

注意：```|x|```是```x```的绝对值。

示例1：
```
输入：arr = [2,1,3,1,2,3,3]
输出：[4,2,7,2,4,4,5]
解释：
- 下标 0 ：另一个 2 在下标 4 ，|0 - 4| = 4
- 下标 1 ：另一个 1 在下标 3 ，|1 - 3| = 2
- 下标 2 ：另两个 3 在下标 5 和 6 ，|2 - 5| + |2 - 6| = 7
- 下标 3 ：另一个 1 在下标 1 ，|3 - 1| = 2
- 下标 4 ：另一个 2 在下标 0 ，|4 - 0| = 4
- 下标 5 ：另两个 3 在下标 2 和 6 ，|5 - 2| + |5 - 6| = 4
- 下标 6 ：另两个 3 在下标 2 和 5 ，|6 - 2| + |6 - 5| = 5
```

示例2：
```
输入：arr = [10,5,10,10]
输出：[5,0,3,4]
解释：
- 下标 0 ：另两个 10 在下标 2 和 3 ，|0 - 2| + |0 - 3| = 5
- 下标 1 ：只有这一个 5 在数组中，所以到相同元素的间隔之和是 0
- 下标 2 ：另两个 10 在下标 0 和 3 ，|2 - 0| + |2 - 3| = 3
- 下标 3 ：另两个 10 在下标 0 和 2 ，|3 - 0| + |3 - 2| = 4
```

提示：
- n == arr.length
- 1 <= n <= 105
- 1 <= arr[i] <= 105


## 分析和解答

思路还是很巧妙的，假设一个```[a, b, c, d, e]```角标数组，现在要统计```c```这个位置下的角标绝对差，那么这个位置上就是```(c-a) + (c-b) + (d-c) + (e-c)```，如果要统计
```d```这个位置下的角标绝对差，那么这个位置上就是```(d-a) + (d-b) + (d-c) + (e-d)```，这样：

- 在```c```的位置下看规律，前缀（left_sum）是```a+b```，后缀（right_sum）是```d+e```，求和是：```(c-a) + (c-b) + (d-c) + (e-c)```

- 在```d```的位置下看规律，前缀（left_sum）是```a+b+c```，后缀（right_sum）是```e```，求和是：```(d-a) + (d-b) + (d-c) + (e-d)```

这样感觉可以总结出来规律了，在哪个角标x位置，就是：``` ((x * 前缀个数) - 前缀) + (后缀 - (x * 后缀个数))```，这样前缀后缀可以通过一种类似于滑动窗口的方法更新，大大减少了时间复杂度。


```python
class Solution(object):
    def getDistances(self, arr):
        """
        :type arr: List[int]
        :rtype: List[int]
        """
        return_list = [0 for i in range(len(arr))]

        # 把arr转化为一个map，类似于3: [2, 5, 6]这样的，前边是数值，后边是数值的角标
        tmp_dict = {}  # {1: [1, 3], 2: [0, 4], 3: [2, 5, 6]}
        for i, item in enumerate(arr):
            if tmp_dict.get(arr[i]) is None:
                tmp_dict[arr[i]] = [i]
            else:
                tmp_dict[arr[i]].append(i)
        
        for key, value in tmp_dict.items():
            # 在每个value中，使用 a b c d e的思想
            left_sum = 0
            left_cnt = 0
            right_sum = 0
            right_cnt = 0
            for i in range(0, len(value)):
                right_sum += value[i]
                right_cnt += 1
            
            for i in range(len(value)):
                # 滑动窗口的感觉
                right_sum -= value[i]
                right_cnt -= 1
                return_list[value[i]] = (right_sum - (right_cnt * value[i])) + (left_cnt * value[i] - left_sum)
                left_sum += value[i]
                left_cnt += 1
                
        return return_list
```