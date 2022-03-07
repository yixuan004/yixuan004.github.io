---
title: 39.组合总和-python
date: 2022-03-07 20:16:26
tags:
    - 数组
    - 回溯
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

39.组合总和（中等）

题目大意：

给你一个 **无重复元素** 的整数数组 ```candidates``` 和一个目标整数 ```target``` ，找出 ```candidates``` 中可以使数字和为目标数 ```target``` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。

```candidates``` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 ```target``` 的不同组合数少于 ```150``` 个。

<!--more-->

# 题目

给你一个 **无重复元素** 的整数数组 ```candidates``` 和一个目标整数 ```target``` ，找出 ```candidates``` 中可以使数字和为目标数 ```target``` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。

```candidates``` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 ```target``` 的不同组合数少于 ```150``` 个。

示例1：
```
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
7 也是一个候选， 7 = 7 。
仅有这两种组合。
```

示例2：
```
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```

示例3：
```
输入: candidates = [2], target = 1
输出: []
```

提示：
- ```1 <= candidates.length <= 30```
- ```1 <= candidates[i] <= 200```
- ```candidate``` 中的每个元素都 **互不相同**
- ```1 <= target <= 500```

# 分析和解答

这个题是个经典的回溯题，首先想一个dfs的构造：

- 每次要把现在的now_sum传进去，代表dfs到每一层的时候现在加到多少了，用来进行出口条件的判断；
- 每次要判断一个位置选还是不选，这样的话假设一个数组长度是n，每次要把现在到第几个n传递下去，而因为是dfs，每次不需要传递i+1，还是可以传递i的；
- 每次要把list给加上，代表dfs的时候，现在选了哪些了，然后在第一个条件如果到出口的话，就要把list append进去；

```dfs```的开始状态，按照这个思路就是```0，0，[]```，然后出口条件如果等于则append列表，如果大于则```return```，之后走新的```dfs```条件，每次i从当前的```now_idx```取到```n```，每个状态都生成一个分支，这里可以进行一个剪枝，如果超过了就```break```掉，然后这样的话就可以开展第一轮```dfs```了，先把选这个```i```位置处的```append```进去，然后用```copy.deepcopy()```传一下到下一层，然后再```list = list[:-1]```把这个去除掉

**另外特别注意，这个题的数组需要先排序一下，否则循环剪枝那里可能后边还有更小的就break了不会被放进来了**

```python
res = []
def dfs(now_sum, now_idx, now_list):
    if now_sum == target:
        res.append(now_list)
    if now_sum > target:
        return
    
    for i in range(now_idx, len(candidates)):
        if now_sum + candidates[i] > target:
            break
        now_list.append(candidates[i])
        dfs(now_sum + candidates[i], i, copy.deepcopy(now_list))  # 注意这里要deepcopy
        now_list = now_list[:-1]  # 截断下
```


当时写的代码如下：
```python
class Solution(object):
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        candidates.sort()  # 先排个序
        res = []
        # 递归要找出口，一个条件一个条件的加入到参数中
        def dfs(now_sum, tmp_list, now_idx):
            if now_sum == target:
                # 需要解决一个去重问题，多加一个位标记，代表只能从后边的开始选了，比如说开始选择了3之后，就不能再开始选前边的2了
                res.append(tmp_list)
                return
            elif now_sum > target:  # 加爆了，不满足return
                return
            else:
                for i in range(now_idx, len(candidates)):
                    if now_sum + candidates[i] <= target:
                        tmp_list.append(candidates[i])
                        dfs(now_sum + candidates[i], copy.deepcopy(tmp_list), i)  # 下一次这个now_idx只能从当前的i之后开始了，这样解决重复问题
                        tmp_list = tmp_list[:-1]  # 回溯
                    else:
                        break

        dfs(0, [], 0)
        return res
```