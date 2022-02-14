---
title: 280场LeetCode周赛
date: 2022-02-14 01:58:59
tags:
    - 
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

这周做的状态和水平感觉都不是很好，第一个题做下去的时候就感觉不是很顺，第二个题直接就卡住了，感觉还是把第二题想的太简单或者说样例分析不太够？第三题直接没来得及看，最后复盘的时候感觉比赛有规定的时间内也不一定好想，和之前某一周ABCDE的前后缀和有点像，但是简单了一些；

第一题：一种模拟操作的感觉就行了？开始还以为要一些数学的操作，这种题如果告诉难度的话可能比较简单，但是如果不知道难度比较怕想到数学题上边去；

第二题：这个题当时做的时候直接就卡死了，卡了可以说一个多小时，还是样例可能误导了一种的感觉，最少的修改数目也就是奇偶位要拆开，在奇数位偶数位各选一个最大的数字作为统一就可以了！注意这两个数字在角标上不一定是连续的，被这个地方坑了好久，不用连续只要几乎选一个最大的，然后稍微做一些分类讨论就可以了；

第三题：一种前缀和，或者说**反向思路**的感觉，算出来最多能留多少个，也就是最少能拿出来多少个了，还是很牛的；

第四题：鸽！

<!--more-->


# 第一题：2169.得到0的操作数

