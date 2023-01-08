---
title: 70场LeetCode双周赛
date: 2022-01-23 00:33:04
tags:
    - 
    - EASY
    - MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

不常做双周赛的题目hhh，这次的bfs走迷宫一类的题目还是让人印象很深的，简单的dfs bfs模板还是要熟练些，这种题还是比全排列的那种深度优先更让人好理解一些；

第一题：贪心就可以，排个序后每买两个不买一个；

第二题：这个隐藏数组题目在时间复杂度上还是被坑了一下的，如果不看示例自己在TLE后能不能想到还是个问题，对于首位序列lower upper的问题，从示例总结出来的“对齐”思路很关键；

第三题：一个标准的bfs题目模板，题目给的排序约束条件既可以按照自己的方法先都走完后再拿出来排，也可能可以作为优先队列的约束条件，还要注意题目的step其实暗示了bfs而不是dfs；

第四题：咕咕咕

<!--more-->

# 第一题：5971.打折购买糖果的最小开销

[题目链接](https://leetcode-cn.com/problems/minimum-cost-of-buying-candies-with-discount/)

## 题目大意

一家商店正在打折销售糖果。每购买 **两个** 糖果，商店会 **免费** 送一个糖果。

免费送的糖果唯一的限制是：它的价格需要小于等于购买的两个糖果价格的 **较小值** 。

- 比方说，总共有```4```个糖果，价格分别为```1```，```2```，```3```和```4```，一位顾客买了价格为```2```和```3```的糖果，那么他可以免费获得价格为```1```的糖果，但不能获得价格为```4```的糖果。

给你一个下标从 **0** 开始的整数数组```cost```，其中```cost[i]```表示第```i```个糖果的价格，请你返回获得 **所有** 糖果的 **最小** 总开销。

示例1：
```
输入：cost = [1,2,3]
输出：5
解释：我们购买价格为 2 和 3 的糖果，然后免费获得价格为 1 的糖果。
总开销为 2 + 3 = 5 。这是开销最小的 唯一 方案。
注意，我们不能购买价格为 1 和 3 的糖果，并免费获得价格为 2 的糖果。
这是因为免费糖果的价格必须小于等于购买的 2 个糖果价格的较小值。
```

示例2：
```
输入：cost = [6,5,7,9,2,2]
输出：23
解释：最小总开销购买糖果方案为：
- 购买价格为 9 和 7 的糖果
- 免费获得价格为 6 的糖果
- 购买价格为 5 和 2 的糖果
- 免费获得价格为 2 的最后一个糖果
因此，最小总开销为 9 + 7 + 5 + 2 = 23 。
```

示例3：
```
输入：cost = [5,5]
输出：10
解释：由于只有 2 个糖果，我们需要将它们都购买，而且没有免费糖果。
所以总最小开销为 5 + 5 = 10 。
```

提示：
- ```1 <= cost.length <= 100```
- ```1 <= cost[i] <= 100```

## 分析和解答

贪！ 排序后贪心就可以了，记得写的时候对于这个continue的位置还纠结了下，不过复盘来说应该会有不少种写法的

```python
class Solution(object):
    def minimumCost(self, cost):
        """
        :type cost: List[int]
        :rtype: int
        """
        cost = sorted(cost, key=lambda x: -x)
        cnt = 0
        result = 0
        for i in range(len(cost)):
            if cnt != 0 and cnt % 2 == 0:
                cnt = 0
                continue
            result += cost[i]
            cnt += 1
        return result
```


# 第二题：5972.统计隐藏数组数目

[题目链接](https://leetcode-cn.com/problems/count-the-hidden-sequences/)

## 题目大意

给你一个下标从 **0** 开始且长度为 ```n``` 的整数数组 ```differences``` ，它表示一个长度为 ```n + 1``` 的 **隐藏** 数组 **相邻** 元素之间的 **差值** 。更正式的表述为：我们将隐藏数组记作 ```hidden``` ，那么 ```differences[i] = hidden[i + 1] - hidden[i]``` 。

同时给你两个整数 ```lower``` 和 ```upper``` ，它们表示隐藏数组中所有数字的值都在 闭 区间 ```[lower, upper]``` 之间。

- 比方说，```differences = [1, -3, 4]``` ，```lower = 1``` ，```upper = 6``` ，那么隐藏数组是一个长度为 4 且所有值都在 1 和 6 （包含两者）之间的数组。
    - ```[3, 4, 1, 5]``` 和 ```[4, 5, 2, 6]``` 都是符合要求的隐藏数组。
    - ```[5, 6, 3, 7]``` 不符合要求，因为它包含大于 6 的元素。
    - ```[1, 2, 3, 4]``` 不符合要求，因为相邻元素的差值不符合给定数据。

请你返回 **符合** 要求的隐藏数组的数目。如果没有符合要求的隐藏数组，请返回 0 。

示例1：
```
输入：differences = [1,-3,4], lower = 1, upper = 6
输出：2
解释：符合要求的隐藏数组为：
- [3, 4, 1, 5]
- [4, 5, 2, 6]
所以返回 2 。
```

示例2：
```
输入：differences = [3,-4,5,1,-2], lower = -4, upper = 5
输出：4
解释：符合要求的隐藏数组为：
- [-3, 0, -4, 1, 2, 0]
- [-2, 1, -3, 2, 3, 1]
- [-1, 2, -2, 3, 4, 2]
- [0, 3, -1, 4, 5, 3]
所以返回 4 。
```

示例3：
```
输入：differences = [4,-7,2], lower = 3, upper = 6
输出：0
解释：没有符合要求的隐藏数组，所以返回 0 。
```

提示：
- n == differences.length
- 1 <= n <= 105
- -105 <= differences[i] <= 105
- -105 <= lower <= upper <= 105

## 分析和解答

这个题开始想到的是一种从lower开始全部遍历的策略，但是很容易的就超时了，后来从示例2发现可以使用一种“数学”方法，复盘认为最关键的内容就是要使用一种“对齐”的思想，这样就避免了把两边的情况都考虑上，只考虑一边就可以了。

```python
class Solution(object):
    def numberOfArrays(self, differences, lower, upper):
        """
        :type differences: List[int]
        :type lower: int
        :type upper: int
        :rtype: int
        """
        
        """
        # 看了一个示例后，感觉只算一遍，然后算一个max low就可以了吧
        result = 0
        for start_idx in range(lower, upper+1):
            tmp_start_idx = start_idx
            flag = True
            for i in range(len(differences)):
                tmp_start_idx += differences[i]
                if not (tmp_start_idx <= upper and tmp_start_idx >= lower):
                    flag = False 
                    break
            if flag:
                result += 1
        
        return result
        """
        result = 0
        tmp_start_idx = lower
        seq_max = lower
        seq_min = lower
        for i in range(len(differences)):
            tmp_start_idx += differences[i]
            seq_max = max(seq_max, tmp_start_idx)
            seq_min = min(seq_min, tmp_start_idx)
#         print("seq_max: ", seq_max)
#         print("seq_min: ", seq_min)

        d1 = upper - seq_max
        d2 = seq_min - lower
        
        # 3种情况
        seq_max = seq_max + (lower - seq_min)
        seq_min = lower
        
        # print("seq_max: ", seq_max)
        # print("seq_min: ", seq_min)
        
        return max(0, upper - seq_max + 1)
```


# 第三题：5973.价格范围内最高排名的 K 样物品

[题目链接](https://leetcode-cn.com/problems/k-highest-ranked-items-within-a-price-range/)

## 题目大意

给你一个下标从 **0** 开始的二维整数数组 ```grid``` ，它的大小为 ```m x n``` ，表示一个商店中物品的分布图。数组中的整数含义为：

- ```0``` 表示无法穿越的一堵墙。
- ```1``` 表示可以自由通过的一个空格子。
- 所有其他正整数表示该格子内的一样物品的价格。你可以自由经过这些格子。

从一个格子走到上下左右相邻格子花费 ```1``` 步。

同时给你一个整数数组 ```pricing``` 和 ```start``` ，其中 ```pricing = [low, high]``` 且 ```start = [row, col]``` ，表示你开始位置为 ```(row, col)``` ，同时你只对物品价格在 闭区间 ```[low, high]``` 之内的物品感兴趣。同时给你一个整数 ```k``` 。

你想知道给定范围 **内** 且 **排名最高** 的 ```k``` 件物品的 **位置** 。排名按照优先级从高到低的以下规则制定：

1. 距离：定义为从 ```start``` 到一件物品的最短路径需要的步数（**较近** 距离的排名更高）。
2. 价格：**较低** 价格的物品有更高优先级，但只考虑在给定范围之内的价格。
3. 行坐标：**较小** 行坐标的有更高优先级。
4. 列坐标：**较小** 列坐标的有更高优先级。

请你返回给定价格内排名最高的 ```k``` 件物品的坐标，将它们按照排名排序后返回。如果给定价格内少于 ```k``` 件物品，那么请将它们的坐标 **全部** 返回。

## 分析和解答

bfs的一个基础模板题，在写的时候先用了dfs结果超时了。复盘分析为什么使用bfs的原因，bfs可以保证每个地方step是一定递增1的，而不像dfs可能一个位置有多种走法。

后来看题解来说这个题实际上背后可能隐藏的是优先队列的思路，但是这块的内容确实还不太熟悉，什么时候先复习下大根堆小根堆的内容吧，滑动窗口那道hard题

### bfs正确题解代码49/49
```python
class Solution(object):
    def highestRankedKItems(self, grid, pricing, start, k):
        """
        :type grid: List[List[int]]
        :type pricing: List[int]
        :type start: List[int]
        :type k: int
        :rtype: List[List[int]]
        """
        # 过一个异常处理
        if len(grid) == 0:
            return []
        
        # 一眼看过去很像dfs啊，d就完事了，会不会超时之后再说了
        m = len(grid)
        n = len(grid[0])
        vis = [[0 for _ in range(n)] for _ in range(m)]
        dx = [0, 1, 0, -1]
        dy = [1, 0, -1, 0]
        
        x = start[0]
        y = start[1]
        vis[x][y] = 1
        bfs_queue = []
        bfs_queue.append([x, y, 0, grid[x][y]])
        result = []
        
        # 使用bfs的思路
        while len(bfs_queue) != 0:
            # 取队头并pop
            now_x, now_y, now_step, now_value = bfs_queue[0][0], bfs_queue[0][1], bfs_queue[0][2], bfs_queue[0][3]
            if now_value >= pricing[0] and now_value <= pricing[1]:
                result.append(bfs_queue[0])
            bfs_queue = bfs_queue[1:]
            
            # 把四个方向满足条件的加入到队列中
            for i in range(4):
                nx = now_x + dx[i]
                ny = now_y + dy[i]
                if nx >= 0 and nx < m and ny >= 0 and ny < n and grid[nx][ny] != 0 and vis[nx][ny] == 0:
                    vis[nx][ny] = 1
                    bfs_queue.append([nx, ny, now_step+1, grid[nx][ny]])
        
        
        # dfs(x, y, 0)
        result.sort(key=lambda x: (x[2], x[3], x[0], x[1]))
        if len(result) > k:
            return [[result[x][0], result[x][1]] for x in range(k)]
        else:
            return [[x[0], x[1]] for x in result]         
```

### dfs超时版本题解代码15/49
```python
class Solution(object):
    def highestRankedKItems(self, grid, pricing, start, k):
        """
        :type grid: List[List[int]]
        :type pricing: List[int]
        :type start: List[int]
        :type k: int
        :rtype: List[List[int]]
        """
        # 过一个异常处理
        if len(grid) == 0:
            return []
        
        # 一眼看过去很像dfs啊，d就完事了，会不会超时之后再说了
        m = len(grid)
        n = len(grid[0])
        vis = [[0 for _ in range(n)] for _ in range(m)]
        dx = [0, 1, 0, -1]
        dy = [1, 0, -1, 0]
        
        x = start[0]
        y = start[1]
        vis[x][y] = 1
        
        result = []
        mappings = {}
        
        def dfs(x, y, step):
            if grid[x][y] <= pricing[1] and grid[x][y] >= pricing[0]:
                if mappings.get((x, y)) is None:
                    mappings[(x, y)] = [step, grid[x][y]]
                else:
                    tmp_list = mappings[(x, y)]
                    if step < tmp_list[0]:
                        mappings[(x, y)] = [step, grid[x][y]]
                
            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]
                if nx >= 0 and nx < m and ny >=0 and ny < n and grid[nx][ny] != 0 and vis[nx][ny] == 0:
                    vis[nx][ny] = 1
                    dfs(nx, ny, step+1)
                    vis[nx][ny] = 0
        
        dfs(x, y, 0)
        
        for key, value in mappings.items():            
            result.append([key[0], key[1], value[0], value[1]])
        
        
        result.sort(key=lambda x: (x[2], x[3], x[0], x[1]))
        
        # print(result)
        
        if len(result) > k:
            return [[result[x][0], result[x][1]] for x in range(k)]
        else:
            return [[x[0], x[1]] for x in result]
```