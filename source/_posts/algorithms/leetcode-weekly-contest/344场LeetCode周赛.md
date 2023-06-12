---
title: 344场LeetCode周赛
date: 2023-06-11 21:00:32
tags:
	- EASY
	- MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

看了看自己的账号记录，好像一下有几十场LeetCode周赛没有打了，再看看LeetCode这边的参赛人数，从当时参加比较多的6 7千人，变得可能只有3 4千人了。想想自己打周赛和不打周赛的原因？开始打周赛就是在室友的带领下有兴趣就自己也跟着玩玩，后来知道了有Knight、Guardian这些段位后，也想证明一下自己的算法能力？从大一没入学开始就一直挺爱好这些的，第一次知道有直接交了马上就能出答案的比赛类型，有点喜欢竞技的自己就爱上了这种模式。后来上了knight后又打了好一阵周赛，可能也是为了追人吧hhh，在一起和分手这段时间就成了自己的一个算法空白期，再加上还怕自己好不容易上的knight掉分。现在重新回来看，掉分有什么可怕的呢，就是一个显示而已别人也不会在意，反而自己不敢参加，疏离自己最爱的这些，才是一种没信心的表现吧，就从现在开始，重新出发吧！

![image-20230611212029571](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230611212029571.png)

第一题：作为t1基本就是按照题目的意思遍历，可以直接使用python的截断比较方便，然后不考虑复杂度直接list(set(...))

第二题：设计一个类的这种题，这个题主要是一个正向mapping和一个反向mapping，正向map是每个数字是多少次频率，反向map是频率下出现了多少次数字，按照题目的要求一步步实现基本就可以

第三题：更新前后进行一些判断，太久没看这个题了，现在有点忘了当时怎么做的，但好像是顺着思路比较好想的那种题

第四题：g

<!--more-->

# 第一题：2670. 找出不同元素数目差数组

https://leetcode.cn/contest/weekly-contest-344/problems/find-the-distinct-difference-array/

## 题目大意

![image-20230611213245181](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230611213245181.png)

## 分析和解答

作为t1基本就是按照题目的意思遍历，可以直接使用python的截断比较方便，然后不考虑复杂度直接list(set(...))

```python
class Solution:
    def distinctDifferenceArray(self, nums: List[int]) -> List[int]:
        res = []
        for i in range(len(nums)):
            a = nums[:i+1]
            b = nums[i+1:]
            # print(a, b)
            res.append(len(set(a)) - len(set(b)))
        return res
```

# 第二题：2671. 频率跟踪器

https://leetcode.cn/contest/weekly-contest-344/problems/frequency-tracker/

## 题目大意

![image-20230611213400834](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230611213400834.png)

![image-20230611213417553](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230611213417553.png)

## 分析和解答

设计一个类的这种题，这个题主要是一个正向mapping和一个反向mapping，正向map是每个数字是多少次频率，反向map是频率下出现了多少次数字，按照题目的要求一步步实现基本就可以

```python
from collections import defaultdict

class FrequencyTracker:

    def __init__(self):
        self.num_to_feq = defaultdict(int)  # 每个数字是多少次频率
        self.feq_to_nums = defaultdict(int)  # 频率下，出现了多少次数字，要是唯一的那种干那句

    def add(self, number: int) -> None:
        # 数值更新
        self.num_to_feq[number] += 1
        # 频率更新
        self.feq_to_nums[self.num_to_feq[number]] += 1
        self.feq_to_nums[self.num_to_feq[number]-1] -= 1
        return 
            
    def deleteOne(self, number: int) -> None:
        if self.num_to_feq[number] <= 0:
            return
        self.num_to_feq[number] -= 1
        # 频率更新
        self.feq_to_nums[self.num_to_feq[number]] += 1
        self.feq_to_nums[self.num_to_feq[number]+1] -= 1

    def hasFrequency(self, frequency: int) -> bool:
        if self.feq_to_nums[frequency] > 0:
            return True
        else:
            return False


# Your FrequencyTracker object will be instantiated and called as such:
# obj = FrequencyTracker()
# obj.add(number)
# obj.deleteOne(number)
# param_3 = obj.hasFrequency(frequency)
```

## 第三题：2672. 有相同颜色的相邻元素数目

https://leetcode.cn/contest/weekly-contest-344/problems/number-of-adjacent-elements-with-the-same-color/

## 题目大意

![image-20230611214058014](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230611214058014.png)

## 分析和解答

更新前后进行一些判断，太久没看这个题了，现在有点忘了当时怎么做的，但好像是顺着思路比较好想的那种题

```python
class Solution:
    def colorTheArray(self, n: int, queries: List[List[int]]) -> List[int]:
        color = [0 for _ in range(n)]
        prev_diff_count = 0
        res = []
        for i in range(len(queries)):
            # 不更新特判，直接弹出
            if color[queries[i][0]] == queries[i][1]:
                res.append(prev_diff_count)
                continue
            
            # 和前后判断
            prev_equ = 0
            idx = queries[i][0]
            if idx - 1 >= 0 and color[idx] == color[idx-1] and color[idx] != 0:
                prev_equ += 1
            if idx + 1 < n and color[idx] == color[idx+1] and color[idx] != 0:
                prev_equ += 1
            prev_diff_count -= prev_equ
            
            # 更新后和前后判断
            prev_equ2 = 0
            color[idx] = queries[i][1]
            if idx - 1 >= 0 and color[idx] == color[idx-1] and color[idx] != 0:
                prev_equ2 += 1
            if idx + 1 < n and color[idx] == color[idx+1] and color[idx] != 0:
                prev_equ2 += 1
            prev_diff_count += prev_equ2
        
            res.append(prev_diff_count)
        
        return res
```

