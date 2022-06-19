---
title: 294场LeetCode周赛
date: 2022-05-24 16:06:58
tags:
    - 字符串
    - 贪心
    - 数组
    - 排序
    - 几何
    - 数学
    - 数论
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

快乐羽毛球就没有现场做这场周赛了hh，事后发现好像还是最好没有现场做这一场周赛，前三个题目都很简单但是各有各的坑，包括小数的向下取整保存，还有两个小数点后特别多位数精度比较的问题，要 `from decimal import Decimal`，然后用这样的对象来比，总之感觉就是一场奇奇怪怪的周赛哈哈哈哈，如果真的按时参加的话心态可能要崩，全都是奇奇怪怪的BUG；

第一题：按照题目的要求来统计就行，注意要向下取整实际上直接完成整除就可以，做的时候还在那小数点搞来搞去的也是服了自己；

第二题：理解题意后按照差值排个序贪心就可以，这题目写的带上背包也是笑死；

第三题：好几个坑点，首先需要按照days来排序否则顺序可能是乱的，其次注意每次days之间的差值不一定是1，再其次注意精度问题！精度这个问题属实是个大坑，出题人感觉也是瞄准了这一点的罚时，其中有一个badcase是：`[[1,1],[500000000,499999999],[1000000000,999999998]]` 实际上差值是非常接近的；

第四题：咕咕QAQ；

<!--more-->

# 第一题：2278.字母在字符串中的百分比

