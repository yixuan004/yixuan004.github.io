---
title: 291场LeetCode周赛
date: 2022-05-02 09:07:10
tags:
    - 
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

久违的前1000名了，这次能排的稍微靠前点在于前三个题特别快的就做出来了，可惜第四个题没做出来，要不说不定能到前500呢hh~ 上周做了一回笔试题，今天再看周赛题还有点陌生了，也可能是这种陌生感让自己做快了一些吧，另外还是喜欢描述比较短的题；

感觉就是状态好+题目对着自己常做的思路吧，也可能和周中稍微做了几个题有些关系，前三个题描述还都不是很简单，但是做出来的都比自己预期的快一些，第四个题上来方向又走偏了，如果走到数学规律上面的话感觉可能还有戏，这个题是个“贡献”类的思路，比较巧妙

第一题：毕竟第一题，直接暴力每个位置比较+列表拼接join就可以了，注意最后的返回值是个字符串，室友大佬后来还提醒说这个题中间如果转int处理可能会爆，看来是用python正好就把这个坑给避开了；

第二题：做一个哈希表，每个数字映射到他出现过位置的一个列表，然后遍历这个哈希表，看看区间长度比较下找个min的就可以了；

第三题：这个题先判断每个位置是否可以被p整除，然后根据这个做一个新的列表，能被p整除的就是1，不能被p整除的就是0，之后按照他这个子数组的定义来遍历（就是j要从i开始遍历），去重的话可以用tuple的子数组作为key，然后就是对每个区间sum就可以了；

第四题：一道计算“贡献”的题目，自己一开始想的有点偏，贡献类的题目感觉不能从完全全局来说，要考虑每个字符在全局上的贡献，这个题也是第一次遇到，感觉算法还是很神奇的，具体内容写在博客里了；

<!--more-->

希望下次前1000名来得快一点，这次确实是题简单了

![](/images/2022-05-02-09-13-42.png)

# 第一题：6047.移除指定数字得到的最大结果

