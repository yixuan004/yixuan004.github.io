---
title: 293场LeetCode周赛
date: 2022-05-15 16:24:58
tags:
    - 数组
    - 哈希表
    - 字符串
    - 排序
    - 位运算
    - 计数
    - EASY
    - MEDIUM
    - HARD
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

没想到一周过后学校就各个地方都封了，快乐没有了啊gg，感觉最近也是越来越没干劲，还是通过多刷一刷题缓解迷茫吧~ 周赛练思路，平常练面试基础题了，之前朋友说的“基础不牢，地动山摇”是真的太有道理了，上一周也是又被上了一课hhh

第一题：第一题还是要仔细读题，然后按照题目的内容模拟操作就可以了，这里开始开读错了一次题，应该是前后 `i` 和 `i-1` 之间不相同就可以了，python的话可以用 `Counter` 小偷一鸡，其他语言可以用字符串排序后做比较；

第二题：是个哨兵的题，开始应该注意一下时间复杂度的，另外空间和时间复杂度一样应该是不能开到 `10^9` 这个级别的，应该注意下题目示例说明，那样感觉很容易就能想到是加哨兵然后遍历的思路了；

第三题：二进制的题目，本来开始的时候没有什么思路，后来边和同学讨论边做，同学说要不要先转化成二进制看看，然后转成二进制后发现只要纵向遍历，看看哪一列的1最多就可以了，根据之前刷题的经验，纵向遍历就是把 `for j in range(len(a[0]))` 放在 `for i in range(len(a))` 之外，但是内部循环还是 `a[i][j]` 就可以了；

第四题：这种多次区间维护和查询的操作感觉看起来就很像是线段树的题目了，咕咕了；

<!--more-->

# 第一题：5234.移除字母异位词后的结果数组

