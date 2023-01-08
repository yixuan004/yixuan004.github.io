---
title: 276场LeetCode周赛
date: 2022-01-16 17:27:04
tags:
    - 字符串
    - 模拟
    - 贪心
    - 数学
    - 数组
    - 动态规划
    - EASY
    - MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

前两题难度还是比较低的，第三题的话感觉dp这个思路还值得复盘研究一下，常见的一维dp数组感觉是有两种思路，一种是存储当前位置的一些状态值，另外一种是存储截止到这个位置下的状态值；

第一题：简单字符串模拟，有一些mod的使用，熟练度的题目；

第二题：从大到小贪心就行了，最后一次直接 += target - 1；

第三题：一眼看上去很像背包，这种题多半就是dp了，就像总结里说的，常见的一维dp感觉是有两种思路，一种是存储当前位置的一些状态值，另外一种是存储截止到这个位置下的状态值，这个题就是后边一种思路，如果使用第一种思路实现的话会因为多次使用```max()```而TLE，所以在优化方向上应该也找到后边一种思路的优化方向；

第四题：未来有机会再试了TAT（周常鸽第四题）；

<!--more-->

# 第一题：5980.将字符串拆分为若干长度为k的组

[题目链接](https://leetcode-cn.com/problems/divide-a-string-into-groups-of-size-k/)

## 题目大意

字符串```s```可以按下述步骤划分为若干长度为```k```的组：
- 第一组由字符串中的前```k```个字符组成，第二组由接下来的```k```个字符串组成，依此类推。每个字符都能够成为**某一个**组的一部分。
- 对于最后一组，如果字符串剩下的字符**不足**```k```个，需使用字符```fill```来补全这一组字符。

注意，在去除最后一个组的填充字符```fill```（如果存在的话）并按顺序连接所有的组后，所得到的字符串应该是```s```。

给你一个字符串```s```，以及每组的长度```k```和一个用于填充的字符```fill```，按上述步骤处理之后，返回一个字符串数组，该数组表示```s```分组后**每个组的组成情况**。

示例1：
```
输入：s = "abcdefghi", k = 3, fill = "x"
输出：["abc","def","ghi"]
解释：
前 3 个字符是 "abc" ，形成第一组。
接下来 3 个字符是 "def" ，形成第二组。
最后 3 个字符是 "ghi" ，形成第三组。
由于所有组都可以由字符串中的字符完全填充，所以不需要使用填充字符。
因此，形成 3 组，分别是 "abc"、"def" 和 "ghi" 。
```

示例2：
```
输入：s = "abcdefghij", k = 3, fill = "x"
输出：["abc","def","ghi","jxx"]
解释：
与前一个例子类似，形成前三组 "abc"、"def" 和 "ghi" 。
对于最后一组，字符串中仅剩下字符 'j' 可以用。为了补全这一组，使用填充字符 'x' 两次。
因此，形成 4 组，分别是 "abc"、"def"、"ghi" 和 "jxx" 。
```

提示：
- 1 <= s.length <= 100
- s 仅由小写英文字母组成
- 1 <= k <= 100
- fill 是一个小写英文字母

## 分析和解答

简单的字符串模拟，需要比较熟练地使用mod

```python
class Solution(object):
    def divideString(self, s, k, fill):
        """
        :type s: str
        :type k: int
        :type fill: str
        :rtype: List[str]
        """
        return_list = []
        
        s_list = list(s)
        if len(s_list) % k != 0:
            s_list += [fill for _ in range(k - len(s_list) % k)]
        print(s_list)
        
        i = 0
        while i < len(s_list):
            return_list.append("".join(s_list[i: i+k]))
            i += k
        return return_list
```

# 第二题：5194.得到目标值的最少行动次数

[题目链接](https://leetcode-cn.com/problems/minimum-moves-to-reach-target-score/)

## 题目大意

你正在玩一个整数游戏。从整数```1```开始，期望得到整数```target```。

在一次行动中，你可以做下述两种操作之一：

- 递增，将当前整数的值加 1（即，```x = x + 1```）。
- 加倍，使当前整数的值翻倍（即，```x = 2 * x```）。

在整个游戏过程中，你可以使用**递增**操作**任意**次数。但是只能使用**加倍**操作 至多```maxDoubles```次。

给你两个整数```target```和```maxDoubles```，返回从 1 开始得到```target```需要的最少行动次数。

示例1：
```
输入：target = 5, maxDoubles = 0
输出：4
解释：一直递增 1 直到得到 target 。
```

示例2：
```
输入：target = 19, maxDoubles = 2
输出：7
解释：最初，x = 1 。
递增 3 次，x = 4 。
加倍 1 次，x = 8 。
递增 1 次，x = 9 。
加倍 1 次，x = 18 。
递增 1 次，x = 19 。
```

示例3：
```
输入：target = 10, maxDoubles = 4
输出：4
解释：
最初，x = 1 。 
递增 1 次，x = 2 。 
加倍 1 次，x = 4 。 
递增 1 次，x = 5 。 
加倍 1 次，x = 10 。
```

提示：
- 1 <= target <= 109
- 0 <= maxDoubles <= 100

## 分析和解答

贪心，还是比较容易看出来的

```python
class Solution(object):
    def minMoves(self, target, maxDoubles):
        """
        :type target: int
        :type maxDoubles: int
        :rtype: int
        """
        
        cnt = 0
        while maxDoubles != 0 and target != 1:
            if target % 2 != 0:
                target -= 1
                cnt += 1
            else:
                target //= 2
                cnt += 1
                maxDoubles -= 1
        
        cnt += target - 1
        
        return cnt
```


# 第三题：5982.解决智力问题

[题目链接](https://leetcode-cn.com/problems/solving-questions-with-brainpower/)

## 题目大意

给你一个下标从**0**开始的二维整数数组```questions```，其中```questions[i] = [pointsi, brainpoweri]```。

这个数组表示一场考试里的一系列题目，你需要**按顺序**（也就是从问题```0```开始依次解决），针对每个问题选择**解决**或者**跳过**操作。解决问题```i```将让你**获得**```pointsi```的分数，但是你将**无法**解决接下来的```brainpoweri```个问题（即只能跳过接下来的```brainpoweri```个问题）。如果你跳过问题```i```，你可以对下一个问题决定使用哪种操作。

- 比方说，给你```questions = [[3, 2], [4, 3], [4, 4], [2, 5]]```：
  - 如果问题```0```被解决了， 那么你可以获得```3```分，但你不能解决问题```1```和```2```。
  - 如果你跳过问题```0```，且解决问题```1```，你将获得```4```分但是不能解决问题```2```和```3```。

请你返回这场考试里你能获得的**最高**分数。

示例1：
```
输入：questions = [[3,2],[4,3],[4,4],[2,5]]
输出：5
解释：解决问题 0 和 3 得到最高分。
- 解决问题 0 ：获得 3 分，但接下来 2 个问题都不能解决。
- 不能解决问题 1 和 2
- 解决问题 3 ：获得 2 分
总得分为：3 + 2 = 5 。没有别的办法获得 5 分或者多于 5 分。
```

示例2：
```
输入：questions = [[1,1],[2,2],[3,3],[4,4],[5,5]]
输出：7
解释：解决问题 1 和 4 得到最高分。
- 跳过问题 0
- 解决问题 1 ：获得 2 分，但接下来 2 个问题都不能解决。
- 不能解决问题 2 和 3
- 解决问题 4 ：获得 5 分
总得分为：2 + 5 = 7 。没有别的办法获得 7 分或者多于 7 分。
```

提示：
- 1 <= questions.length <= 105
- questions[i].length == 2
- 1 <= pointsi, brainpoweri <= 105

## 分析和解答

题目的名字好有意思哈哈哈哈，这是按时解决一下我的智力问题吗23333

一眼看上去很像背包，这种题多半就是dp了，就像总结里说的，常见的一维dp感觉是有两种思路，一种是存储当前位置的一些状态值，另外一种是存储截止到这个位置下的状态值，这个题就是后边一种思路，如果使用第一种思路实现的话会因为多次使用```max()```而TLE，所以在优化方向上应该也找到后边一种思路的优化方向；

TLE的写法，这里dp数组存储的是每个状态的：
```python
class Solution(object):
    def mostPoints(self, questions):
        """
        :type questions: List[List[int]]
        :rtype: int
        """
        questions.reverse()
        dp = [x[0] for x in questions]  # 每个位置上先初始化
        
        
        for i in range(len(questions)):  # 每个位置有选得到和选不到两种状态的dp
            if i - questions[i][1] - 1 < 0:
                pass
            else:
                # 选和不选两种状态
                # 选的话
                tmp_choose = questions[i][0] + max(dp[0:i-questions[i][1]])
                # 不选的话
                tmp_not_choose = max(dp[i-questions[i][1]: i])
                dp[i] = max(tmp_choose, tmp_not_choose)
                
                # print(dp)

        return max(dp)
```

优化后的写法， 这里dp数组存储的是截止到这个状态的，下图是思路：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-01-16-21-16-46.png)
```python
class Solution(object):
    def mostPoints(self, questions):
        """
        :type questions: List[List[int]]
        :rtype: int
        """
        
        dp = [0 for x in questions]
        questions.reverse()
        
        for i in range(len(questions)):
            if i - questions[i][1] - 1 < 0:  # 越界了的状态，维护不断增加的dp
                dp[i] = max(dp[i-1], questions[i][0])
            else:  # 没有越界的话，因为不断增加
                dp[i] = max(dp[i-1], questions[i][0] + dp[i - questions[i][1] - 1])
        
        # print(max(dp))
        
        return dp[len(questions) - 1]
```