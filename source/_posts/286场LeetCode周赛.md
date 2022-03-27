---
title: 286场LeetCode周赛
date: 2022-03-27 21:37:13
tags:
    - 哈希表
    - 贪心
    - 动态规划
    - 数学
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

这周周赛久违的在常规时间里做出三题了，感觉还是要先仔细读题然后多想一想常用的数据结构和思路吧，像这次的第二题就很像之前有一个“模板匹配”的题目，主要需要反向思维一下，把删除变成添加；另外这次周赛感觉又在面向答案编程了，自己的思考总是不太全面就开始做题，中间各种边界情况和极端情况就会忽略，笔试比较好用但是面试就不好说了，还是要加强下自己的一次AC准确率！

第一题：比较简单，但是又忘记了 ```set``` 是可以直接相减的了，还用了个 ```Counter``` 搞来搞去整了半天，有点过于耗费时间了；

第二题：看过去感觉就是个贪心思路的题，没有太验证过就开始写了，在调试了一些case后最后还是过了，自己搞这种题目的话如果这样就会要么过，要么就推倒重来，其实还是应该稍微合理性验证下的！很像之前有一个“模板匹配”的题目，主要需要反向思维一下，把删除变成添加，然后贪心就可以！

第三题：数学算角标的题目，如果是多年前刚刚接触编程的时候估计找到规律后也算不太对，但现在只要手动推导出来规律后实现起来应该几乎差不多了。这次多少还是有点面向答案编程的感觉，出一个badcase调试一组出一个badcase调试一组，这个是个比较坏的习惯。整体来说的话，这个题的思路就是算一半！比如要是四位数（偶数）的话，就是前一半先写 ```10 11 12 13``` ，这样二位数往上递增的，后一半直接把这个反向过来拼接就行，其实也是个隐含的贪心思路，就等于从小往大选。如果是奇数的话，还需要单独把中间那个数算出来，多一个 ```9*```次数的感觉。另外总觉得这个题很像之前cjc老师教数字逻辑课的时候，某一种二进制的写法一样，每次也是要颠倒一下，具体记不太清了；

第四题：未来有机会再试了TAT（周常鸽第四题）；

<!--more-->

# 第一题：5268.找出两数组的不同

