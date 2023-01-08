---
title: 297场LeetCode周赛
date: 2022-06-19 15:54:36
tags:
    - 数组
    - 模拟
    - 动态规划
    - 矩阵
    - 位运算
    - 回溯
    - 状态压缩
    - EASY
    - MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

题目难度：t1(1283)+t2(1658)+t3(1886)；

这周实在是人被虐傻了，一个半小时内只把第一个题坎坷的做出来了。第二个题问题定义很奇怪，还真是第一次见这么写转移的，没想到本质上不是搜索问题而是dp问题，后来给zy做那个周赛题的时候可能也积累到一种思路，就是说看起来像是dfs的问题如果数据范围比较奇怪的话，可能背后隐藏的就是dp类的问题。如果说第二题看起来像搜索但其实不是搜索，那么第三题就看起来不像搜索实际上是个搜索（略有点超出能力范围了），本来自己想了一种n进制的方法，但是无奈超时了，n进制状态压缩也算是给自己积累个思路吧；

第一题：理解下题意就可以，做一个差值的存储，这里差值的存储一开始搞错了，如果从前往后更新的话前边的变了后边的也就跟着变了，要用一个新的或者从后往前来；

第二题：看上去像是dfs但实际上是个dp，也算提醒自己很多dfs题目但是看起来数据超范围的了，就可以用dp的思路给干掉；

第三题：暂时不记录这个题的正确解法了，这种题能想到搜索的思路怎么都还是觉得有点怪，有点没找到突破口，记录一个自己的k进制状态转移方法，虽然超时了但是能保证暴力对；

第四题：咕咕QAQ；

<!--more-->

# 第一题：2303.计算应缴税款总额【难度：1283】

