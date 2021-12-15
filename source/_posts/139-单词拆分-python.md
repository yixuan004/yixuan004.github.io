---
title: 139.单词拆分-python
date: 2021-12-14 00:12:45
tags:
    - 字典树
    - 记忆化搜索
    - 哈希表
    - 字符串
    - 动态规划
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

139.单词拆分（中等）

题目大意：给你一个字符串 s 和一个字符串列表 wordDict 作为字典，判定 s 是否可以由空格拆分为一个或多个在字典中出现的单词。

说明：拆分时可以重复使用字典中的单词。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/word-break/)

给你一个字符串 s 和一个字符串列表 wordDict 作为字典，判定 s 是否可以由空格拆分为一个或多个在字典中出现的单词。

说明：拆分时可以重复使用字典中的单词。

示例1：
```
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
```

示例2：
```
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。
```

示例3：
```
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

提示：
- 1 <= s.length <= 300
- 1 <= wordDict.length <= 1000
- 1 <= wordDict[i].length <= 20
- s 和 wordDict[i] 仅有小写英文字母组成
- wordDict 中的所有字符串 互不相同


# 分析和解答

这个题在没见过之前想到dp还是挺难的，这里使用的是一维dp数组，其数组的含义是，当前这个位置下，能否被拆分成多个字典中的词

dp的状态转移方程想法是，从这个位置之前逐个遍历
- 要么是0到这个位置能构成一个单词
- 要么是之前一个能构成字典中单词的位置，到现在这个位置之间的词也在字典中

重点也是要特殊处理如下j==0的情况，另外列表截取的左闭右开

这个题要用dp的思路，这个感觉是最难想的，dp的每个位置代表当前位置，是否所有单词都能包含在词典中；；；对于一个位置，如果之前出现过能包含在词典中的（dp[j]==True），并且j到i（[j+1: i+1]）也在词典里，那么这个位置就是在的；；；注意按照这种思路，可能需要处理一些0的特殊情况

```python
class Solution(object):
    def wordBreak(self, s, wordDict):
        """
        :type s: str
        :type wordDict: List[str]
        :rtype: bool
        """
        
        # 这个题很难看出是dp的思路了，一眼看过去还以为是Trie树，KMP这样的
        # dp数组代表的含义是，当前这个位置下，[能否](bool)被拆分成多个字典词
        if len(s) == 0:
            return False



        dp = [False for i in range(len(s))]  # 当前这个位置下，能否全部在词典中出现
        for i in range(len(s)):    
            # 如果i为0的情况
            if i == 0:
                if s[i] in wordDict:
                    dp[i] = True            
            else:
                # 对于一个位置判断是否能全部在词典中出现，等于前一个能出现的位置 and 那个位置到这个位置之间能不能出现
                for j in range(i):
                    if j == 0 and s[: i+1] in wordDict:
                        dp[i] = True
                        break
                    # j == 0没法走到这个情况
                    if dp[j] is True and s[j+1: i+1] in wordDict:
                        dp[i] = True
                        break
        # print(dp)
        return dp[len(s)-1]
```