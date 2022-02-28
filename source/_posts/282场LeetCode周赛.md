---
title: 282场LeetCode周赛
date: 2022-02-27 23:17:19
tags:
    - 
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

又是光速做完前两个题，然后盯着第三题发呆的一周周赛，第三题这次用到的思路是二分，感觉还是要多刷题多积累，可能需要找个时间复盘一下做题常用到的思路了吧哈哈哈，现在感觉思路有点打不开了，可能要阶段性总结梳理下吧，这样避免下有些想法太极端了~

第一题：这个题python有```str.startswtih()```函数，用这个返回值的```True```和```False```直接加就行了；

第二题：一眼看过去都有点没看懂这个题，后来感觉就是一个计数两个字符串的题目，计数一下然后互相用Counter相减的性质，互相相减后统计字符个数就行了；

第三题：这个题一眼看过去就感觉有很暴力的思路，遍历每个时刻，在每个时刻下再进一步遍历数组，判断是不是可以超过需要的趟数，后来在大佬的提醒下是在遍历每个时刻这里可以二分，这很符合二分的性质，也是好久没在具体的题里用到过二分了；

第四题：鸽了鸽了；

<!--more-->


# 第一题：6008.统计包含给定前缀的字符串

[题目链接](https://leetcode-cn.com/problems/counting-words-with-a-given-prefix/)

## 题目大意

给你一个字符串数组 ```words``` 和一个字符串 ```pref``` 。

返回 ```words``` 中以 ```pref``` 作为 **前缀** 的字符串的数目。

字符串 ```s``` 的 **前缀** 就是  ```s``` 的任一前导连续字符串。

示例1：
```
输入：words = ["pay","attention","practice","attend"], pref = "at"
输出：2
解释：以 "at" 作为前缀的字符串有两个，分别是："attention" 和 "attend" 。
```

示例2：
```
输入：words = ["leetcode","win","loops","success"], pref = "code"
输出：0
解释：不存在以 "code" 作为前缀的字符串。
```

提示：
- ```1 <= words.length <= 100```
- ```1 <= words[i].length, pref.length <= 100```
- ```words[i]``` 和 ```pref``` 由小写英文字母组成


## 分析和解答

直接调用python的```str.startswith(pref)```函数就可以，这里或许可以借机复习下Trie树的数据结构，每个节点由一个mapping的词典和```is_last```标记组成，第一次插入一个单词的时候就是不断创造mapping里边嵌套的mapping，中间的节点is_last都是False，最后的一个节点is_last设置成True；之后插入第二个单词的时候就有一种寻迹的感觉了，如果已经有了就不用重复插入；

p.s.没想到这题和某top10排名大佬代码一模一样哈哈哈，奇怪的开心点增加了

```python
class Solution(object):
    def prefixCount(self, words, pref):
        """
        :type words: List[str]
        :type pref: str
        :rtype: int
        """
        res = 0
        for word in words:
            if word.startswith(pref):
               res += 1 
        
        return res
```


# 第二题：2186.使两字符串互为字母异位词的最少步骤数

[题目链接](https://leetcode-cn.com/problems/minimum-number-of-steps-to-make-two-strings-anagram-ii/)

## 题目大意

给你两个字符串 ```s``` 和 ```t``` 。在一步操作中，你可以给 ```s``` 或者 ```t``` 追加 **任一字符** 。

返回使 ```s``` 和 ```t``` 互为 **字母异位词** 所需的最少步骤数。

**字母异位词** 指字母相同但是顺序不同（或者相同）的字符串。

示例1：
```
输入：s = "leetcode", t = "coats"
输出：7
解释：
- 执行 2 步操作，将 "as" 追加到 s = "leetcode" 中，得到 s = "leetcodeas" 。
- 执行 5 步操作，将 "leede" 追加到 t = "coats" 中，得到 t = "coatsleede" 。
"leetcodeas" 和 "coatsleede" 互为字母异位词。
总共用去 2 + 5 = 7 步。
可以证明，无法用少于 7 步操作使这两个字符串互为字母异位词。
```

示例2：
```
输入：s = "night", t = "thing"
输出：0
解释：给出的字符串已经互为字母异位词。因此，不需要任何进一步操作。
```

提示：
- ```1 <= s.length, t.length <= 2 * 10^5```
- ```s``` 和 ```t``` 由小写英文字符组成

## 分析和解答

看到这个题就想着要拟合测试样例，感觉能拟合测试样例可能就差不多能做出来了，然后看到第二个样例感觉就是比较明显的Counter了，感觉就是一个计数两个字符串的题目，计数一下然后互相用Counter相减的性质，互相相减后统计字符个数就行了；

```python
class Solution(object):
    def minSteps(self, s, t):
        """
        :type s: str
        :type t: str
        :rtype: int
        """
        s_counter = Counter(s)
        t_counter = Counter(t)
        # print(s_counter)
        # print(t_counter)
        
        s_t_counter = s_counter - t_counter
        t_s_counter = t_counter - s_counter
        
        # print("s_t_counter:", s_t_counter)
        # print("t_s_counter:", t_s_counter)
        res = 0
        for key, value in s_t_counter.items():
            res += value
        for key, value in t_s_counter.items():
            res += value
        return res
```


# 第三题：2187.完成旅途的最少时间

[题目链接](https://leetcode-cn.com/problems/minimum-time-to-complete-trips/)

## 题目大意

给你一个数组 ```time``` ，其中 ```time[i]``` 表示第 ```i``` 辆公交车完成 **一趟旅途** 所需要花费的时间。

每辆公交车可以 **连续** 完成多趟旅途，也就是说，一辆公交车当前旅途完成后，可以 **立马开始** 下一趟旅途。每辆公交车 独立 运行，也就是说可以同时有多辆公交车在运行且互不影响。

给你一个整数 ```totalTrips``` ，表示所有公交车 **总共** 需要完成的旅途数目。请你返回完成 **至少** ```totalTrips``` 趟旅途需要花费的 **最少** 时间。

示例1：
```
输入：time = [1,2,3], totalTrips = 5
输出：3
解释：
- 时刻 t = 1 ，每辆公交车完成的旅途数分别为 [1,0,0] 。
  已完成的总旅途数为 1 + 0 + 0 = 1 。
- 时刻 t = 2 ，每辆公交车完成的旅途数分别为 [2,1,0] 。
  已完成的总旅途数为 2 + 1 + 0 = 3 。
- 时刻 t = 3 ，每辆公交车完成的旅途数分别为 [3,1,1] 。
  已完成的总旅途数为 3 + 1 + 1 = 5 。
所以总共完成至少 5 趟旅途的最少时间为 3 。
```

示例2：
```
输入：time = [2], totalTrips = 1
输出：2
解释：
只有一辆公交车，它将在时刻 t = 2 完成第一趟旅途。
所以完成 1 趟旅途的最少时间为 2 。
```

提示：
- ```1 <= time.length <= 10^5```
- ```1 <= time[i], totalTrips <= 10^7```

## 分析和解答

这个题一眼看过去就感觉有很暴力的思路，遍历每个时刻，在每个时刻下再进一步遍历数组，判断是不是可以超过需要的趟数，后来在大佬的提醒下是在遍历每个时刻这里可以二分，这很符合二分的性质，也是好久没在具体的题里用到过二分了；

二分的过程细节需要把握下，就是 ```right left = mid``` 还是 ```mid + 1``` 还是 ```mid - 1``` 这类的问题。

```python
class Solution(object):
    def minimumTime(self, time, totalTrips):
        """
        :type time: List[int]
        :type totalTrips: int
        :rtype: int
        """
        def judge(mid, time, totalTrips):
            tmp = 0
            for t in time:
                tmp += mid // t
            
            if tmp >= totalTrips:
                return True
            else:
                return False
            
        left = 1
        right = min(time) * totalTrips
        
        while left < right:
            mid = (left + right) >> 1  # 中间那个时间
            # 判断中间那个时间是否可以完成
            if judge(mid, time, totalTrips):
                right = mid
            else:
                left = mid + 1
        
        return left
```