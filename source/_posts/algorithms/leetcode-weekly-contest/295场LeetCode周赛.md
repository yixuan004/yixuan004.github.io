---
title: 295场LeetCode周赛
date: 2022-06-19 17:42:21
tags:
    - 哈希表
    - 字符串
    - 计数
    - EASY
    - MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

题目难度：t1(1299)+t2(1577)；

这周好像是因为从学校回家还是怎么的，只后来虚拟竞赛做了前两个题了，之后看第三题的难度达到了惊人的2100+，可能正好躲开了一次坐牢吧；

第一题：简单字典计数；

第二题：这个题加了很多限制，开始想用正则表达式整花活整了半天，最后发现还是模拟来得好。。。；

第三题：咕咕QAQ；

第四题：咕咕QAQ；

<!--more-->

# 第一题：2287.重排字符形成目标字符串【难度：1299】

[题目链接](https://leetcode.cn/problems/rearrange-characters-to-make-target-string/)

## 题目大意

给你两个下标从 **0** 开始的字符串 `s` 和 `target` 。你可以从 `s` 取出一些字符并将其重排，得到若干新的字符串。

从 `s` 中取出字符并重新排列，返回可以形成 `target` 的 **最大** 副本数。

示例1：
```
输入：s = "ilovecodingonleetcode", target = "code"
输出：2
解释：
对于 "code" 的第 1 个副本，选取下标为 4 、5 、6 和 7 的字符。
对于 "code" 的第 2 个副本，选取下标为 17 、18 、19 和 20 的字符。
形成的字符串分别是 "ecod" 和 "code" ，都可以重排为 "code" 。
可以形成最多 2 个 "code" 的副本，所以返回 2 。
```

示例2
```
输入：s = "abcba", target = "abc"
输出：1
解释：
选取下标为 0 、1 和 2 的字符，可以形成 "abc" 的 1 个副本。 
可以形成最多 1 个 "abc" 的副本，所以返回 1 。
注意，尽管下标 3 和 4 分别有额外的 'a' 和 'b' ，但不能重用下标 2 处的 'c' ，所以无法形成 "abc" 的第 2 个副本。
```

示例3：
```
输入：s = "abbaccaddaeea", target = "aaaaa"
输出：1
解释：
选取下标为 0 、3 、6 、9 和 12 的字符，可以形成 "aaaaa" 的 1 个副本。
可以形成最多 1 个 "aaaaa" 的副本，所以返回 1 。
```

提示：
- `1 <= s.length <= 100`
- `1 <= target.length <= 10`
- `s` 和 `target` 由小写英文字母组成

## 分析和解答

简单字典计数；

```python
class Solution(object):
    def rearrangeCharacters(self, s, target):
        """
        :type s: str
        :type target: str
        :rtype: int
        """
        cnt_s = Counter(s)
        cnt_target = Counter(target)
        
        res = 1e9
        for key, value in cnt_target.items():
            res = min(res, cnt_s[key]//value)
        
        return res
```


# 第二题：2288.价格减免【难度：1577】

[题目链接](https://leetcode.cn/problems/apply-discount-to-prices/)

## 题目大意

句子 是由若干个单词组成的字符串，单词之间用单个空格分隔，其中每个单词可以包含数字、小写字母、和美元符号 `'$'` 。如果单词的形式为美元符号后跟着一个非负实数，那么这个单词就表示一个价格。
- 例如 `"$100"`、`"$23"` 和 `"$6.75"` 表示价格，而 `"100"`、`"$"` 和 `"2$3"` 不是。

**注意**：本题输入中的价格均为整数。

给你一个字符串 `sentence`  和一个整数 `discount` 。对于每个表示价格的单词，都在价格的基础上减免 `discount%` ，并 **更新** 该单词到句子中。所有更新后的价格应该表示为一个 **恰好保留小数点后两位** 的数字。

返回表示修改后句子的字符串。

示例1：
```
输入：sentence = "there are $1 $2 and 5$ candies in the shop", discount = 50
输出："there are $0.50 $1.00 and 5$ candies in the shop"
解释：
表示价格的单词是 "$1" 和 "$2" 。 
- "$1" 减免 50% 为 "$0.50" ，所以 "$1" 替换为 "$0.50" 。
- "$2" 减免 50% 为 "$1" ，所以 "$1" 替换为 "$1.00" 。
```

示例2：
```
输入：sentence = "1 2 $3 4 $5 $6 7 8$ $9 $10$", discount = 100
输出："1 2 $0.00 4 $0.00 $0.00 7 8$ $0.00 $10$"
解释：
任何价格减免 100% 都会得到 0 。
表示价格的单词分别是 "$3"、"$5"、"$6" 和 "$9"。
每个单词都替换为 "$0.00"。
```

提示：
- `1 <= sentence.length <= 10^5`
- `sentence` 由小写英文字母、数字、`' '` 和 `'$'` 组成
- `sentence` 不含前导和尾随空格
- `sentence` 的所有单词都用单个空格分隔
- 所有价格都是 **正** 整数且不含前导零
- 所有价格 **最多** 为  `10` 位数字
- `0 <= discount <= 100`

## 分析和解答

这个题加了很多限制，开始想用正则表达式整花活整了半天，最后发现还是模拟来得好。。。；

P.S. 借这个题想说好讨厌这种描述又长，又带着小数的题啊，感觉不像是正路的题目；

```python
class Solution(object):
    def discountPrices(self, sentence, discount):
        """
        :type sentence: str
        :type discount: int
        :rtype: str
        """
        
        tmp_list = sentence.split()
        res_list = []
        for i in range(len(tmp_list)):

            if tmp_list[i][0] == '$':
                try:
                    tmp = int(tmp_list[i][1:])
                    tmp = tmp - (tmp * discount * 1.0 /100)
                    tmp = '{:.2f}'.format(tmp)
                    tmp = '$' + tmp
                    res_list.append(tmp)
                except:
                    res_list.append(tmp_list[i])
            else:
                res_list.append(tmp_list[i])
        
        return ' '.join(res_list)
```