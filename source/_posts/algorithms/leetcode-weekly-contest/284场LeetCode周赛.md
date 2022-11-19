---
title: 284场LeetCode周赛
date: 2022-03-13 21:43:04
tags:
    - 数组
    - 哈希表
    - 模拟
    - 贪心
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

这周三个题还是相对友善一点点的：第一题读题有些困难hhh读好了一些角标标记的操作就可以了；第二题感觉一下想过去就是个哈希表记录，感觉也是状态好才能现场写出来吧；第三题需要考虑很多种case是个比较细节的题，感觉还是模拟为主。总结来说这周还都属于是一下想过去有思路的题，希望未来能进一步保持啊啊啊啊啊；

第一题：角标的基础题，感觉读明白题后很快就做出来了，注意需要一个绝对值 ```abs``` 就可以了；

第二题：一眼看过去大概就能识别到是一个需要记录的题，主要是一些反向哈希的策略，实际上只要在写的时候不断完善程序逻辑基本就能做出来了，注意字典的key可以是元组（tuple）；

第三题：这个题感觉还是case比较多，感觉自己还是一种模拟的思路吧，感觉通过这个题积累的一些周赛思路是有些边界条件或者极端case完全是可以单独处理的（比如这个题数组长度为1的时候），尝试融合到通用case里反而会增加难度，另外看这个题的做题思路可能和其他人不太一样，好像有更加数学的解法。另外实际上是看到 ```nums``` 的长度是 ```10^5``` 的时候就感觉可以模拟了，然后 ```k``` 比这个大就说明可能会有一部分相对特殊的case；

第四题：未来有机会再试了TAT（周常鸽第四题）；

<!--more-->

# 第一题：6031.找出数组中的所有 K 近邻下标

