---
title: 275场LeetCode周赛
date: 2022-01-16 21:20:42
tags:
    - 数组
    - 哈希表
    - 矩阵
    - 滑动窗口
    - 位运算
    - 字符串
    - 排序
    - EASY
    - MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

前两个题实际上说感觉还没有那么容易想，第三个题的话感觉简化的思路是需要复盘梳理的，另外只能说Counter永远的神哈哈哈哈

第一题：这个题开始想法是把每个位置都遍历判断一下，但这样搞就超时了，后来发现只判断对角线就可以了，这里还有个思路是取矩阵的某一行可以用```matrix[i]```，而取矩阵的某一列没有什么很好的思路，可以用```[matrix[x][j] for x in range(n)]```，这里的```j```代表第```j```列；

第二题：比较思路题的感觉，用一个滑动窗口存储最终状态，当前状态和最终状态的差距就是move的次数，另外有个需要注意的是，滑动窗口的尾部角标是i+k（或者i+k-1一类的），这个题也是能体现出Counter的好用，但是Counter在使用的时候时间复杂度貌似比较高，注意每次不要都生成新的Counter，只要根据首尾位置修改即可；

第三题：只能说是非常妙的一个题，普通搞的话很容易超时，于是可以将每个单词的字母排序后放入到一个set/dict/Counter中，这样可以O(1)的判断是否存在；

第四题：未来有机会再试了TAT（周常鸽第四题）；

<!--more-->

# 第一题：2133.检查是否每一行每一列都包含全部整数