[题目链接](https://leetcode.cn/problems/find-resultant-array-after-removing-anagrams/)

## 题目大意

给你一个下标从 **0** 开始的字符串 `words` ，其中 `words[i]` 由小写英文字符组成。

在一步操作中，需要选出任一下标 `i` ，从 `words` 中 **删除** `words[i]` 。其中下标 `i` 需要同时满足下述两个条件：
1. `0 < i < words.length`
2. `words[i - 1]` 和 `words[i]` 是 **字母异位词** 。

只要可以选出满足条件的下标，就一直执行这个操作。

在执行所有操作后，返回 `words` 。可以证明，按任意顺序为每步操作选择下标都会得到相同的结果。

**字母异位词** 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。例如，`"dacb"` 是 `"abdc"` 的一个字母异位词。

示例1：
```
输入：words = ["abba","baba","bbaa","cd","cd"]
输出：["abba","cd"]
解释：
获取结果数组的方法之一是执行下述步骤：
- 由于 words[2] = "bbaa" 和 words[1] = "baba" 是字母异位词，选择下标 2 并删除 words[2] 。
  现在 words = ["abba","baba","cd","cd"] 。
- 由于 words[1] = "baba" 和 words[0] = "abba" 是字母异位词，选择下标 1 并删除 words[1] 。
  现在 words = ["abba","cd","cd"] 。
- 由于 words[2] = "cd" 和 words[1] = "cd" 是字母异位词，选择下标 2 并删除 words[2] 。
  现在 words = ["abba","cd"] 。
无法再执行任何操作，所以 ["abba","cd"] 是最终答案。
```

示例2：
```
输入：words = ["a","b","c","d","e"]
输出：["a","b","c","d","e"]
解释：
words 中不存在互为字母异位词的两个相邻字符串，所以无需执行任何操作。
```

提示：
- `1 <= words.length <= 100`
- `1 <= words[i].length <= 10`
- `words[i]` 由小写英文字母组成

## 分析和解答

第一题还是要仔细读题，然后按照题目的内容模拟操作就可以了，这里开始开读错了一次题，应该是前后 `i` 和 `i-1` 之间不相同就可以了，python的话可以用 `Counter` 小偷一鸡，其他语言可以用字符串排序后做比较；

```python
class Solution(object):
    def removeAnagrams(self, words):
        """
        :type words: List[str]
        :rtype: List[str]
        """
        i = 1
        res = []
        init_cnt = Counter(words[0])
        res.append(words[0])
        while i < len(words):
            if Counter(words[i]) == init_cnt:
                i += 1
                continue
            else:
                init_cnt = Counter(words[i])
                res.append(words[i])
                i += 1

        return res
```

# 第二题：6064.不含特殊楼层的最大连续楼层数

[题目链接](https://leetcode.cn/problems/maximum-consecutive-floors-without-special-floors/)

## 题目大意

Alice 管理着一家公司，并租用大楼的部分楼层作为办公空间。Alice 决定将一些楼层作为 **特殊楼层** ，仅用于放松。

给你两个整数 `bottom` 和 `top` ，表示 Alice 租用了从 `bottom` 到 `top`（含 `bottom` 和 `top` 在内）的所有楼层。另给你一个整数数组 `special` ，其中 `special[i]` 表示  Alice 指定用于放松的特殊楼层。

返回不含特殊楼层的 **最大** 连续楼层数。

示例1：
```
输入：bottom = 2, top = 9, special = [4,6]
输出：3
解释：下面列出的是不含特殊楼层的连续楼层范围：
- (2, 3) ，楼层数为 2 。
- (5, 5) ，楼层数为 1 。
- (7, 9) ，楼层数为 3 。
因此，返回最大连续楼层数 3 。
```

示例2：
```
输入：bottom = 6, top = 8, special = [7,6,8]
输出：0
解释：每层楼都被规划为特殊楼层，所以返回 0 。
```

提示
- `1 <= special.length <= 10^5`
- `1 <= bottom <= special[i] <= top <= 10^9`
- `special` 中的所有值 互不相同

## 分析和解答

是个哨兵的题，开始应该注意一下时间复杂度的，另外空间和时间复杂度一样应该是不能开到 `10^9` 这个级别的，应该注意下题目示例说明，那样感觉很容易就能想到是加哨兵然后遍历的思路了；

```python
class Solution(object):
    def maxConsecutive(self, bottom, top, special):
        """
        :type bottom: int
        :type top: int
        :type special: List[int]
        :rtype: int
        """
        
        special.append(bottom-1)
        special.append(top+1)
        special.sort()
        
        print(special)
        res = 0
        
        for i in range(1, len(special)):
            res = max(res, special[i]-special[i-1]-1)
        
        return res
```

# 第三题：6065.按位与结果大于零的最长组合

[题目链接](https://leetcode.cn/problems/largest-combination-with-bitwise-and-greater-than-zero/)

## 题目大意

对数组 `nums` 执行 **按位与** 相当于对数组 `nums` 中的所有整数执行 **按位与** 。
- 例如，对 `nums = [1, 5, 3]` 来说，按位与等于 `1 & 5 & 3 = 1` 。
- 同样，对 `nums = [7]` 而言，按位与等于 `7` 。

给你一个正整数数组 `candidates` 。计算 `candidates` 中的数字每种组合下 **按位与** 的结果。 `candidates` 中的每个数字在每种组合中只能使用 **一次** 。

返回按位与结果大于 `0` 的 **最长** 组合的长度。

示例1：
```
输入：candidates = [16,17,71,62,12,24,14]
输出：4
解释：组合 [16,17,62,24] 的按位与结果是 16 & 17 & 62 & 24 = 16 > 0 。
组合长度是 4 。
可以证明不存在按位与结果大于 0 且长度大于 4 的组合。
注意，符合长度最大的组合可能不止一种。
例如，组合 [62,12,24,14] 的按位与结果是 62 & 12 & 24 & 14 = 8 > 0 。
```

示例2：
```
输入：candidates = [8,8]
输出：2
解释：最长组合是 [8,8] ，按位与结果 8 & 8 = 8 > 0 。
组合长度是 2 ，所以返回 2 。
```

提示：
- `1 <= candidates.length <= 10^5`
- `1 <= candidates[i] <= 10^7`

## 分析和解答

二进制的题目，本来开始的时候没有什么思路，后来边和同学讨论边做，同学说要不要先转化成二进制看看，然后转成二进制后发现只要纵向遍历，看看哪一列的1最多就可以了，根据之前刷题的经验，纵向遍历就是把 `for j in range(len(a[0]))` 放在 `for i in range(len(a))` 之外，但是内部循环还是 `a[i][j]` 就可以了；

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-05-16-10-17-08.png)

```python
class Solution(object):
    def largestCombination(self, candidates):
        """
        :type candidates: List[int]
        :rtype: int
        """
        candidates.sort()
        
        def convert_to_bin(num, pad=31):
            tmp_list = []
            while num > 0:
                tmp_list.append(num&1)
                num >>= 1
            
            tmp_list.reverse()
            tmp_list = [0] * (pad-len(tmp_list)) + tmp_list
            
            return tmp_list
        
        a = []
        
        
        for i in range(len(candidates)):
            a.append(convert_to_bin(candidates[i]))
            # print(candidates[i], convert_to_bin(candidates[i]))
        
        res = 0
        for j in range(len(a[0])):
            tmp = 0
            for i in range(len(a)):
                if a[i][j] == 1:
                    tmp += 1
            res = max(res, tmp)
        
        return res
```