[题目链接](https://leetcode.cn/problems/calculate-amount-paid-in-taxes/)

## 题目大意

给你一个下标从 **0** 开始的二维整数数组 `brackets` ，其中 `brackets[i] = [upperi, percenti]` ，表示第 `i` 个税级的上限是 `upperi` ，征收的税率为 `percenti` 。税级按上限 **从低到高排序**（在满足 `0 < i < brackets.length` 的前提下，`upperi-1 < upperi`）。

税款计算方式如下：
- 不超过 `upper0` 的收入按税率 `percent0` 缴纳
- 接着 `upper1 - upper0` 的部分按税率 `percent1` 缴纳
- 然后 `upper2 - upper1` 的部分按税率 `percent2` 缴纳
- 以此类推

给你一个整数 `income` 表示你的总收入。返回你需要缴纳的税款总额。与标准答案误差不超 `10^-5` 的结果将被视作正确答案。

示例1：
```
输入：brackets = [[3,50],[7,10],[12,25]], income = 10
输出：2.65000
解释：
前 $3 的税率为 50% 。需要支付税款 $3 * 50% = $1.50 。
接下来 $7 - $3 = $4 的税率为 10% 。需要支付税款 $4 * 10% = $0.40 。
最后 $10 - $7 = $3 的税率为 25% 。需要支付税款 $3 * 25% = $0.75 。
需要支付的税款总计 $1.50 + $0.40 + $0.75 = $2.65 。
```

示例2：
```
输入：brackets = [[1,0],[4,25],[5,50]], income = 2
输出：0.25000
解释：
前 $1 的税率为 0% 。需要支付税款 $1 * 0% = $0 。
剩下 $1 的税率为 25% 。需要支付税款 $1 * 25% = $0.25 。
需要支付的税款总计 $0 + $0.25 = $0.25 。
```

示例3：
```
输入：brackets = [[2,50]], income = 0
输出：0.00000
解释：
没有收入，无需纳税，需要支付的税款总计 $0 。
```

提示：
- `1 <= brackets.length <= 100`
- `1 <= upperi <= 1000`
- `0 <= percenti <= 100`
- `0 <= income <= 1000`
- `upperi` 按递增顺序排列
- `upperi` 中的所有值 互不相同
- 最后一个税级的上限大于等于 `income`

## 分析和解答

理解下题意就可以，做一个差值的存储，这里差值的存储一开始搞错了，如果从前往后更新的话前边的变了后边的也就跟着变了，要用一个新的或者从后往前来；

```python
class Solution(object):
    def calculateTax(self, brackets, income):
        """
        :type brackets: List[List[int]]
        :type income: int
        :rtype: float
        """
        for i in range(len(brackets)-1, 0, -1):
            brackets[i][0] = brackets[i][0] - brackets[i-1][0]
    
        # print(brackets)
        res = 0
        i = 0
        for i in range(len(brackets)):
            if income >= brackets[i][0]:
                res +=  ((brackets[i][0] * brackets[i][1] * 1.0) / 100)
                income -= brackets[i][0]
                i += 1
            else:
                
                res +=  ((income * brackets[i][1] * 1.0) / 100)
                i += 1
                break
        return res
```


# 第二题：2304.网格中的最小路径代价【难度：1658】

[题目链接](https://leetcode.cn/problems/minimum-path-cost-in-a-grid/)

## 题目大意

给你一个下标从 **0** 开始的整数矩阵 `grid` ，矩阵大小为 `m x n` ，由从 `0` 到 `m * n - 1` 的不同整数组成。你可以在此矩阵中，从一个单元格移动到 **下一行** 的任何其他单元格。如果你位于单元格 `(x, y)` ，且满足 `x < m - 1` ，你可以移动到 `(x + 1, 0), (x + 1, 1), ..., (x + 1, n - 1)` 中的任何一个单元格。**注意**： 在最后一行中的单元格不能触发移动。

每次可能的移动都需要付出对应的代价，代价用一个下标从 **0** 开始的二维数组 `moveCost` 表示，该数组大小为 `(m * n) x n` ，其中 `moveCost[i][j]` 是从值为 `i` 的单元格移动到下一行第 `j` 列单元格的代价。从 `grid` 最后一行的单元格移动的代价可以忽略。

`grid` 一条路径的代价是：所有路径经过的单元格的 **值之和** 加上 所有移动的 **代价之和** 。从 **第一行** 任意单元格出发，返回到达 **最后一行** 任意单元格的最小路径代价。

示例1：

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-06-19-16-46-14.png)
```
输入：grid = [[5,3],[4,0],[2,1]], moveCost = [[9,8],[1,5],[10,12],[18,6],[2,4],[14,3]]
输出：17
解释：最小代价的路径是 5 -> 0 -> 1 。
- 路径途经单元格值之和 5 + 0 + 1 = 6 。
- 从 5 移动到 0 的代价为 3 。
- 从 0 移动到 1 的代价为 8 。
路径总代价为 6 + 3 + 8 = 17 。
```

示例2：
```
输入：grid = [[5,1,2],[4,0,3]], moveCost = [[12,10,15],[20,23,8],[21,7,1],[8,1,13],[9,10,25],[5,3,2]]
输出：6
解释：
最小代价的路径是 2 -> 3 。 
- 路径途经单元格值之和 2 + 3 = 5 。 
- 从 2 移动到 3 的代价为 1 。 
路径总代价为 5 + 1 = 6 。
```

提示：
- `m == grid.length`
- `n == grid[i].length`
- `2 <= m, n <= 50`
- `grid` 由从 `0` 到 `m * n - 1` 的不同整数组成
- `moveCost.length == m * n`
- `moveCost[i].length == n`
- `1 <= moveCost[i][j] <= 100`

## 分析和解答

看上去像是dfs但实际上是个dp，也算提醒自己很多dfs题目但是看起来数据超范围的了，就可以用dp的思路给干掉；

```python
class Solution(object):
    def minPathCost(self, grid, moveCost):
        """
        :type grid: List[List[int]]
        :type moveCost: List[List[int]]
        :rtype: int
        """

        m, n = len(grid), len(grid[0])
        base = grid[0]

        for i in range(1, m):  # 遍历m行
            new_base = [1e99 for _ in range(n)]  # n个
            # 下面是一个从当前n个状态（base，用j代表）到下n个状态（用k表示）的转移，从上一行的n个转移到下一行的n个，每个地方取最优的dp
            for j in range(n):
                for k in range(n):
                    new_base[k] = min(new_base[k], base[j]+grid[i][k]+moveCost[grid[i-1][j]][k])  # 选一个所有状态转移里最小的
            
            base = new_base
        
        return min(base)
```


# 第三题：2305.公平分发饼干【难度：1886】

[题目链接](https://leetcode.cn/problems/fair-distribution-of-cookies/)

## 题目大意

给你一个整数数组 `cookies` ，其中 `cookies[i]` 表示在第 `i` 个零食包中的饼干数量。另给你一个整数 `k` 表示等待分发零食包的孩子数量，**所有** 零食包都需要分发。在同一个零食包中的所有饼干都必须分发给同一个孩子，不能分开。

分发的 **不公平程度** 定义为单个孩子在分发过程中能够获得饼干的最大总数。

返回所有分发的最小不公平程度。

示例1：
```
输入：cookies = [8,15,10,20,8], k = 2
输出：31
解释：一种最优方案是 [8,15,8] 和 [10,20] 。
- 第 1 个孩子分到 [8,15,8] ，总计 8 + 15 + 8 = 31 块饼干。
- 第 2 个孩子分到 [10,20] ，总计 10 + 20 = 30 块饼干。
分发的不公平程度为 max(31,30) = 31 。
可以证明不存在不公平程度小于 31 的分发方案。
```

示例2：
```
输入：cookies = [6,1,3,2,2,4,1,2], k = 3
输出：7
解释：一种最优方案是 [6,1]、[3,2,2] 和 [4,1,2] 。
- 第 1 个孩子分到 [6,1] ，总计 6 + 1 = 7 块饼干。 
- 第 2 个孩子分到 [3,2,2] ，总计 3 + 2 + 2 = 7 块饼干。
- 第 3 个孩子分到 [4,1,2] ，总计 4 + 1 + 2 = 7 块饼干。
分发的不公平程度为 max(7,7,7) = 7 。
可以证明不存在不公平程度小于 7 的分发方案。
```

提示：
- `2 <= cookies.length <= 8`
- `1 <= cookies[i] <= 10^5`
- `2 <= k <= cookies.length`

## 分析和解答

暂时不记录这个题的正确解法了，这种题能想到搜索的思路怎么都还是觉得有点怪，有点没找到突破口，记录一个自己的k进制状态转移方法，虽然超时了但是能保证暴力对；

```python
# 超时解法27/36
class Solution(object):
    def distributeCookies(self, cookies, k):
        """
        :type cookies: List[int]
        :type k: int
        :rtype: int
        """
        res = 1e99
        def f(n, x, padding_len):
            #n为待转换的十进制数，x为机制，取值为2-16
            a=[0,1,2,3,4,5,6,7,8,9,'A','b','C','D','E','F']
            b=[]
            while True:
                s=n//x  # 商
                y=n%x  # 余数
                b=b+[y]
                if s==0:
                    break
                n=s
            b.reverse()
            tmp = []
            for i in b:
                tmp.append(a[i])
            return tmp + [0] * (padding_len - len(tmp))
        
        for i in range(k**len(cookies)):
            tmp = f(i, k, len(cookies))
            d = defaultdict(int)
            tmp_max = 0
            for i in range(len(tmp)):
                d[tmp[i]] += cookies[i]
            
            # print(d)
            
            if len(d) != k:
                continue
            else:
                tmp_max = max([x for x in d.values()])

            res = min(res, tmp_max)
        
        return res
```