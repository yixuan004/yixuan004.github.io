---
title: 277场LeetCode周赛
date: 2022-01-23 15:51:15
tags:
    - 数组
    - 排序
    - 双指针
    - 模拟
    - 哈希表
    - 计数
    - 位运算
    - 回溯
    - 枚举
    - EASY
    - MEDIUM
    - HARD
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

在大佬们的帮助下第一次周赛ak并进了前1000名还是很开心的hhh，前三个题是有史以来见过最水的周赛题，感觉全是easy难度的，最后一个题学习到了位运算的暴力遍历思路，而且还有一个题目特有的思想是只需要考虑说真话的人就行了，因为假的说话怎么都是有可能的，于是在假定所有说真话的人判断是否和条件有冲突就可以了。

另外突然感觉这次题目的前三题描述都好简单啊，喜欢这样的题，搞直接点行不行hhh

第一题：排个序后和最大最小比就行了，这个内容还是挺隐含的，如果能看出来是和最大最小比就很简单了；

第二题：自己使用的是比较简单的解法，因为是周赛就不在继续想更加复杂的解法了，拆成两个数组就行了；

第三题：mapping就能解决，这种题还是很好想到mapping（哈希表）的；

第四题：感谢大佬们的帮助hh，看到题目条件的数据范围只有15，所以可以2^15次方这个数量级的二进制暴力来做，需要注意的附加条件是只需要判断假定所有说真话的人是否和条件有冲突就可以了；

<!--more-->

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-01-24-00-09-08.png)

# 第一题：5989.元素计数