[题目链接](https://leetcode-cn.com/problems/count-operations-to-obtain-zero/)

## 题目大意

给你两个 **非负** 整数 ```num1``` 和 ```num2``` 。

每一步 **操作** 中，如果 ```num1 >= num2``` ，你必须用 ```num1``` 减 ```num2``` ；否则，你必须用 ```num2``` 减 ```num1``` 。

- 例如， ```num1 = 5``` 且 ```num2 = 4``` ，应该用 ```num1``` 减 ```num2``` ，因此，得到 ```num1 = 1``` 和 ```num2 = 4``` 。然而，如果 ```num1 = 4``` 且 ```num2 = 5``` ，一步操作后，得到 ```num1 = 4``` 和 ```num2 = 1``` 。

返回使 ```num1 = 0``` 或 ```num2 = 0``` 的 **操作数** 。

示例1：
```
输入：num1 = 2, num2 = 3
输出：3
解释：
- 操作 1 ：num1 = 2 ，num2 = 3 。由于 num1 < num2 ，num2 减 num1 得到 num1 = 2 ，num2 = 3 - 2 = 1 。
- 操作 2 ：num1 = 2 ，num2 = 1 。由于 num1 > num2 ，num1 减 num2 。
- 操作 3 ：num1 = 1 ，num2 = 1 。由于 num1 == num2 ，num1 减 num2 。
此时 num1 = 0 ，num2 = 1 。由于 num1 == 0 ，不需要再执行任何操作。
所以总操作数是 3 。
```

示例2：
```
输入：num1 = 10, num2 = 10
输出：1
解释：
- 操作 1 ：num1 = 10 ，num2 = 10 。由于 num1 == num2 ，num1 减 num2 得到 num1 = 10 - 10 = 0 。
此时 num1 = 0 ，num2 = 10 。由于 num1 == 0 ，不需要再执行任何操作。
所以总操作数是 1 。
```

提示：
- ```0 <= num1, num2 <= 105```

## 分析和解答

这个题开始还以为需要一些数学的操作，没想到直接循环模拟就行了，注意python的两个数值交换可以使用如下代码直接交换

```python
a, b = b, a
```

看了一些题解说是可以用辗转相除法，不禁想到信安数学里学过为数不多的数论知识都还给老师了哈哈哈

```python
class Solution(object):
    def countOperations(self, num1, num2):
        """
        :type num1: int
        :type num2: int
        :rtype: int
        """
        cnt = 0
        while True:
            if num1 == 0 or num2 == 0:
                break
            if num1 < num2:
                num1, num2 = num2, num1
            if num1 >= num2:
                num1 = num1 - num2
            cnt += 1
            # print(num1, num2)
        return cnt
```


# 第二题：2170.使数组变成交替数组的最少操作数

[题目链接](https://leetcode-cn.com/problems/minimum-operations-to-make-the-array-alternating/)

## 题目大意

给你一个下标从 **0** 开始的数组 ```nums``` ，该数组由 ```n``` 个正整数组成。

如果满足下述条件，则数组 nums 是一个 **交替数组** ：

- ```nums[i - 2] == nums[i]``` ，其中 ```2 <= i <= n - ``` 。
- ```nums[i - 1] != nums[i]``` ，其中 ```1 <= i <= n - 1``` 。

在一步 **操作** 中，你可以选择下标 ```i``` 并将 ```nums[i]``` **更改** 为 **任一** 正整数。

返回使数组变成交替数组的 **最少操作数** 。

示例1：
```
输入：nums = [3,1,3,2,4,3]
输出：3
解释：
使数组变成交替数组的方法之一是将该数组转换为 [3,1,3,1,3,1] 。
在这种情况下，操作数为 3 。
可以证明，操作数少于 3 的情况下，无法使数组变成交替数组。
```

示例2：
```
输入：nums = [1,2,2,2,2]
输出：2
解释：
使数组变成交替数组的方法之一是将该数组转换为 [1,2,1,2,1].
在这种情况下，操作数为 2 。
注意，数组不能转换成 [2,2,2,2,2] 。因为在这种情况下，nums[0] == nums[1]，不满足交替数组的条件。
```

提示：
- ```1 <= nums.length <= 105```
- ```1 <= nums[i] <= 105```


## 分析和解答

这个题感觉还是个很考思路的题，一旦有一些理解错或者路走歪了就容易做不出来了。

最少的修改数目也就是奇偶位要拆开，在奇数位偶数位各选一个最大的数字作为统一就可以了！注意这两个数字在角标上不一定是连续的，被这个地方坑了好久，不用连续只要几乎选一个最大的，然后稍微做一些分类讨论就可以了

```python
class Solution(object):
    def minimumOperations(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        
        if len(nums) == 1:
            return 0
        
        oushu = [nums[i] for i in range(0, len(nums), 2)]
        jishu = [nums[i] for i in range(1, len(nums), 2)]
        oushu_counter = Counter(oushu)
        jishu_counter = Counter(jishu)
        
        
        oushu_counter_value = sorted(oushu_counter.items(), key=lambda x: -x[1])
        jishu_counter_value = sorted(jishu_counter.items(), key=lambda x: -x[1])
        
        # 三种情况
        if oushu_counter_value[0][0] != jishu_counter_value[0][0]:
            max_num = oushu_counter_value[0][1] + jishu_counter_value[0][1]
        else:
            
            if len(oushu_counter_value) < 2:
                oushu_counter_value.append((0, 0))
             
            if len(jishu_counter_value) < 2:
                jishu_counter_value.append((0, 0))
            
            
            max_num = max(oushu_counter_value[1][1] + jishu_counter_value[0][1], oushu_counter_value[0][1] + jishu_counter_value[1][1])
        
        return len(nums) - max_num
```


# 第三题：2171.拿出最少数目的魔法豆

[题目链接](https://leetcode-cn.com/problems/removing-minimum-number-of-magic-beans/comments/)

## 题目大意

给你一个 正 整数数组 ```beans``` ，其中每个整数表示一个袋子里装的魔法豆的数目。

请你从每个袋子中 **拿出** 一些豆子（也可以 **不拿出** ），使得剩下的 **非空** 袋子中（即 **至少** 还有 一颗 魔法豆的袋子）魔法豆的数目 **相等** 。一旦魔法豆从袋子中取出，你不能将它放到任何其他的袋子中。

请你返回你需要拿出魔法豆的 **最少数目**。

示例1：
```
输入：beans = [4,1,6,5]
输出：4
解释：
- 我们从有 1 个魔法豆的袋子中拿出 1 颗魔法豆。
  剩下袋子中魔法豆的数目为：[4,0,6,5]
- 然后我们从有 6 个魔法豆的袋子中拿出 2 个魔法豆。
  剩下袋子中魔法豆的数目为：[4,0,4,5]
- 然后我们从有 5 个魔法豆的袋子中拿出 1 个魔法豆。
  剩下袋子中魔法豆的数目为：[4,0,4,4]
总共拿出了 1 + 2 + 1 = 4 个魔法豆，剩下非空袋子中魔法豆的数目相等。
没有比取出 4 个魔法豆更少的方案。
```

示例2：
```
输入：beans = [2,10,3,2]
输出：7
解释：
- 我们从有 2 个魔法豆的其中一个袋子中拿出 2 个魔法豆。
  剩下袋子中魔法豆的数目为：[0,10,3,2]
- 然后我们从另一个有 2 个魔法豆的袋子中拿出 2 个魔法豆。
  剩下袋子中魔法豆的数目为：[0,10,3,0]
- 然后我们从有 3 个魔法豆的袋子中拿出 3 个魔法豆。
  剩下袋子中魔法豆的数目为：[0,10,0,0]
总共拿出了 2 + 2 + 3 = 7 个魔法豆，剩下非空袋子中魔法豆的数目相等。
没有比取出 7 个魔法豆更少的方案。
```

提示：
- ```1 <= beans.length <= 105```
- ```1 <= beans[i] <= 105```

## 分析和解答

一眼看过去这种题目肯定是要排序的，后边发现只要排序+枚举就可以了，有一种反向思路的感觉

一种前缀和，或者说**反向思路**的感觉，算出来最多能留多少个，也就是最少能拿出来多少个了，还是很牛的

```python
class Solution(object):
    def minimumRemoval(self, beans):
        """
        :type beans: List[int]
        :rtype: int
        """
        
        # 第i个地方最多保留(n-i) * beans[i]个
        
        beans.sort()
        
        total = 0
        max_save = 0
        for i in range(len(beans)):
            total += beans[i]
            max_save = max(max_save, (len(beans)-i) * beans[i])
        
        return total - max_save
```