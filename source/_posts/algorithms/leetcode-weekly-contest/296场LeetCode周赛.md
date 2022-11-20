---
title: 296场LeetCode周赛
date: 2022-06-19 16:56:36
tags:
    - 数组
    - 模拟
    - 贪心
    - 排序
    - 哈希表
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

题目难度：t1(1241)+t2(1416)+t3(1445)；

难度非常小的一周周赛，半个小时做了三个题居然排名都到1716去了，第四个题还是日常咕咕了。对于前三个题的话没有什么太过于特殊的思路，基本按照题目要求模拟/贪心就可以完成了；

第一题：按照题目要求模拟即可，注意在这里使用了while 1 + 条件break的那种循环；

第二题：排序后贪心，维护一个组内的最大最小值；

第三题：题目加上了很多特殊的限制，限制了一些比较极端情况的出现，就显得比较人性化；

第四题：咕咕QAQ；

<!--more-->

# 第一题：2293.极大极小游戏【难度：1241】

[题目链接](https://leetcode.cn/problems/min-max-game/)

## 题目大意

给你一个下标从 **0** 开始的整数数组 `nums` ，其长度是 `2` 的幂。

对 `nums` 执行下述算法：
1. 设 `n` 等于 `nums` 的长度，如果 `n == 1` ，**终止** 算法过程。否则，**创建** 一个新的整数数组 `newNums` ，新数组长度为 `n / 2` ，下标从 **0** 开始。
2. 对于满足 `0 <= i < n / 2` 的每个 **偶数** 下标 `i` ，将 `newNums[i]` **赋值** 为 `min(nums[2 * i], nums[2 * i + 1])` 。
3. 对于满足 `0 <= i < n / 2` 的每个 **奇数** 下标 `i` ，将 `newNums[i]` **赋值** 为 `max(nums[2 * i], nums[2 * i + 1])` 。
4. 用 `newNums` 替换 `nums` 。
5. 从步骤 1 开始 **重复** 整个过程。

执行算法后，返回 `nums` 中剩下的那个数字。

示例1：

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-06-19-17-05-41.png)
```
输入：nums = [1,3,5,2,4,8,2,2]
输出：1
解释：重复执行算法会得到下述数组。
第一轮：nums = [1,5,4,2]
第二轮：nums = [1,4]
第三轮：nums = [1]
1 是最后剩下的那个数字，返回 1 。
```

示例2：
```
输入：nums = [3]
输出：3
解释：3 就是最后剩下的数字，返回 3 。
```

提示：
- `1 <= nums.length <= 1024`
- `1 <= nums[i] <= 10^9`
- `nums.length` 是 `2` 的幂

## 分析和解答

按照题目要求模拟即可，注意在这里使用了while 1 + 条件break的那种循环；

```python
class Solution(object):
    def minMaxGame(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if len(nums) == 1:
            return nums[0]
        
        while 1:
            tmp = []
            idx = 0
            for i in range(0, len(nums), 2):
                if idx % 2 == 0:
                    tmp.append(min(nums[i], nums[i+1]))
                    idx += 1
                else:
                    tmp.append(max(nums[i], nums[i+1]))
                    idx += 1
            # print(tmp)
            nums = copy.deepcopy(tmp)
            if len(tmp) == 1:
                break
        
        # print(tmp)
        return tmp[0]
```


# 第二题：2294.划分数组使最大差为 K【难度：1416】

