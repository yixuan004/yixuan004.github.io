---
title: 289场LeetCode周赛
date: 2022-04-18 18:03:00
tags:
    - 字符串
    - 模拟
    - 贪心
    - 数组
    - 哈希表
    - 计数
    - 矩阵
    - 前缀和
    - EASY
    - MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

这次周赛之后补的没有按时间做了，总体来说的话用到的数据结构和方法都是周赛常见的，前缀还有Counter这些的。第四题根据大佬的说法是个树形DP，其实应该看一看的但是事情比较多只能把第四题鸽了；

第二题这种有点类似数学的题，还有最近的聊天来看又让自己想起本科刷题比赛期间里最后悔的一个题目了，关于素数的题那学期还正好在学信安数学，找了那么久规律已经对了，可惜最后只是错在边界上了，如果当时能和队友再多讨论下，或者再想想边界和精度的问题应该就能过了，也可能这就是ACM比赛的魅力吧... [吃饭时的怪癖](http://bjutacm.openjudge.cn/lianxi/193E/)

好像也是自己的第15场周赛，希望能继续坚持了，不知道目标是什么的时候就坚持刷题呗hhh

第一题：比较简单但是这种题总让人感觉要敲很多本来可以合并到一起的代码，自己的想法总需要锻炼手速，可能就是自己和大佬的区别了；

第二题：很像数学题，但仔细看起来就是个贪心，Counter+贪心很快就能搞定，然后拓展一下的话让自己想起中国剩余定理、扩展欧几里得算法、欧拉函数这些的了。另外补充说一下周赛的第二题感觉够不上medium的难度，笔试的题2/3也都是medium但是难很多；

第三题：一方面要想到后缀为0实际上只能通过2*5得到，这样把每个格子的2 5先记录下来。然后能想到是前缀的思想，自己想到了从上到下，从左到右，从右到左的前缀和，但是漏掉了从下到上的前缀和，就是从下往上的一个L拐的情况，最开始还以为是dfs但是数据范围和只能拐一次不太像。求出四方向的前缀后，剩下就是角标算一算了，注意如果能往右拐一格，实际上就能拐到底了；

第四题：未来有机会再试了TAT（周常鸽第四题）；

<!--more-->

# 第一题：6070.计算字符串的数字和

[题目链接](https://leetcode-cn.com/problems/calculate-digit-sum-of-a-string/)

## 题目大意

给你一个由若干数字（`0` - `9`）组成的字符串 `s` ，和一个整数。

如果 `s` 的长度大于 `k` ，则可以执行一轮操作。在一轮操作中，需要完成以下工作：

1. 将 `s` **拆分** 成长度为 `k` 的若干 **连续数字组** ，使得前 `k` 个字符都分在第一组，接下来的 `k` 个字符都分在第二组，依此类推。**注意**，最后一个数字组的长度可以小于 `k` 。
2. 用表示每个数字组中所有数字之和的字符串来 **替换** 对应的数字组。例如，`"346"` 会替换为 `"13"` ，因为 `3 + 4 + 6 = 13` 。
3. 合并 所有组以形成一个新字符串。如果新字符串的长度大于 `k` 则重复第一步。

返回在完成所有轮操作后的 `s` 。

示例1：
```
输入：s = "11111222223", k = 3
输出："135"
解释：
- 第一轮，将 s 分成："111"、"112"、"222" 和 "23" 。
  接着，计算每一组的数字和：1 + 1 + 1 = 3、1 + 1 + 2 = 4、2 + 2 + 2 = 6 和 2 + 3 = 5 。 
  这样，s 在第一轮之后变成 "3" + "4" + "6" + "5" = "3465" 。
- 第二轮，将 s 分成："346" 和 "5" 。
  接着，计算每一组的数字和：3 + 4 + 6 = 13 、5 = 5 。
  这样，s 在第二轮之后变成 "13" + "5" = "135" 。 
现在，s.length <= k ，所以返回 "135" 作为答案。
```

示例2：
```
输入：s = "00000000", k = 3
输出："000"
解释：
将 "000", "000", and "00".
接着，计算每一组的数字和：0 + 0 + 0 = 0 、0 + 0 + 0 = 0 和 0 + 0 = 0 。 
s 变为 "0" + "0" + "0" = "000" ，其长度等于 k ，所以返回 "000" 。
```

提示：
- `1 <= s.length <= 100`
- `2 <= k <= 100`
- `s` 仅由数字（`0` - `9`）组成。

## 分析和解答

比较简单但是这种题总让人感觉要敲很多本来可以合并到一起的代码，自己的想法总需要锻炼手速，可能就是自己和大佬的区别了；

```python
class Solution(object):
    def digitSum(self, s, k):
        """
        :type s: str
        :type k: int
        :rtype: str
        """

        while len(s) > k:
            tmp_list = []
            ttmp_list = []
            for i in range(len(s)):
                ttmp_list.append(s[i])
                if (i+1) % k == 0:
                    tmp_list.append(ttmp_list)
                    ttmp_list = []
            if len(ttmp_list):
                tmp_list.append(ttmp_list)
            # print(tmp_list)
            tttmp_list = []
            for i in range(len(tmp_list)):
                tsum = 0
                for j in range(len(tmp_list[i])):
                    tsum += int(tmp_list[i][j])
                tttmp_list.append(str(tsum))
            s = ''.join(tttmp_list)
        return s
```

# 第二题：6071.完成所有任务需要的最少轮数

[题目链接](https://leetcode-cn.com/problems/minimum-rounds-to-complete-all-tasks/)

## 题目大意

给你一个下标从 **0** 开始的整数数组 `tasks` ，其中 `tasks[i]` 表示任务的难度级别。在每一轮中，你可以完成 2 个或者 3 个 **相同难度级别** 的任务。

返回完成所有任务需要的 **最少** 轮数，如果无法完成所有任务，返回 `-1`

示例1：
```
输入：tasks = [2,2,3,3,2,4,4,4,4,4]
输出：4
解释：要想完成所有任务，一个可能的计划是：
- 第一轮，完成难度级别为 2 的 3 个任务。 
- 第二轮，完成难度级别为 3 的 2 个任务。 
- 第三轮，完成难度级别为 4 的 3 个任务。 
- 第四轮，完成难度级别为 4 的 2 个任务。 
可以证明，无法在少于 4 轮的情况下完成所有任务，所以答案为 4 。
```

示例2：
```
输入：tasks = [2,3,3]
输出：-1
解释：难度级别为 2 的任务只有 1 个，但每一轮执行中，只能选择完成 2 个或者 3 个相同难度级别的任务。因此，无法完成所有任务，答案为 -1 。
```

提示：
- `1 <= tasks.length <= 10^5`
- `1 <= tasks[i] <= 10^9`

## 分析和解答

很像数学题，但仔细看起来就是个贪心，Counter+贪心很快就能搞定，然后拓展一下的话让自己想起中国剩余定理、扩展欧几里得算法、欧拉函数这些的了。另外补充说一下周赛的第二题感觉够不上medium的难度，笔试的题2/3也都是medium但是难很多；

```python
class Solution(object):
    def minimumRounds(self, tasks):
        """
        :type tasks: List[int]
        :rtype: int
        """
        from collections import Counter
        cnt_tasks = Counter(tasks)
        print(cnt_tasks)  # Counter({4: 5, 2: 3, 3: 2})

        res = 0
        for key, value in cnt_tasks.items():
            if value == 1:  # 中间跳出
                return -1

            t_value = value
            cnt_nums = t_value // 3
            t_value -= cnt_nums * 3
            
            if t_value == 1:
                cnt_nums = cnt_nums - 1 + 2  # 去掉一次搞3，然后搞两次2
            elif t_value == 2:
                cnt_nums += 1
                t_value -= 2
            # print(t_value)
            res += cnt_nums

        return res
```

# 第三题：6072.转角路径的乘积中最多能有几个尾随零

[题目链接](https://leetcode-cn.com/problems/maximum-trailing-zeros-in-a-cornered-path/)

## 题目大意

给你一个二维整数数组 `grid` ，大小为 `m x n`，其中每个单元格都含一个正整数。

**转角路径** 定义为：包含至多一个弯的一组相邻单元。具体而言，路径应该完全 **向水平方向** 或者 **向竖直方向** 移动过弯（如果存在弯），而不能访问之前访问过的单元格。在过弯之后，路径应当完全朝 **另一个** 方向行进：如果之前是向水平方向，那么就应该变为向竖直方向；反之亦然。当然，同样不能访问之前已经访问过的单元格。

一条路径的 **乘积** 定义为：路径上所有值的乘积。

请你从 `grid` 中找出一条乘积中尾随零数目最多的转角路径，并返回该路径中尾随零的数目。

注意：

- **水平** 移动是指向左或右移动。
- **竖直** 移动是指向上或下移动。

示例1：

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-04-18-18-31-09.png)
```
输入：grid = [[23,17,15,3,20],[8,1,20,27,11],[9,4,6,2,21],[40,9,1,10,6],[22,7,4,5,3]]
输出：3
解释：左侧的图展示了一条有效的转角路径。
其乘积为 15 * 20 * 6 * 1 * 10 = 18000 ，共计 3 个尾随零。
可以证明在这条转角路径的乘积中尾随零数目最多。

中间的图不是一条有效的转角路径，因为它有不止一个弯。
右侧的图也不是一条有效的转角路径，因为它需要重复访问已经访问过的单元格。
```

示例2：

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-04-18-18-31-15.png)
```
输入：grid = [[4,3,2],[7,6,1],[8,8,8]]
输出：0
解释：网格如上图所示。
不存在乘积含尾随零的转角路径。
```

提示：
- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 10^5`
- `1 <= m * n <= 10^5`
- `1 <= grid[i][j] <= 1000`


## 分析和解答

一方面要想到后缀为0实际上只能通过2*5得到，这样把每个格子的2 5先记录下来。然后能想到是前缀的思想，自己想到了从上到下，从左到右，从右到左的前缀和，但是漏掉了从下到上的前缀和，就是从下往上的一个L拐的情况，最开始还以为是dfs但是数据范围和只能拐一次不太像。求出四方向的前缀后，剩下就是角标算一算了，注意如果能往右拐一格，实际上就能拐到底了；

```python
class Solution(object):
    def maxTrailingZeros(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """

        def get_num_five(num):
            cnt = 0 
            while num%5 == 0:
                num /= 5
                cnt += 1
            return cnt

        def get_num_two(num):
            cnt = 0
            while num%2 == 0:
                num /= 2
                cnt += 1
            return cnt

        # 从上到下的前缀矩阵，从下到上的前缀矩阵
        grid_up_to_down_prefix = [[[0, 0] for _ in range(len(grid[0]))] for _ in range(len(grid))]
        grid_down_to_up_prefix = [[[0, 0] for _ in range(len(grid[0]))] for _ in range(len(grid))]
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if i == 0:  # 第一行
                    grid_up_to_down_prefix[i][j][0] = get_num_two(grid[i][j])
                    grid_up_to_down_prefix[i][j][1] = get_num_five(grid[i][j])

                    grid_down_to_up_prefix[len(grid)-1][j][0] = get_num_two(grid[len(grid)-1][j])
                    grid_down_to_up_prefix[len(grid)-1][j][1] = get_num_five(grid[len(grid)-1][j])

                else:
                    grid_up_to_down_prefix[i][j][0] = grid_up_to_down_prefix[i-1][j][0] + get_num_two(grid[i][j])
                    grid_up_to_down_prefix[i][j][1] = grid_up_to_down_prefix[i-1][j][1] + get_num_five(grid[i][j])

                    grid_down_to_up_prefix[len(grid)-1-i][j][0] = grid_down_to_up_prefix[len(grid)-i][j][0] + get_num_two(grid[len(grid)-1-i][j])
                    grid_down_to_up_prefix[len(grid)-1-i][j][1] = grid_down_to_up_prefix[len(grid)-i][j][1] + get_num_five(grid[len(grid)-1-i][j])

        # 从左到右的前缀矩阵，从右到左的前缀矩阵
        grid_left_to_right_prefix = [[[0, 0] for _ in range(len(grid[0]))] for _ in range(len(grid))]
        grid_right_to_left_prefix = [[[0, 0] for _ in range(len(grid[0]))] for _ in range(len(grid))]
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if j == 0:  # 第一列
                    grid_left_to_right_prefix[i][j][0] = get_num_two(grid[i][j])
                    grid_left_to_right_prefix[i][j][1] = get_num_five(grid[i][j])

                    grid_right_to_left_prefix[i][len(grid[0])-1][0] = get_num_two(grid[i][len(grid[0])-1])
                    grid_right_to_left_prefix[i][len(grid[0])-1][1] = get_num_five(grid[i][len(grid[0])-1])

                else:
                    grid_left_to_right_prefix[i][j][0] = grid_left_to_right_prefix[i][j-1][0] + get_num_two(grid[i][j])
                    grid_left_to_right_prefix[i][j][1] = grid_left_to_right_prefix[i][j-1][1] + get_num_five(grid[i][j])

                    grid_right_to_left_prefix[i][len(grid[0])-1-j][0] = grid_right_to_left_prefix[i][len(grid[0])-j][0] + get_num_two(grid[i][len(grid[0])-1-j])
                    grid_right_to_left_prefix[i][len(grid[0])-1-j][1] = grid_right_to_left_prefix[i][len(grid[0])-j][1] + get_num_five(grid[i][len(grid[0])-1-j])

        # for i in range(len(grid)):
        #     for j in range(len(grid[0])):
        #         print(grid_right_to_left_prefix[i][j])
        #     print("\n")


        # 从上往下走，从下往上走同时
        max_zero_last = 0
        for i in range(len(grid)):
            for j in range(len(grid[0])):

                # 上往下，然后往右看
                cnt_num_2 = grid_up_to_down_prefix[i][j][0] + grid_left_to_right_prefix[i][-1][0] - grid_left_to_right_prefix[i][j][0]
                cnt_num_5 = grid_up_to_down_prefix[i][j][1] + grid_left_to_right_prefix[i][-1][1] - grid_left_to_right_prefix[i][j][1]
                max_zero_last = max(max_zero_last, min(cnt_num_2, cnt_num_5))

                # 下往上，然后往右看
                cnt_num_2 = grid_down_to_up_prefix[len(grid)-1-i][j][0] + grid_left_to_right_prefix[len(grid)-1-i][-1][0] - grid_left_to_right_prefix[len(grid)-1-i][j][0]
                cnt_num_5 = grid_down_to_up_prefix[len(grid)-1-i][j][1] + grid_left_to_right_prefix[len(grid)-1-i][-1][1] - grid_left_to_right_prefix[len(grid)-1-i][j][1]
                max_zero_last = max(max_zero_last, min(cnt_num_2, cnt_num_5))
                
                # 上往下，然后往左看
                cnt_num_2 = grid_up_to_down_prefix[i][j][0] + grid_right_to_left_prefix[i][0][0] - grid_right_to_left_prefix[i][j][0]
                cnt_num_5 = grid_up_to_down_prefix[i][j][1] + grid_right_to_left_prefix[i][0][1] - grid_right_to_left_prefix[i][j][1]
                max_zero_last = max(max_zero_last, min(cnt_num_2, cnt_num_5))

                # 下往上，然后往左看
                cnt_num_2 = grid_down_to_up_prefix[len(grid)-1-i][j][0] + grid_right_to_left_prefix[len(grid)-1-i][0][0] - grid_right_to_left_prefix[len(grid)-1-i][j][0]
                cnt_num_5 = grid_down_to_up_prefix[len(grid)-1-i][j][1] + grid_right_to_left_prefix[len(grid)-1-i][0][1] - grid_right_to_left_prefix[len(grid)-1-i][j][1]
                max_zero_last = max(max_zero_last, min(cnt_num_2, cnt_num_5))
        
        return max_zero_last
```