[题目链接](https://leetcode-cn.com/problems/count-elements-with-strictly-smaller-and-greater-elements/)

## 题目大意

给你一个整数数组 ```nums``` ，统计并返回在 ```nums``` 中同时具有一个严格较小元素和一个严格较大元素的元素数目。

示例1：
```
输入：nums = [11,7,2,15]
输出：2
解释：元素 7 ：严格较小元素是元素 2 ，严格较大元素是元素 11 。
元素 11 ：严格较小元素是元素 7 ，严格较大元素是元素 15 。
总计有 2 个元素都满足在 nums 中同时存在一个严格较小元素和一个严格较大元素。
```

示例2：
```
输入：nums = [-3,3,3,90]
输出：2
解释：元素 3 ：严格较小元素是元素 -3 ，严格较大元素是元素 90 。
由于有两个元素的值为 3 ，总计有 2 个元素都满足在 nums 中同时存在一个严格较小元素和一个严格较大元素。
```

提示：
- 1 <= nums.length <= 100
- -105 <= nums[i] <= 105

## 分析和解答

排个序后和最大最小比就行了，这个内容还是挺隐含的，如果能看出来是和最大最小比就很简单了
```python
class Solution(object):
    def countElements(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        cnt = 0
        nums.sort()
        for i in range(1, len(nums)-1):
            if nums[i] > nums[0] and nums[-1] > nums[i]:
                cnt += 1
        
        return cnt
```


# 第二题：5991.按符号重排数组

[题目链接](https://leetcode-cn.com/problems/rearrange-array-elements-by-sign/)

## 题目大意

给你一个下标从 **0** 开始的整数数组 ``nums`` ，数组长度为 **偶数** ，由数目相等的正整数和负整数组成。

你需要 **重排** ```nums``` 中的元素，使修改后的数组满足下述条件：

1. 任意 **连续** 的两个整数 **符号相反**
2. 对于符号相同的所有整数，**保留** 它们在 ```nums``` 中的 **顺序** 。
3. 重排后数组以正整数开头。

重排元素满足上述条件后，返回修改后的数组。

示例1：
```
输入：nums = [3,1,-2,-5,2,-4]
输出：[3,-2,1,-5,2,-4]
解释：
nums 中的正整数是 [3,1,2] ，负整数是 [-2,-5,-4] 。
重排的唯一可行方案是 [3,-2,1,-5,2,-4]，能满足所有条件。
像 [1,-2,2,-5,3,-4]、[3,1,2,-2,-5,-4]、[-2,3,-5,1,-4,2] 这样的其他方案是不正确的，因为不满足一个或者多个条件。 
```

示例2：
```
输入：nums = [-1,1]
输出：[1,-1]
解释：
1 是 nums 中唯一一个正整数，-1 是 nums 中唯一一个负整数。
所以 nums 重排为 [1,-1] 。
```

提示：
- ```2 <= nums.length <= 2 * 105```
- ```nums.length``` 是 **偶数**
- ```1 <= |nums[i]| <= 105```
- ```nums``` 由 **相等** 数量的正整数和负整数组成

## 分析和解答

自己使用的是比较简单的解法，因为是周赛就不在继续想更加复杂的解法了，拆成两个数组就行了

```python
class Solution(object):
    def rearrangeArray(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        result = []
        nums_positive = []
        nums_negative = []
        for i in range(len(nums)):
            if nums[i]  > 0:
                nums_positive.append(nums[i])
            else:
                nums_negative.append(nums[i])
        
        for i in range(len(nums_positive)):
            result.append(nums_positive[i])
            result.append(nums_negative[i])
        return result
```

# 第三题：5990.找出数组中的所有孤独数字

[题目链接](https://leetcode-cn.com/problems/find-all-lonely-numbers-in-the-array/)

## 题目大意

给你一个整数数组 ```nums``` 。如果数字 ```x``` 在数组中仅出现 **一次** ，且没有 **相邻** 数字（即，```x + 1``` 和 ```x - 1```）出现在数组中，则认为数字 ```x``` 是 **孤独数字** 。

返回 ```nums``` 中的 **所有** 孤独数字。你可以按 **任何顺序** 返回答案。

示例1：
```
输入：nums = [10,6,5,8]
输出：[10,8]
解释：
- 10 是一个孤独数字，因为它只出现一次，并且 9 和 11 没有在 nums 中出现。
- 8 是一个孤独数字，因为它只出现一次，并且 7 和 9 没有在 nums 中出现。
- 5 不是一个孤独数字，因为 6 出现在 nums 中，反之亦然。
因此，nums 中的孤独数字是 [10, 8] 。
注意，也可以返回 [8, 10] 。
```

示例2：
```
输入：nums = [1,3,5,3]
输出：[1,5]
解释：
- 1 是一个孤独数字，因为它只出现一次，并且 0 和 2 没有在 nums 中出现。
- 5 是一个孤独数字，因为它只出现一次，并且 4 和 6 没有在 nums 中出现。
- 3 不是一个孤独数字，因为它出现两次。
因此，nums 中的孤独数字是 [1, 5] 。
注意，也可以返回 [5, 1] 。
```

提示：
- ```1 <= nums.length <= 105```
- ```0 <= nums[i] <= 106```

## 分析和解答

mapping就能解决，这种题还是很好想到mapping（哈希表）的；也或许题目中的任何顺序暗示了可以排序和mapping做吧

```python
class Solution(object):
    def findLonely(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        result = []
        mappings = {}
        for i in range(len(nums)):
            if mappings.get(nums[i]) is None:
                mappings[nums[i]] = 1
            else:
                mappings[nums[i]] += 1
        
        for key, value in mappings.items():
            # 把孤独的加进来，用key
            if value < 2 and mappings.get(key-1) is None and mappings.get(key+1) is None:
                result.append(key)
        
        return result
```


# 第四题：5992.基于陈述统计最多好人数

[题目链接](https://leetcode-cn.com/problems/maximum-good-people-based-on-statements/)

## 题目大意

戏中存在两种角色：

- **好人**：该角色只说真话。
- **坏人**：该角色可能说真话，也可能说假话。

给你一个下标从 **0** 开始的二维整数数组 ```statements``` ，大小为 ```n x n``` ，表示 ```n``` 个玩家对彼此角色的陈述。具体来说，```statements[i][j]``` 可以是下述值之一：

- ```0``` 表示 ```i``` 的陈述认为 ```j``` 是 **坏人** 。
- ```1``` 表示 ```i``` 的陈述认为 ```j``` 是 **好人** 。
- ```2``` 表示 ```i``` 没有对 ```j``` 作出陈述。

另外，玩家不会对自己进行陈述。形式上，对所有 ```0 <= i < n``` ，都有 ```statements[i][i] = 2``` 。

根据这 ```n``` 个玩家的陈述，返回可以认为是 **好人** 的 **最大** 数目。

示例1：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-01-24-00-43-36.png)
```
输入：statements = [[2,1,2],[1,2,2],[2,0,2]]
输出：2
解释：每个人都做一条陈述。
- 0 认为 1 是好人。
- 1 认为 0 是好人。
- 2 认为 1 是坏人。
以 2 为突破点。
- 假设 2 是一个好人：
    - 基于 2 的陈述，1 是坏人。
    - 那么可以确认 1 是坏人，2 是好人。
    - 基于 1 的陈述，由于 1 是坏人，那么他在陈述时可能：
        - 说真话。在这种情况下会出现矛盾，所以假设无效。
        - 说假话。在这种情况下，0 也是坏人并且在陈述时说假话。
    - 在认为 2 是好人的情况下，这组玩家中只有一个好人。
- 假设 2 是一个坏人：
    - 基于 2 的陈述，由于 2 是坏人，那么他在陈述时可能：
        - 说真话。在这种情况下，0 和 1 都是坏人。
            - 在认为 2 是坏人但说真话的情况下，这组玩家中没有一个好人。
        - 说假话。在这种情况下，1 是好人。
            - 由于 1 是好人，0 也是好人。
            - 在认为 2 是坏人且说假话的情况下，这组玩家中有两个好人。
在最佳情况下，至多有两个好人，所以返回 2 。
注意，能得到此结论的方法不止一种。
```

示例2：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-01-24-00-43-46.png)
```
输入：statements = [[2,0],[0,2]]
输出：1
解释：每个人都做一条陈述。
- 0 认为 1 是坏人。
- 1 认为 0 是坏人。
以 0 为突破点。
- 假设 0 是一个好人：
    - 基于与 0 的陈述，1 是坏人并说假话。
    - 在认为 0 是好人的情况下，这组玩家中只有一个好人。
- 假设 0 是一个坏人：
    - 基于 0 的陈述，由于 0 是坏人，那么他在陈述时可能：
        - 说真话。在这种情况下，0 和 1 都是坏人。
            - 在认为 0 是坏人但说真话的情况下，这组玩家中没有一个好人。
        - 说假话。在这种情况下，1 是好人。
            - 在认为 0 是坏人且说假话的情况下，这组玩家中只有一个好人。
在最佳情况下，至多有一个好人，所以返回 1 。 
注意，能得到此结论的方法不止一种。
```

提示：
- ```n == statements.length == statements[i].length```
- ```2 <= n <= 15```
- ```statements[i][j]``` 的值为 ```0```、```1``` 或 ```2```
- ```statements[i][i] == 2```

## 分析和解答

感谢大佬们的帮助hh，看到题目条件的数据范围只有15，所以可以2^15次方这个数量级的二进制暴力来做，需要注意的附加条件是只需要判断假定所有说真话的人是否和条件有冲突就可以了

```python
class Solution(object):
    def maximumGood(self, statements):
        """
        :type statements: List[List[int]]
        :rtype: int
        """
        max_good_people = 0
        n = len(statements)
        
        def judge(state, statements):
            for i in range(len(state)):
                if state[i] == 1:  # 只判断是好人的情况
                    for j in range(n):
                        if i == j:
                            continue
                        if statements[i][j] == 2:
                            continue
                        if statements[i][j] != state[j]:
                            return False
            return True

        
        # 学到了，2^n种状态，用位运算遍历
        for i in range((2 ** n) - 1, -1, -1):
            now_good = 0
            tmp = i
            state = [0 for i in range(n)]
            cnt = 0
            while tmp > 0:
                state[cnt] = tmp & 1
                if state[cnt] == 1:
                    now_good += 1
                tmp >>= 1
                cnt += 1
            flag = judge(state, statements)
            if flag:
                max_good_people = max(max_good_people, now_good)
        
        return max_good_people
```