[题目链接](https://leetcode-cn.com/problems/find-the-difference-of-two-arrays/)

## 题目大意

给你两个下标从 ```0``` 开始的整数数组 ```nums1``` 和 ```nums2``` ，请你返回一个长度为 ```2``` 的列表 ```answer``` ，其中：

- ```answer[0]``` 是 ```nums1``` 中所有 **不** 存在于 ```nums2``` 中的 **不同** 整数组成的列表。
- ```answer[1]``` 是 ```nums2``` 中所有 **不** 存在于 ```nums1``` 中的 **不同** 整数组成的列表。

**注意**：列表中的整数可以按 **任意** 顺序返回。

示例1：
```
输入：nums1 = [1,2,3], nums2 = [2,4,6]
输出：[[1,3],[4,6]]
解释：
对于 nums1 ，nums1[1] = 2 出现在 nums2 中下标 0 处，然而 nums1[0] = 1 和 nums1[2] = 3 没有出现在 nums2 中。因此，answer[0] = [1,3]。
对于 nums2 ，nums2[0] = 2 出现在 nums1 中下标 1 处，然而 nums2[1] = 4 和 nums2[2] = 6 没有出现在 nums2 中。因此，answer[1] = [4,6]。
```

示例2：
```
输入：nums1 = [1,2,3,3], nums2 = [1,1,2,2]
输出：[[3],[]]
解释：
对于 nums1 ，nums1[2] 和 nums1[3] 没有出现在 nums2 中。由于 nums1[2] == nums1[3] ，二者的值只需要在 answer[0] 中出现一次，故 answer[0] = [3]。
nums2 中的每个整数都在 nums1 中出现，因此，answer[1] = [] 。 
```

提示：
- 1 <= nums1.length, nums2.length <= 1000
- -1000 <= nums1[i], nums2[i] <= 1000

## 分析和解答

比较简单，但是又忘记了 ```set``` 是可以直接相减的了，还用了个 ```Counter``` 搞来搞去整了半天，有点过于耗费时间了；


方法1（比赛时方法，现在看起来有点蠢hhh）：
```python
class Solution(object):
    def findDifference(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: List[List[int]]
        """
        nums1 = list(set(nums1))
        nums2 = list(set(nums2))
        
        cnt1 = Counter(nums1) - Counter(nums2)
        cnt2 = Counter(nums2) - Counter(nums1)
        
        print(cnt1)
        print(cnt2)
        
        res = []
        
        tmp = []
        for key, value in cnt1.items():
            tmp.append(key)
        res.append(tmp)
        
        tmp = []
        for key, value in cnt2.items():
            tmp.append(key)
        res.append(tmp)
        
        return res
```


方法2（set方法，只需一行233333）：
```python
class Solution(object):
    def findDifference(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: List[List[int]]
        """
        return [list(set(nums1) - set(nums2)), list(set(nums2) - set(nums1))]
```


# 第二题：5236.美化数组的最少删除数

[题目链接](https://leetcode-cn.com/problems/minimum-deletions-to-make-array-beautiful/)

## 题目大意

给你一个下标从 **0** 开始的整数数组 ```nums``` ，如果满足下述条件，则认为数组 ```nums``` 是一个 **美丽数组** ：

- ```nums.length``` 为偶数
- 对所有满足 ```i % 2 == 0``` 的下标 ```i``` ，```nums[i] != nums[i + 1]``` 均成立

注意，空数组同样认为是美丽数组。

你可以从 ```nums``` 中删除任意数量的元素。当你删除一个元素时，被删除元素右侧的所有元素将会向左移动一个单位以填补空缺，而左侧的元素将会保持 **不变** 。

返回使 ```nums``` 变为美丽数组所需删除的 **最少** 元素数目。

示例1：
```
输入：nums = [1,1,2,3,5]
输出：1
解释：可以删除 nums[0] 或 nums[1] ，这样得到的 nums = [1,2,3,5] 是一个美丽数组。可以证明，要想使 nums 变为美丽数组，至少需要删除 1 个元素。
```

示例2：
```
输入：nums = [1,1,2,2,3,3]
输出：2
解释：可以删除 nums[0] 和 nums[5] ，这样得到的 nums = [1,2,2,3] 是一个美丽数组。可以证明，要想使 nums 变为美丽数组，至少需要删除 2 个元素。
```

提示：
- ```1 <= nums.length <= 10^5```
- ```0 <= nums[i] <= 10^5```

## 分析和解答

看过去感觉就是个贪心思路的题，没有太验证过就开始写了，在调试了一些case后最后还是过了，自己搞这种题目的话如果这样就会要么过，要么就推倒重来，其实还是应该稍微合理性验证下的！很像之前有一个“模板匹配”的题目，主要需要反向思维一下，把删除变成添加，然后贪心就可以！

```python
class Solution(object):
    def minDeletion(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        
        if len(nums) == 0:
            return 0
        
        res = [-1 for _ in range(len(nums))]
        idx_res = 0
        idx_nums = 0
        
        i = 0
        while i < len(nums):
            if idx_res % 2 == 0:
                """ 如果是偶数位了，就直接贪心选 """
                res[idx_res] = nums[i]
                idx_res += 1
                i += 1
            elif idx_res % 2 != 0:
                """ 如果是奇数位，选一个离得比较远的 """
                while i < len(nums):
                    if nums[i] != res[idx_res-1]:
                        break
                    i += 1
                    
                # print(nums[i], res[idx_res-1])
                try:
                    res[idx_res] = nums[i]
                    idx_res += 1
                    i += 1
                except:
                    break
            
            # print("out: ", i)
                

        if idx_res % 2 != 0:
            idx_res -= 1
            
            
        # print(res[:idx_res])
        return len(nums) - idx_res
```


# 第三题：5253.找到指定长度的回文数

[题目链接](https://leetcode-cn.com/problems/find-palindrome-with-fixed-length/)

## 题目大意

给你一个整数数组 ```queries``` 和一个 **正** 整数 ```intLength``` ，请你返回一个数组 ```answer``` ，其中 ```answer[i]``` 是长度为 ```intLength``` 的 **正回文数** 中第 ```queries[i]``` 小的数字，如果不存在这样的回文数，则为 ```-1```。

**回文数** 指的是从前往后和从后往前读一模一样的数字。回文数不能有前导 0 。

示例1：
```
输入：queries = [1,2,3,4,5,90], intLength = 3
输出：[101,111,121,131,141,999]
解释：
长度为 3 的最小回文数依次是：
101, 111, 121, 131, 141, 151, 161, 171, 181, 191, 201, ...
第 90 个长度为 3 的回文数是 999 。
```

示例2：
```
输入：queries = [2,4,6], intLength = 4
输出：[1111,1331,1551]
解释：
长度为 4 的前 6 个回文数是：
1001, 1111, 1221, 1331, 1441 和 1551 。
```

提示：
- ```1 <= queries.length <= 5 * 10^4```
- ```1 <= queries[i] <= 10^9```
- ```1 <= intLength <= 15```


## 分析和解答

数学算角标的题目，如果是多年前刚刚接触编程的时候估计找到规律后也算不太对，但现在只要手动推导出来规律后实现起来应该几乎差不多了。这次多少还是有点面向答案编程的感觉，出一个badcase调试一组出一个badcase调试一组，这个是个比较坏的习惯。整体来说的话，这个题的思路就是算一半！比如要是四位数（偶数）的话，就是前一半先写 ```10 11 12 13``` ，这样二位数往上递增的，后一半直接把这个反向过来拼接就行，其实也是个隐含的贪心思路，就等于从小往大选。如果是奇数的话，还需要单独把中间那个数算出来，多一个 ```9*```次数的感觉。另外总觉得这个题很像之前cjc老师教数字逻辑课的时候，某一种二进制的写法一样，每次也是要颠倒一下，具体记不太清了；

```python
class Solution(object):
    def kthPalindrome(self, queries, intLength):
        """
        :type queries: List[int]
        :type intLength: int
        :rtype: List[int]
        """
        res = []
        if intLength == 1:
            for i in range(len(queries)):
                res.append(queries[i])
            # print(res)
            # return res
        elif intLength == 2:
            for i in range(len(queries)):
                tmp = int(str(queries[i]) + str(queries[i]))
                res.append(tmp)
            # print(res)
            # return res
        
        # elif intLength == 3:
        #     for i in range(len(queries)):
        #         a = queries[i] // 10 + 1
        #         b = queries[i] % 10 - 1
        #         if b == -1:
        #             b = 9
        #         if a == 10:
        #             a = 9
        #         tmp = int(str(a) + str(b) + str(a))   
        #         res.append(tmp)
        #     # print(res)
        #     # return res
        else:      
            ttmp = intLength // 2  # 整除2
            if intLength % 2 == 0:  # 要求偶数位，那就是第i个ttmp位数
                base_idx = 10 ** (ttmp - 1) - 1
                for i in range(len(queries)):
                    half = str(base_idx + queries[i])
                    res.append(int(half + half[::-1]))
                # return res
            else:  # 奇数位的，先确定中间的
                for i in range(len(queries)):
                    mid = str((queries[i] - 1) % 10)
                    # print(mid)
                    base_idx = 10 ** (ttmp - 1) - 1  # 多少位数在前边
                    
                    if queries[i] % 10 != 0:
                        offset = (queries[i] // 10) + 1
                    else:
                        offset = (queries[i] // 10)
                    
                    
                    
                    half = str(base_idx + offset)
                    # print(half)
                    res.append(int(half+mid+half[::-1]))
            
                # return res
        
        max_tmp = (10 ** intLength) - 1
        
        for i in range(len(res)):
            if res[i] > max_tmp:
                res[i] = -1
        return res
```