[题目链接](https://leetcode.cn/problems/partition-array-such-that-maximum-difference-is-k/)

## 题目大意

给你一个整数数组 `nums` 和一个整数 `k` 。你可以将 `nums` 划分成一个或多个 **子序列** ，使 `nums` 中的每个元素都 **恰好** 出现在一个子序列中。

在满足每个子序列中最大值和最小值之间的差值最多为 `k` 的前提下，返回需要划分的 **最少** 子序列数目。

**子序列** 本质是一个序列，可以通过删除另一个序列中的某些元素（或者不删除）但不改变剩下元素的顺序得到。

示例1：
```
输入：nums = [3,6,1,2,5], k = 2
输出：2
解释：
可以将 nums 划分为两个子序列 [3,1,2] 和 [6,5] 。
第一个子序列中最大值和最小值的差值是 3 - 1 = 2 。
第二个子序列中最大值和最小值的差值是 6 - 5 = 1 。
由于创建了两个子序列，返回 2 。可以证明需要划分的最少子序列数目就是 2 。
```

示例2：
```
输入：nums = [1,2,3], k = 1
输出：2
解释：
可以将 nums 划分为两个子序列 [1,2] 和 [3] 。
第一个子序列中最大值和最小值的差值是 2 - 1 = 1 。
第二个子序列中最大值和最小值的差值是 3 - 3 = 0 。
由于创建了两个子序列，返回 2 。注意，另一种最优解法是将 nums 划分成子序列 [1] 和 [2,3] 。
```

示例3：
```
输入：nums = [2,2,4,5], k = 0
输出：3
解释：
可以将 nums 划分为三个子序列 [2,2]、[4] 和 [5] 。
第一个子序列中最大值和最小值的差值是 2 - 2 = 0 。
第二个子序列中最大值和最小值的差值是 4 - 4 = 0 。
第三个子序列中最大值和最小值的差值是 5 - 5 = 0 。
由于创建了三个子序列，返回 3 。可以证明需要划分的最少子序列数目就是 3 。
```

提示：
- `1 <= nums.length <= 10^5`
- `0 <= nums[i] <= 10^5`
- `0 <= k <= 10^5`

## 分析和解答

排序后贪心，维护一个组内的最大最小值；

```python
class Solution(object):
    def partitionArray(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        nums.sort()
        res = []
 
        now_min = nums[0]
        now_max = nums[0]
        tmp = [nums[0]]
        
        for i in range(1, len(nums)):
            now_min = min(now_min, nums[i])
            now_max = max(now_max, nums[i])
            chazhi = abs(now_max-now_min)
            
            if chazhi <= k:
                tmp.append(nums[i])
            else:
                res.append(tmp)
                tmp = [nums[i]]
                now_min = nums[i]
                now_max = nums[i]
        
        res.append(tmp)
        # print(res)
        
        return len(res)
```


# 第三题：2295.替换数组中的元素【难度：1445】

[题目链接](https://leetcode.cn/problems/replace-elements-in-an-array/)

## 题目大意

给你一个下标从 **0** 开始的数组 `nums` ，它包含 `n` 个 **互不相同** 的正整数。请你对这个数组执行 `m` 个操作，在第 `i` 个操作中，你需要将数字 `operations[i][0]` 替换成 `operations[i][1]` 。

题目保证在第 `i` 个操作中：
- `operations[i][0]` 在 `nums` 中存在。
- `operations[i][1]` 在 `nums` 中不存在。

请你返回执行完所有操作后的数组。

示例1：
```
输入：nums = [1,2,4,6], operations = [[1,3],[4,7],[6,1]]
输出：[3,2,7,1]
解释：我们对 nums 执行以下操作：
- 将数字 1 替换为 3 。nums 变为 [3,2,4,6] 。
- 将数字 4 替换为 7 。nums 变为 [3,2,7,6] 。
- 将数字 6 替换为 1 。nums 变为 [3,2,7,1] 。
返回最终数组 [3,2,7,1] 。
```

示例2：
```
输入：nums = [1,2], operations = [[1,3],[2,1],[3,2]]
输出：[2,1]
解释：我们对 nums 执行以下操作：
- 将数字 1 替换为 3 。nums 变为 [3,2] 。
- 将数字 2 替换为 1 。nums 变为 [3,1] 。
- 将数字 3 替换为 2 。nums 变为 [2,1] 。
返回最终数组 [2,1] 。
```

提示：
- `n == nums.length`
- `m == operations.length`
- `1 <= n, m <= 10^5`
- `nums` 中所有数字 **互不相同** 。
- `operations[i].length == 2`
- `1 <= nums[i], operations[i][0], operations[i][1] <= 10^6`
- 在执行第 `i` 个操作时，`operations[i][0]` 在 `nums` 中存在。
- 在执行第 `i` 个操作时，`operations[i][1]` 在 `nums` 中不存在。

## 分析和解答

题目加上了很多特殊的限制，限制了一些比较极端情况的出现，就显得比较人性化；

```python
class Solution(object):
    def arrayChange(self, nums, operations):
        """
        :type nums: List[int]
        :type operations: List[List[int]]
        :rtype: List[int]
        """
        
        d = {}
        for i in range(len(nums)):
            d[nums[i]] = [nums[i], nums[i], i]
        
        # print(d)
        
        for i in range(len(operations)):
            old, new = operations[i][0], operations[i][1]
            tmp_list = d[old]
            
            d[old] = [new, tmp_list[1], tmp_list[2]]
            d[new] = d[old]
            del d[old]
        
        tmp_res = []
        for key, value in d.items():
            tmp_res.append([value[0], value[2]])
        tmp_res.sort(key=lambda x: x[1])
        # print(tmp_res)
        
        res = []
        for i in range(len(tmp_res)):
            res.append(tmp_res[i][0])
        return res
```