[题目链接](https://leetcode-cn.com/problems/find-all-k-distant-indices-in-an-array/)

## 题目大意

给你一个下标从 **0** 开始的整数数组 ```nums``` 和两个整数 ```key``` 和 ```k``` 。**K 近邻下标** 是 ```nums``` 中的一个下标 ```i``` ，并满足至少存在一个下标 ```j``` 使得 ```|i - j| <= k``` 且 ```nums[j] == key``` 。

以列表形式返回按 **递增顺序** 排序的所有 K 近邻下标。

示例1：
```
输入：nums = [3,4,9,1,3,9,5], key = 9, k = 1
输出：[1,2,3,4,5,6]
解释：因此，nums[2] == key 且 nums[5] == key 。
- 对下标 0 ，|0 - 2| > k 且 |0 - 5| > k ，所以不存在 j 使得 |0 - j| <= k 且 nums[j] == key 。所以 0 不是一个 K 近邻下标。
- 对下标 1 ，|1 - 2| <= k 且 nums[2] == key ，所以 1 是一个 K 近邻下标。
- 对下标 2 ，|2 - 2| <= k 且 nums[2] == key ，所以 2 是一个 K 近邻下标。
- 对下标 3 ，|3 - 2| <= k 且 nums[2] == key ，所以 3 是一个 K 近邻下标。
- 对下标 4 ，|4 - 5| <= k 且 nums[5] == key ，所以 4 是一个 K 近邻下标。
- 对下标 5 ，|5 - 5| <= k 且 nums[5] == key ，所以 5 是一个 K 近邻下标。
- 对下标 6 ，|6 - 5| <= k 且 nums[5] == key ，所以 6 是一个 K 近邻下标。
因此，按递增顺序返回 [1,2,3,4,5,6] 。
```

示例2：
```
输入：nums = [2,2,2,2,2], key = 2, k = 2
输出：[0,1,2,3,4]
解释：对 nums 的所有下标 i ，总存在某个下标 j 使得 |i - j| <= k 且 nums[j] == key ，所以每个下标都是一个 K 近邻下标。 
因此，返回 [0,1,2,3,4] 。
```

提示：
- ```1 <= nums.length <= 1000```
- ```1 <= nums[i] <= 1000```
- ```key``` 是数组 ```nums``` 中的一个整数
- ```1 <= k <= nums.length```

## 分析和解答

角标的基础题，感觉读明白题后很快就做出来了，注意需要一个绝对值 ```abs``` 就可以了

```python
class Solution(object):
    def findKDistantIndices(self, nums, key, k):
        """
        :type nums: List[int]
        :type key: int
        :type k: int
        :rtype: List[int]
        """
        
        tmp = []
        for i in range(len(nums)):
            if nums[i] == key:
                tmp.append(i)
        print(tmp)

        res = []
        for i in range(len(nums)):
            for j in range(len(tmp)):
                if abs(i - tmp[j]) <= k:
                    print("i, j: ", i,j)
                    res.append(i)
                    break
        # print(res)
        return res
```


# 第二题：5203.统计可以提取的工件

[题目链接](https://leetcode-cn.com/problems/count-artifacts-that-can-be-extracted/)

## 题目大意

存在一个 ```n x n``` 大小、下标从 **0** 开始的网格，网格中埋着一些工件。给你一个整数 ```n``` 和一个下标从 **0** 开始的二维整数数组 ```artifacts``` ，```artifacts``` 描述了矩形工件的位置，其中 ```artifacts[i] = [r1i, c1i, r2i, c2i]``` 表示第 ```i``` 个工件在子网格中的填埋情况：

- ```(r1i, c1i)``` 是第 ```i``` 个工件 **左上** 单元格的坐标，且
- ```(r2i, c2i)``` 是第 ```i``` 个工件 **右下** 单元格的坐标。

你将会挖掘网格中的一些单元格，并清除其中的填埋物。如果单元格中埋着工件的一部分，那么该工件这一部分将会裸露出来。如果一个工件的所有部分都都裸露出来，你就可以提取该工件。

给你一个下标从 **0** 开始的二维整数数组 ```dig``` ，其中 ```dig[i] = [ri, ci]``` 表示你将会挖掘单元格 ```(ri, ci)``` ，返回你可以提取的工件数目。

生成的测试用例满足：

- 不存在重叠的两个工件。
- 每个工件最多只覆盖 ```4``` 个单元格。
- ```dig``` 中的元素互不相同。


示例1：

![](/images/2022-03-13-22-25-47.png)
```
输入：n = 2, artifacts = [[0,0,0,0],[0,1,1,1]], dig = [[0,0],[0,1]]
输出：1
解释： 
不同颜色表示不同的工件。挖掘的单元格用 'D' 在网格中进行标记。
有 1 个工件可以提取，即红色工件。
蓝色工件在单元格 (1,1) 的部分尚未裸露出来，所以无法提取该工件。
因此，返回 1 。
```

示例2：

![](/images/2022-03-13-22-26-06.png)
```
输入：n = 2, artifacts = [[0,0,0,0],[0,1,1,1]], dig = [[0,0],[0,1],[1,1]]
输出：2
解释：红色工件和蓝色工件的所有部分都裸露出来（用 'D' 标记），都可以提取。因此，返回 2 。 
```

提示：
- ```1 <= n <= 1000```
- ```1 <= artifacts.length, dig.length <= min(n^2, 10^5)```
- ```artifacts[i].length == 4```
- ```dig[i].length == 2```
- ```0 <= r1i, c1i, r2i, c2i, ri, ci <= n - 1```
- ```r1i <= r2i```
- ```c1i <= c2i```
- 不存在重叠的两个工件
- 每个工件 **最多** 只覆盖 ```4``` 个单元格
- ```dig``` 中的元素互不相同

## 分析和解答

一眼看过去大概就能识别到是一个需要记录的题，主要是一些反向哈希的策略，实际上只要在写的时候不断完善程序逻辑基本就能做出来了，注意字典的key可以是元组（tuple）

```python
class Solution(object):
    def digArtifacts(self, n, artifacts, dig):
        """
        :type n: int
        :type artifacts: List[List[int]]
        :type dig: List[List[int]]
        :rtype: int
        """
        
        # 哈希表记录每个地方的状态
        
        # 要用mark吗，还是说看起来直接暴力就行了
        reverse_mapping = {}
        d = [0 for i in range(len(artifacts))]
        m = [[-1 for i in range(n)] for j in range(n)]
        for i in range(len(artifacts)):
            r1, c1, r2 ,c2 = artifacts[i]
            d[i] = 0  # mapping
            for j in range(r1, r2+1):
                for k in range(c1, c2+1):
                    m[j][k] = 1
                    # 反向mapping，这个工件属于第几个
                    reverse_mapping[(j, k)] = i  # jk角标的地方属于第i个工件
                    d[i] += 1
        #print("d: ", d)
        #print("m: ", m)
        #print("reverse_mapping: ", reverse_mapping)
    
    
        
        for i in range(len(dig)):
            r, c = dig[i]
            # 先判断这个地方是不是有
            if m[r][c] == 1:
                # 再判断是第几个零件
                index = reverse_mapping[(r, c)]
                # 减去第几个零件
                d[index] -= 1
                
                m[r][c] = 0  # 挖掉
            else:
                continue
        
        res = 0
        #print(d)
        for i in range(len(d)):
            if d[i] == 0:
                res += 1
        return res
```


# 第三题：5227.K 次操作后最大化顶端元素

[题目链接](https://leetcode-cn.com/problems/maximize-the-topmost-element-after-k-moves/)

## 题目大意

给你一个下标从 **0** 开始的整数数组 ```nums``` ，它表示一个 **栈** ，其中 ```nums[0]``` 是栈顶的元素。

每一次操作中，你可以执行以下操作 **之一** ：

- 如果栈非空，那么 **删除** 栈顶端的元素。
- 如果存在 1 个或者多个被删除的元素，你可以从它们中选择任何一个，**添加** 回栈顶，这个元素成为新的栈顶元素。

同时给你一个整数 ```k``` ，它表示你总共需要执行操作的次数。

请你返回 **恰好** 执行 ```k``` 次操作以后，栈顶元素的 **最大值** 。如果执行完 ```k``` 次操作以后，栈一定为空，请你返回 ```-1``` 。

示例1：
```
输入：nums = [5,2,2,4,0,6], k = 4
输出：5
解释：
4 次操作后，栈顶元素为 5 的方法之一为：
- 第 1 次操作：删除栈顶元素 5 ，栈变为 [2,2,4,0,6] 。
- 第 2 次操作：删除栈顶元素 2 ，栈变为 [2,4,0,6] 。
- 第 3 次操作：删除栈顶元素 2 ，栈变为 [4,0,6] 。
- 第 4 次操作：将 5 添加回栈顶，栈变为 [5,4,0,6] 。
注意，这不是最后栈顶元素为 5 的唯一方式。但可以证明，4 次操作以后 5 是能得到的最大栈顶元素。
```

示例2：
```
输入：nums = [2], k = 1
输出：-1
解释：
第 1 次操作中，我们唯一的选择是将栈顶元素弹出栈。
由于 1 次操作后无法得到一个非空的栈，所以我们返回 -1 。
```

提示：
- ```1 <= nums.length <= 10^5```
- ```0 <= nums[i], k <= 10^9```

## 分析和解答

这个题感觉还是case比较多，感觉自己还是一种模拟的思路吧，感觉通过这个题积累的一些周赛思路是有些边界条件或者极端case完全是可以单独处理的（比如这个题数组长度为1的时候），尝试融合到通用case里反而会增加难度，另外看这个题的做题思路可能和其他人不太一样，好像有更加数学的解法。另外实际上是看到 ```nums``` 的长度是 ```10^5``` 的时候就感觉可以模拟了，然后 ```k``` 比这个大就说明可能会有一部分相对特殊的case；


```python
class Solution(object):
    def maximumTop(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        if k == 0:
            return nums[0]
        
        jilu_remove = []
        now_max = -1
        cnt_op = 0
        index = 0
        res = -1
        
        
        for i in range(len(nums)):  #
            if cnt_op == k - 1:
                # 判断是再拿一个的高，还是把加回去的高
                try:
                    nayige = nums[index+1]
                except:
                    nayige = -1
                
                cnt_op += 1
                res = max(now_max, nayige)
                
            
            elif cnt_op < k:  # 代表还能操作
                jilu_remove.append(nums[index])
                if nums[index] > now_max:
                    now_max = nums[index]
                index += 1
                cnt_op += 1
            else:
                break
        
        
        # 最后还有可能出现还剩很多的情况，因为len(nums)比k小，到时候要判断情况的
        left_times = k - cnt_op
        jilu_remove.sort(reverse=True)
        print("left_times: ", left_times)
        print("jilu_remove: ", jilu_remove)
        print("res: ", res)
        
        
        # 当剩余操作次数为left_times时
        left_times %= (2 * len(nums))
        print("left_times: ", left_times)
        
        if left_times <= len(nums) and left_times != 0:
            res = now_max
        
        
        if left_times > len(nums):
            if len(nums) == 1:
                if left_times % 2 != 0:  # 奇数
                    res = now_max
                else:  # 偶数
                    try:
                        res = jilu_remove[1]
                    except:
                        res = -1
            
            else:
                res = now_max
        
        return res
```