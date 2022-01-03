---
title: 274周LeetCode周赛
date: 2022-01-02 22:13:43
tags:
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

前三个感觉都是水题，自己做的话感觉难得的10-20分钟就搞定了，因为是后来做的看了眼大佬们最快都做了20多分钟，前三个题这么简单的话第四题估计自己现在的水平很难做出来了；

第一题：一个由ab组成的字符串，如果b出现过了后边就不能出现a了，用一个flag判断就可以了；

第二题：用Counter统计每行1的个数，然后把0的行排除掉，```result += count[i] * count[i+1]```就可以了；

第三题：还是用到了排序，第一次遍历过去的时候可以先不断贪心，然后排序，再一个个加入来判断就行了；

第四题：未来有机会再试了


# 第一题：2124.检查是否所有A都在B之前

[题目链接](https://leetcode-cn.com/problems/check-if-all-as-appears-before-all-bs/)

## 题目大意

给你一个**仅**由字符```'a'```和```'b'```组成的字符串```s```。如果字符串中 每个```'a'```都出现在 每个```'b'```之前，返回```true```；否则，返回```false```。

示例1：
```
输入：s = "aaabbb"
输出：true
解释：
'a' 位于下标 0、1 和 2 ；而 'b' 位于下标 3、4 和 5 。
因此，每个 'a' 都出现在每个 'b' 之前，所以返回 true 。
```

示例2：
```
输入：s = "abab"
输出：false
解释：
存在一个 'a' 位于下标 2 ，而一个 'b' 位于下标 1 。
因此，不能满足每个 'a' 都出现在每个 'b' 之前，所以返回 false 。
```

示例3：
```
输入：s = "bbb"
输出：true
解释：
不存在 'a' ，因此可以视作每个 'a' 都出现在每个 'b' 之前，所以返回 true 。
```

提示：
- 1 <= s.length <= 100
- s[i] 为 'a' 或 'b'


## 分析和解答

easy题，读懂题意就好

```python
class Solution(object):
    def checkString(self, s):
        """
        :type s: str
        :rtype: bool
        """
        
        flag = False
        for i, c in enumerate(s):
            if flag == True and c == 'a':
                return False
            if c == 'b':
                flag = True
        return True
```


# 第二题：2125.银行中的激光束数量

[题目链接](https://leetcode-cn.com/problems/number-of-laser-beams-in-a-bank/)

## 题目大意

银行内部的防盗安全装置已经激活。给你一个下标从**0**开始的二进制字符串数组```bank```，表示银行的平面图，这是一个大小为```m x n```的二维矩阵。```bank[i]```表示第```i```行的设备分布，由若干```'0'```和若干```'1'```组成。```'0'```表示单元格是空的，而```'1'```表示单元格有一个安全设备。

对任意两个安全设备而言，**如果同时**满足下面两个条件，则二者之间存在 一个 激光束：

两个设备位于两个 不同行 ：```r1```和```r2```，其中```r1 < r2```。
满足```r1 < i < r2```的**所有**行```i```，都**没有安全设备**。
激光束是独立的，也就是说，一个激光束既不会干扰另一个激光束，也不会与另一个激光束合并成一束。

返回银行中激光束的总数量。

示例1：
```
输入：bank = ["011001","000000","010100","001000"]
输出：8
解释：在下面每组设备对之间，存在一条激光束。总共是 8 条激光束：
 * bank[0][1] -- bank[2][1]
 * bank[0][1] -- bank[2][3]
 * bank[0][2] -- bank[2][1]
 * bank[0][2] -- bank[2][3]
 * bank[0][5] -- bank[2][1]
 * bank[0][5] -- bank[2][3]
 * bank[2][1] -- bank[3][2]
 * bank[2][3] -- bank[3][2]
注意，第 0 行和第 3 行上的设备之间不存在激光束。
这是因为第 2 行存在安全设备，这不满足第 2 个条件。
```

示例2：
```
输入：bank = ["000","111","000"]
输出：0
解释：不存在两个位于不同行的设备
```

提示：
- m == bank.length
- n == bank[i].length
- 1 <= m, n <= 500
- bank[i][j] 为 '0' 或 '1'

## 分析和解答

理解题意就比较好做了

```python
class Solution(object):
    def numberOfBeams(self, bank):
        """
        :type bank: List[str]
        :rtype: int
        """
        result = 0
        num_list = []
        for item in bank:
            count = Counter(list(item))['1']
            if count != 0:
                num_list.append(count)

        # print(num_list)
        for i in range(len(num_list) - 1):
            result += num_list[i] * num_list[i+1]

        return result
```

# 第三题：2126.摧毁小行星

[题目链接](https://leetcode-cn.com/problems/destroying-asteroids/)

## 题目大意
给你一个整数```mass```，它表示一颗行星的初始质量。再给你一个整数数组```asteroids```，其中```asteroids[i]```是第```i```颗小行星的质量。

你可以按**任意顺序**重新安排小行星的顺序，然后让行星跟它们发生碰撞。如果行星碰撞时的质量**大于等于**小行星的质量，那么小行星被**摧毁**，并且行星会**获得**这颗小行星的质量。否则，行星将被摧毁。

如果所有小行星**都**能被摧毁，请返回```true```，否则返回```false```。

示例1：
```
输入：mass = 10, asteroids = [3,9,19,5,21]
输出：true
解释：一种安排小行星的方式为 [9,19,5,3,21] ：
- 行星与质量为 9 的小行星碰撞。新的行星质量为：10 + 9 = 19
- 行星与质量为 19 的小行星碰撞。新的行星质量为：19 + 19 = 38
- 行星与质量为 5 的小行星碰撞。新的行星质量为：38 + 5 = 43
- 行星与质量为 3 的小行星碰撞。新的行星质量为：43 + 3 = 46
- 行星与质量为 21 的小行星碰撞。新的行星质量为：46 + 21 = 67
所有小行星都被摧毁。
```

示例2：
```
输入：mass = 5, asteroids = [4,9,23,4]
输出：false
解释：
行星无论如何没法获得足够质量去摧毁质量为 23 的小行星。
行星把别的小行星摧毁后，质量为 5 + 4 + 9 + 4 = 22 。
它比 23 小，所以无法摧毁最后一颗小行星。
```

提示：
- 1 <= mass <= 105
- 1 <= asteroids.length <= 105
- 1 <= asteroids[i] <= 105

## 分析和解答

还是用到了排序，第一次遍历过去的时候可以先不断贪心，然后排序，再一个个加入来判断就行了；

```python
class Solution(object):
    def asteroidsDestroyed(self, mass, asteroids):
        """
        :type mass: int
        :type asteroids: List[int]
        :rtype: bool
        """
        tmp_list = []
        for i, item in enumerate(asteroids):
            if item <= mass:
                mass += item
            else:
                tmp_list.append(item)
        
        tmp_list = sorted(tmp_list, reverse=False)
        
        for item in tmp_list:
            if item <= mass:
                mass += item
            else:
                return False
        
        return True
```