[题目链接](https://leetcode-cn.com/problems/check-if-every-row-and-column-contains-all-numbers/)

## 题目大意

对一个大小为```n x n```的矩阵而言，如果其每一行和每一列都包含从```1```到```n```的**全部**整数（含```1```和```n```），则认为该矩阵是一个**有效**矩阵。

给你一个大小为```n x n```的整数矩阵```matrix```，请你判断矩阵是否为一个有效矩阵：如果是，返回```true```；否则，返回```false```。

示例1：
```
输入：matrix = [[1,2,3],[3,1,2],[2,3,1]]
输出：true
解释：在此例中，n = 3 ，每一行和每一列都包含数字 1、2、3 。
因此，返回 true 。
```

示例2：
```
输入：matrix = [[1,1,1],[1,2,3],[1,2,3]]
输出：false
解释：在此例中，n = 3 ，但第一行和第一列不包含数字 2 和 3 。
因此，返回 false 。
```

提示：
- ```n == matrix.length == matrix[i].length```
- ```1 <= n <= 100```
- ```1 <= matrix[i][j] <= n```

## 分析和解答

这个题开始想法是把每个位置都遍历判断一下，但这样搞就超时了，后来发现只判断对角线就可以了，这里还有个思路是取矩阵的某一行可以用```matrix[i]```，而取矩阵的某一列没有什么很好的思路，可以用```[matrix[x][j] for x in range(n)]```，这里的```j```代表第```j```列；

```python
class Solution(object):
    def checkValid(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: bool
        """      
        
        """
        # tle
        n = len(matrix)
        target = [i+1 for i in range(n)]
        target_count = Counter(target)
        for i in range(n):
            for j in range(n):
                tmp_counter1 = Counter(matrix[i])
                tmp_counter2 = Counter([matrix[x][j] for x in range(n)])
                if not (tmp_counter1 == target_count and tmp_counter2 == target_count):
                    return False
        return True
        """
    
        # 只判断对角线就够了？
        n = len(matrix)
        target = [i+1 for i in range(n)]
        target_count = Counter(target)
        for i in range(n):
            tmp_counter1 = Counter(matrix[i])
            tmp_counter2 = Counter([matrix[x][i] for x in range(n)])
            if not (tmp_counter1 == target_count and tmp_counter2 == target_count):
                return False
        return True
```

# 第二题：2134.最少交换次数来组合所有的1 II

[题目链接](https://leetcode-cn.com/problems/minimum-swaps-to-group-all-1s-together-ii/)

## 题目大意

**交换**定义为选中一个数组中的两个**互不相同**的位置并交换二者的值。

**环形**数组是一个数组，可以认为**第一个**元素和**最后一个**元素**相邻**。

给你一个**二进制环形**数组```nums```，返回在**任意位置**将数组中的所有```1```聚集在一起需要的最少交换次数。

示例1：
```
输入：nums = [0,1,0,1,1,0,0]
输出：1
解释：这里列出一些能够将所有 1 聚集在一起的方案：
[0,0,1,1,1,0,0] 交换 1 次。
[0,1,1,1,0,0,0] 交换 1 次。
[1,1,0,0,0,0,1] 交换 2 次（利用数组的环形特性）。
无法在交换 0 次的情况下将数组中的所有 1 聚集在一起。
因此，需要的最少交换次数为 1 。
```

示例2：
```
输入：nums = [0,1,1,1,0,0,1,1,0]
输出：2
解释：这里列出一些能够将所有 1 聚集在一起的方案：
[1,1,1,0,0,0,0,1,1] 交换 2 次（利用数组的环形特性）。
[1,1,1,1,1,0,0,0,0] 交换 2 次。
无法在交换 0 次或 1 次的情况下将数组中的所有 1 聚集在一起。
因此，需要的最少交换次数为 2 。
```

示例3：
```
输入：nums = [1,1,0,0,1]
输出：0
解释：得益于数组的环形特性，所有的 1 已经聚集在一起。
因此，需要的最少交换次数为 0 。
```

提示：
- ```1 <= nums.length <= 105```
- ```nums[i]```为```0```或者```1```


## 分析和解答

比较思路题的感觉，用一个滑动窗口存储最终状态，当前状态和最终状态的差距就是move的次数，另外有个需要注意的是，滑动窗口的尾部角标是i+k（或者i+k-1一类的），这个题也是能体现出Counter的好用，但是Counter在使用的时候时间复杂度貌似比较高，注意每次不要都生成新的Counter，只要根据首尾位置修改即可；

另外注意，这种环形的可以通过补充在最后边来进行简易的实现，取模的思路有点麻烦了，python语言还有其专门使用的extend函数；

```python
class Solution(object):
    def minSwaps(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # 处理出一个初始化的目标状态
        target_counter = Counter(nums)
        del target_counter[0]
        
        cnt = 0
        for i in range(target_counter[1]):
            nums.append(nums[cnt])
            cnt += 1
        
        min_swaps = target_counter[1]
        init_counter = Counter(nums[0: (target_counter[1])])
        min_swaps = min(min_swaps, (init_counter-target_counter)[0])
        # print(init_counter)
        
        
        for i in range(1, len(nums)-target_counter[1]+1):
            init_counter -= Counter([nums[i-1]])
            init_counter += Counter([nums[i+target_counter[1]-1]])
            # print(init_counter)
            min_swaps = min(min_swaps, (init_counter-target_counter)[0])
            
        
        return min_swaps
```


# 第三题：2135.统计追加字母可以获得的单词数

[题目链接](https://leetcode-cn.com/problems/count-words-obtained-after-adding-a-letter/)

## 题目大意

给你两个下标从**0**开始的字符串数组```startWords```和```targetWords```。每个字符串都仅由**小写英文字母**组成。

对于```targetWords```中的每个字符串，检查是否能够从 startWords 中选出一个字符串，执行一次**转换操作**，得到的结果与当前```targetWords```字符串相等。

**转换操作**如下面两步所述：

1. **追加**任何 不存在 于当前字符串的任一小写字母到当前字符串的末尾。
- 例如，如果字符串为```"abc"```，那么字母```'d'```、```'e'```或```'y'```都可以加到该字符串末尾，但```'a'```就不行。如果追加的是```'d'```，那么结果字符串为```"abcd"```。

2. **重排**新字符串中的字母，可以按**任意**顺序重新排布字母。
- 例如，```"abcd"```可以重排为```"acbd"```、```"bacd"```、```"cbda"```，以此类推。注意，它也可以重排为```"abcd"```自身。

找出```targetWords```中有多少字符串能够由```startWords```中的**任一**字符串执行上述转换操作获得。返回```targetWords```中这类**字符串的数目**。

注意：你仅能验证```targetWords```中的字符串是否可以由```startWords```中的某个字符串经执行操作获得。```startWords```中的字符串在这一过程中 不 发生实际变更。

## 分析和解答

只能说是非常妙的一个题，普通搞的话很容易超时，于是可以将每个单词的字母排序后放入到一个set/dict/Counter中，这样可以O(1)的判断是否存在；

其中感觉也有用到一种反向的思维，目标单词是startWord加上一个字母，所以startWord是目标单词减去一个字母，这样的话可能更容易想到是用一个set/dict/Counter来进行查找了；

还是很值得复盘的一个题目，感觉字母排序也是一个需要复盘的点；


TLE超时版本代码：
```python
class Solution(object):
    def wordCount(self, startWords, targetWords):
        """
        :type startWords: List[str]
        :type targetWords: List[str]
        :rtype: int
        """

        """
        # TLE
        result_cnt = 0
        none_counter = Counter("")
        counter_start_words = [Counter(x) for x in startWords]
        counter_target_words = [Counter(x) for x in targetWords]
        for i in range(len(counter_target_words)):
            flag = 0
            for j in range(len(counter_start_words)):
                # 剪枝
                if len(targetWords[i]) - len(counter_start_words[j]) != 1:
                    continue
                if len([key for key in (counter_start_words[j] - counter_target_words[i]).keys()]) != 0:
                    continue
                
                tmp_counter = counter_target_words[i] - counter_start_words[j]    
                # 必须只有一个键，且这个键值为1，且这个不出现在原来的counter_target_words[i]中
                key_list = [key for key in tmp_counter.keys()]
                if len(key_list) == 1 and tmp_counter[key_list[0]] == 1 and counter_start_words[j].get(key_list[0]) is None:
                    flag = 1
                    break                    
            if flag == 1:
                result_cnt += 1
        return result_cnt
        """
        set_start_words = [set(x) for x in startWords]
        set_target_words = [set(x) for x in targetWords]
        result_cnt = 0

        for i in range(len(targetWords)):
            for j in range(len(startWords)):
                if len(targetWords[i]) - len(startWords[j]) != 1:
                    continue
                if len(list(set_target_words[i].symmetric_difference(set_start_words[j]))) == 1:
                    print(set_target_words[i], set_start_words[j])
                    result_cnt += 1
                    break
        return result_cnt

```

修改后的不超时代码：
```python
class Solution(object):
    def wordCount(self, startWords, targetWords):
        """
        :type startWords: List[str]
        :type targetWords: List[str]
        :rtype: int
        """
        set_start_words = set(["".join(sorted(list(startWord))) for startWord in startWords])
        sorted_target_words = ["".join(sorted(list(targetWord))) for targetWord in targetWords]
        counter_sorted_target_words = Counter(sorted_target_words)

        cnt = 0
        for targetWord, value in counter_sorted_target_words.items():
            for i in range(len(targetWord)):
                tmp_s = targetWord[:i] + targetWord[i+1:]
                if tmp_s in set_start_words:
                    cnt += value
                    break
        return cnt
```

