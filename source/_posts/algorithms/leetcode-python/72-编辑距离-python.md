---
title: 72.编辑距离-python
date: 2021-12-16 22:52:36
tags:
    - 字符串
    - 动态规划
    - HARD
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

72.编辑距离（困难）

题目大意：

给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

- 插入一个字符
- 删除一个字符
- 替换一个字符

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/edit-distance/)

给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

- 插入一个字符
- 删除一个字符
- 替换一个字符

示例1：
```
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

示例2：
```
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

提示：
- 0 <= word1.length, word2.length <= 500
- word1 和 word2 由小写英文字母组成

# 题目分析和解答

二维dp的题目，想象两个单词word1和word2，二维dp的每个位置上，代表word1的i位置处，和word2的j位置处情况下，多少次变换能达到是一样的情况；

个人习惯上把word1作为行来放置，把word2作为列来放置；

由于word1和word2都可能为空，所以在初始化二维dp数组的时候，行和列要分别是```len(word1)+1```和```len(word2)+1```，初始化dp数组的第一行第一列，想象i到j的这个顺序，第一列```dp[i][0]```代表word1在当前的情况下经过多少次操作能变成空，所以这个数值就等于i；第一行```dp[0][i]```就是代表空字符串经过多少次能变到word2的当前这个位置上。

现在初始化好了dp数组的第一行第一列，**下边就来想状态转移这个方程**，也是这个题目每次复习的时候需要想到的部分，```dp[i][j]```可以由```dp[i-1][j-1]，dp[i-1][j]，dp[i][j-1]```得到：

- ```dp[i-1][j-1]```到```dp[i][j]```的操作是修改，注意修改这个操作需要判断word1 i位置处的值和word2 j位置处的值是不是相等的，如果相等的话则不需要修改，如果是不相等的话则需要修改，也就是```dp[i-1][j-1]+1```；
- ```dp[i][j-1]```到```dp[i][j]```的操作是删除，想象最左上角的三个块，ij代表word1变到word2的这一种感觉。执行删除的情况下，状态转移后必然是```dp[i][j]=dp[i][j-1]+1```；
- ```dp[i-1][j]```到```dp[i][j]```的操作是新增，同样是想象最左上角的三个块。执行新增的情况下，状态转移后必然是```dp[i][j]=dp[i-1][j]+1```

经过如上分析后，在每次判断时先判断是不是相等，如果相等的话，可以走```dp[i][j]=dp[i-1][j-1]```，代表不需要修改。如果是不相等的话就需要对三种情况进行min判断，```dp[i][j]=min(dp[i-1][j-1], dp[i][j-1], dp[i-1][j]) + 1```

经过上述循环后，因为在最开始前边补充的字符串为空的长度情况，所以```dp[len(word1)][len(word2)]```就可以了。

```python
class Solution(object):
    def minDistance(self, word1, word2):
        """
        :type word1: str
        :type word2: str
        :rtype: int
        """
        
        """
        二维dp的题目，dp数组代表，word1的前i个字符，和word2的前j个字符，需要几次修改可以变成一样的
        回想题解里给出的那张图，要分别想到word1，word2为空字符串的情况
        空串情况下的初始化状态比较容易想，一个字符串变成空串，就是这个字符串的长度的次数
        """

        # 初始化dp数组状态，word1在行维度上，word2在列维度上
        dp = [[0 for i in range(len(word2)+1)] for j in range(len(word1)+1)]  # 注意深浅拷贝的问题，这个还挺坑的，只能这么初始化数组
        for i in range(len(word1)+1):
            dp[i][0] = i
        for i in range(len(word2) + 1):
            dp[0][i] = i
        

        # 假设每个地方想象的是从竖着的word1变到横着的word2的状态，那么[i-1][j-1]代表替换，[i-1][j]代表插入，[i][j-1]代表删除（想初始状态即可）
        # 如果当前两个字符相等的情况下，保持不变（即不做修改[i-1][j-1]的状态）一定是最优的，因为无论增加或减少，则一定还需要1次操作
        # 而如果当前两个字符不等的情况，则由删除，增加，修改三种之最小的+1次操作得到
        for i in range(1, len(word1)+1):
            for j in range(1, len(word2)+1):
                if word1[i-1] == word2[j-1]:
                    dp[i][j] = dp[i-1][j-1]
                else:
                    dp[i][j] = min(dp[i-1][j-1], dp[i][j-1], dp[i-1][j]) +1
        
        return dp[len(word1)][len(word2)]
```