[题目链接](https://leetcode.cn/problems/remove-digit-from-number-to-maximize-result/)

## 题目大意

给你一个表示某个正整数的字符串 `number` 和一个字符 `digit` 。

从 `number` 中 **恰好** 移除 **一个** 等于 `digit` 的字符后，找出并返回按 **十进制** 表示 **最大** 的结果字符串。生成的测试用例满足 `digit` 在 `number` 中出现至少一次。

示例1：
```
输入：number = "123", digit = "3"
输出："12"
解释："123" 中只有一个 '3' ，在移除 '3' 之后，结果为 "12" 。
```

示例2：
```
输入：number = "1231", digit = "1"
输出："231"
解释：可以移除第一个 '1' 得到 "231" 或者移除第二个 '1' 得到 "123" 。
由于 231 > 123 ，返回 "231" 。
```

示例3：
```
输入：number = "551", digit = "5"
输出："51"
解释：可以从 "551" 中移除第一个或者第二个 '5' 。
两种方案的结果都是 "51" 。
```

提示：
- `2 <= number.length <= 100`
- `number` 由数字 `'1'` 到 `'9'` 组成
- `digit` 是 `'1'` 到 `'9'` 中的一个数字
- `digit` 在 `number` 中出现至少一次

## 分析和解答

毕竟第一题，直接暴力每个位置比较+列表拼接join就可以了，注意最后的返回值是个字符串，室友大佬后来还提醒说这个题中间如果转int处理可能会爆，看来是用python正好就把这个坑给避开了；

```python
class Solution(object):
    def removeDigit(self, number, digit):
        """
        :type number: str
        :type digit: str
        :rtype: str
        """
        res = -1
        for i in range(len(number)):
            if number[i] == digit:
                tmp_list = ''.join(number[:i] + number[i+1:])
                res = max(res, int(tmp_list))
        
        return str(res)
```


# 第二题：6048.必须拿起的最小连续卡牌数

[题目链接](https://leetcode.cn/problems/minimum-consecutive-cards-to-pick-up/)

## 题目大意

给你一个整数数组 `cards` ，其中 `cards[i]` 表示第 `i` 张卡牌的 **值** 。如果两张卡牌的值相同，则认为这一对卡牌 **匹配** 。

返回你必须拿起的最小连续卡牌数，以使在拿起的卡牌中有一对匹配的卡牌。如果无法得到一对匹配的卡牌，返回 `-1` 。

示例1：
```
输入：cards = [3,4,2,3,4,7]
输出：4
解释：拿起卡牌 [3,4,2,3] 将会包含一对值为 3 的匹配卡牌。注意，拿起 [4,2,3,4] 也是最优方案。
```

示例2：
```
输入：cards = [1,0,5,3]
输出：-1
解释：无法找出含一对匹配卡牌的一组连续卡牌。
```

提示：
- `1 <= cards.length <= 10^5`
- `0 <= cards[i] <= 10^6`

## 分析和解答

做一个哈希表，每个数字映射到他出现过位置的一个列表，然后遍历这个哈希表，看看区间长度比较下找个min的就可以了，还是比较好想到这个思路的

```python
class Solution(object):
    def minimumCardPickup(self, cards):
        """
        :type cards: List[int]
        :rtype: int
        """
        from collections import defaultdict
        tmp_dict = defaultdict(list)
        for i in range(len(cards)):
            tmp_dict[cards[i]].append(i)
        
        # print(tmp_dict)
        res = 1e9
        for value in tmp_dict.values():
            if len(value) < 2:
                continue
            for j in range(len(value)-1):
                res = min(res, value[j+1]-value[j])
        
        if res != 1e9:
            return res+ 1
        else:
            return -1
```


# 第三题：6049.含最多 K 个可整除元素的子数组

[题目链接](https://leetcode.cn/problems/k-divisible-elements-subarrays/)

## 题目大意

给你一个整数数组 `nums` 和两个整数 `k` 和 `p` ，找出并返回满足要求的不同的子数组数，要求子数组中最多 `k` 个可被 `p` 整除的元素。

如果满足下述条件之一，则认为数组 `nums1` 和 `nums2` 是 **不同** 数组：
- 两数组长度 **不同** ，或者
- 存在 **至少** 一个下标 `i` 满足 `nums1[i] != nums2[i]` 。

**子数组** 定义为：数组中的连续元素组成的一个 **非空** 序列。

示例1：
```
输入：nums = [2,3,3,2,2], k = 2, p = 2
输出：11
解释：
位于下标 0、3 和 4 的元素都可以被 p = 2 整除。
共计 11 个不同子数组都满足最多含 k = 2 个可以被 2 整除的元素：
[2]、[2,3]、[2,3,3]、[2,3,3,2]、[3]、[3,3]、[3,3,2]、[3,3,2,2]、[3,2]、[3,2,2] 和 [2,2] 。
注意，尽管子数组 [2] 和 [3] 在 nums 中出现不止一次，但统计时只计数一次。
子数组 [2,3,3,2,2] 不满足条件，因为其中有 3 个元素可以被 2 整除。
```

示例2：
```
输入：nums = [1,2,3,4], k = 4, p = 1
输出：10
解释：
nums 中的所有元素都可以被 p = 1 整除。
此外，nums 中的每个子数组都满足最多 4 个元素可以被 1 整除。
因为所有子数组互不相同，因此满足所有限制条件的子数组总数为 10 。
```

提示：
- 1 <= nums.length <= 200
- 1 <= nums[i], p <= 200
- 1 <= k <= nums.length


## 分析和解答

这个题先判断每个位置是否可以被p整除，然后根据这个做一个新的列表，能被p整除的就是1，不能被p整除的就是0，之后按照他这个子数组的定义来遍历（就是j要从i开始遍历），去重的话可以用tuple的子数组作为key，然后就是对每个区间sum就可以了；

另外还想说这个子字符串好有意思啊，他不是2^n那种遍历方法的，而是j=i那种双重for循环的，看了眼数据范围还在想要不要搞2^n做法呢

```python
class Solution(object):
    def countDistinct(self, nums, k, p):
        """
        :type nums: List[int]
        :type k: int
        :type p: int
        :rtype: int
        """
        # preprocess
        cnt_list = []
        for i in range(len(nums)):
            if nums[i] % p == 0:
                cnt_list.append(1)
            else:
                cnt_list.append(0)
        # print("cnt_list: ", cnt_list)
            
        
        res = 0
        # from collections import defaultdict
        hash_set = {}
        for i in range(len(nums)):
            for j in range(i, len(nums)):
                tmp_list = nums[i:j+1]
                if hash_set.get(tuple(tmp_list)) is not None:
                    continue
                hash_set[tuple(tmp_list)] = 1
                tmp = sum(cnt_list[i:j+1])
                if tmp <= k:
                    res += 1
                
                
                
                # print(tmp_list)
        return res
```


# 第四题：6050.字符串的总引力

[题目链接](https://leetcode.cn/problems/total-appeal-of-a-string/)

## 题目大意

字符串的 **引力** 定义为：字符串中 **不同** 字符的数量。
- 例如，`"abbca"` 的引力为 `3` ，因为其中有 3 个不同字符 `'a'`、`'b'` 和 `'c'` 。

给你一个字符串 `s` ，返回 **其所有子字符串的总引力** 。

**子字符串** 定义为：字符串中的一个连续字符序列。


## 分析和解答

一道计算“贡献”的题目，自己一开始想的有点偏，贡献类的题目感觉不能从完全全局来说，要考虑每个字符在全局上的贡献，这个题也是第一次遇到，感觉算法还是很神奇的，自己举的例子是下边这个图这样，这样能按照每个字符，来统计每个字符的贡献度：

非重复情况：
![](/images/2022-05-02-10-28-47.png)

重复情况：
![](/images/2022-05-02-10-29-13.png)

另外还想再次说这个子字符串好有意思啊，他不是2^n那种遍历方法的，而是j=i那种双重for循环的，看了眼数据范围还在想要不要搞2^n做法呢

正确解答方法：
```python
class Solution(object):
    def appealSum(self, s):
        """
        :type s: str
        :rtype: int
        """
        # 一种每个字符贡献度的思想，还是很有特点的
        res = 0
        char_dict = {}
        for i in range(len(s)):
            if char_dict.get(s[i]) is None:
                res += (i + 1) * (len(s)-i)
            else:
                res += (i - char_dict[s[i]]) * (len(s) - i)
            char_dict[s[i]] = i
        return res
```

这里也额外把自己当时写的暴力方法附上，一位缩减了时间复杂度，实际上像个小丑哈哈哈哈哈哈哈哈，dp了但没完全dp
```python
class Solution(object):
    def appealSum(self, s):
        """
        :type s: str
        :rtype: int
        """
        res = 0
        d1 = {}
        for i in range(len(s)):
            d1[i] = {s[i]: 1}
            res += 1
        print("init: ", d1)
        
        
        res_d = {}
        
        # 这里感觉是一个base基础上的dp，每次要取几位，有点不像平常的遍历思路1
        # 现在要开始每次取两位
        for l in range(2, len(s)+1):
            for i in range(len(s)-l+1):
                # base
                base = s[i:i+l-1]
                # print("base: ", base)
                
                # now（要不要给他加一个标识？）
                tmp_s = s[i:i+l]
                # print("tmp_s: ", tmp_s)
                
                # 增量
                upper = tmp_s[len(base):]
                # print("upper: ", upper)
                
                # 查看upper在不在
                if d1[i].get(upper) is None:  # 不在，这样是一种累加的
                    d1[i][tmp_s] = d1[i][base] + 1
                    d1[i][upper] = 1
                    res += d1[i][tmp_s]
                else:
                    d1[i][tmp_s] = d1[i][base]
                    res += d1[i][tmp_s]
                    
                    # continue
                    # res += d1[i][upper]
                    # d1[i][upper] = d1[i][upper] + 1
                    # continue
                
        # print(d1)
        
        # print(res)
        return res
```