[题目链接](https://leetcode.cn/problems/percentage-of-letter-in-string/)

## 题目大意

给你一个字符串 `s` 和一个字符 `letter` ，返回在 `s` 中等于 `letter` 字符所占的 **百分比** ，向下取整到最接近的百分比。

示例1：
```
输入：s = "foobar", letter = "o"
输出：33
解释：
等于字母 'o' 的字符在 s 中占到的百分比是 2 / 6 * 100% = 33% ，向下取整，所以返回 33 。
```

示例2：
```
输入：s = "jjjj", letter = "k"
输出：0
解释：
等于字母 'k' 的字符在 s 中占到的百分比是 0% ，所以返回 0 。
```

提示：
- `1 <= s.length <= 100`
- `s` 由小写英文字母组成
- `letter` 是一个小写英文字母

## 分析和解答

按照题目的要求来统计就行，注意要向下取整实际上直接完成整除就可以，做的时候还在那小数点搞来搞去的也是服了自己；

```python
class Solution(object):
    def percentageLetter(self, s, letter):
        """
        :type s: str
        :type letter: str
        :rtype: int
        """
        cnt = 0
        for i in  range(len(s)):
            if s[i] == letter:
                cnt += 1
        
        return cnt * 100 // len(s)

        """
        res = (cnt * 1.0 / len(s) ) * 100
        r = int(str(res).split('.')[0])
        return r
        """
```

# 第二题：2279.装满石头的背包的最大数量

[题目链接](https://leetcode.cn/problems/maximum-bags-with-full-capacity-of-rocks/)

## 题目大意

现有编号从 `0` 到 `n - 1` 的 `n` 个背包。给你两个下标从 **0** 开始的整数数组 `capacity` 和 `rocks` 。第 `i` 个背包最大可以装 `capacity[i]` 块石头，当前已经装了 `rocks[i]` 块石头。另给你一个整数 `additionalRocks` ，表示你可以放置的额外石头数量，石头可以往 **任意** 背包中放置。

请你将额外的石头放入一些背包中，并返回放置后装满石头的背包的 **最大** 数量。

示例1：
```
输入：capacity = [2,3,4,5], rocks = [1,2,4,4], additionalRocks = 2
输出：3
解释：
1 块石头放入背包 0 ，1 块石头放入背包 1 。
每个背包中的石头总数是 [2,3,4,4] 。
背包 0 、背包 1 和 背包 2 都装满石头。
总计 3 个背包装满石头，所以返回 3 。
可以证明不存在超过 3 个背包装满石头的情况。
注意，可能存在其他放置石头的方案同样能够得到 3 这个结果。
```

示例2：
```
输入：capacity = [10,2,2], rocks = [2,2,0], additionalRocks = 100
输出：3
解释：
8 块石头放入背包 0 ，2 块石头放入背包 2 。
每个背包中的石头总数是 [10,2,2] 。
背包 0 、背包 1 和背包 2 都装满石头。
总计 3 个背包装满石头，所以返回 3 。
可以证明不存在超过 3 个背包装满石头的情况。
注意，不必用完所有的额外石头。
```

提示：
- `n == capacity.length == rocks.length`
- `1 <= n <= 5 * 10^4`
- `1 <= capacity[i] <= 10^9`
- `0 <= rocks[i] <= capacity[i]`
- `1 <= additionalRocks <= 10^9`

## 分析和解答

理解题意后按照差值排个序贪心就可以，这题目写的带上背包也是笑死；

```python
class Solution(object):
    def maximumBags(self, capacity, rocks, additionalRocks):
        """
        :type capacity: List[int]
        :type rocks: List[int]
        :type additionalRocks: int
        :rtype: int
        """
        cha = []
        for i in range(len(capacity)):
            cha.append(capacity[i]-rocks[i])
        
        cha.sort()

        cnt = 0
        for i in range(len(cha)):
            if cha[i] <= additionalRocks:
                cnt += 1
                additionalRocks -= cha[i]
        
        return cnt
```

# 第三题：2280.表示一个折线图的最少线段数

[题目链接](https://leetcode.cn/problems/minimum-lines-to-represent-a-line-chart/)

## 题目大意

给你一个二维整数数组 `stockPrices` ，其中 `stockPrices[i] = [day_i, price_i]` 表示股票在 `day_i` 的价格为 `price_i` 。**折线图** 是一个二维平面上的若干个点组成的图，横坐标表示日期，纵坐标表示价格，折线图由相邻的点连接而成。比方说下图是一个例子：

![](/images/2022-05-25-00-57-12.png)

请你返回要表示一个折线图所需要的 **最少线段数** 。

示例1：

![](/images/2022-05-25-00-58-50.png)
```
输入：stockPrices = [[1,7],[2,6],[3,5],[4,4],[5,4],[6,3],[7,2],[8,1]]
输出：3
解释：
上图为输入对应的图，横坐标表示日期，纵坐标表示价格。
以下 3 个线段可以表示折线图：
- 线段 1 （红色）从 (1,7) 到 (4,4) ，经过 (1,7) ，(2,6) ，(3,5) 和 (4,4) 。
- 线段 2 （蓝色）从 (4,4) 到 (5,4) 。
- 线段 3 （绿色）从 (5,4) 到 (8,1) ，经过 (5,4) ，(6,3) ，(7,2) 和 (8,1) 。
可以证明，无法用少于 3 条线段表示这个折线图。
```

示例2：

![](/images/2022-05-25-00-59-13.png)
```
输入：stockPrices = [[3,4],[1,2],[7,8],[2,3]]
输出：1
解释：
如上图所示，折线图可以用一条线段表示。
```

提示：
- `1 <= stockPrices.length <= 10^5`
- `stockPrices[i].length == 2`
- `1 <= day_i, price_i <= 10^9`
- 所有 `day_i` 互不相同 。

## 分析和解答

好几个坑点，首先需要按照days来排序否则顺序可能是乱的，其次注意每次days之间的差值不一定是1，再其次注意精度问题！精度这个问题属实是个大坑，出题人感觉也是瞄准了这一点的罚时，其中有一个badcase是：`[[1,1],[500000000,499999999],[1000000000,999999998]]` 实际上差值是非常接近的；

```python
class Solution:
    def minimumLines(self, stockPrices: List[List[int]]) -> int:
        from decimal import Decimal

        if len(stockPrices) == 1:
            return 0
        stockPrices.sort(key=lambda x: (x[0], x[1]))
        # print(stockPrices)
        k = 0
        res = 1
        for i in range(1, len(stockPrices)):
            tmp = Decimal(stockPrices[i][1] - stockPrices[i-1][1]) / Decimal((stockPrices[i][0] - stockPrices[i-1][0]))
            if i == 1:
                continue
            prev = Decimal(stockPrices[i-1][1] - stockPrices[i-2][1])/ Decimal(stockPrices[i-1][0] - stockPrices[i-2][0])

            if prev == tmp:
                continue
            else:
                res += 1

        return res
```