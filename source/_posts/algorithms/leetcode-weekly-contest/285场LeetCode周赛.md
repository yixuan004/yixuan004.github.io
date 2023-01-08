---
title: 285场LeetCode周赛
date: 2022-03-20 22:25:56
tags:
    - 数组
    - 栈
    - 字符串
    - 位运算
    - 递归
    - 枚举
    - EASY
    - MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

被周末临时开会打断的一次周赛，有点烦hhh。总结来说这次周赛给的教训第一个应该就是好好读题？好好读题理解题意后再开始做，反而会比一上来突然来个想法就做顺利很多，在每个想法开始前要有个大概的实现难度预估和通过可能性预估，一般来说周赛前三个题仔细思考下，合理性分析下，感觉还都是可以做出来的，思路其实绕来绕去大部分还是那些，比较需要构思好再写吧！

第一题：比较简单，但是一开始读题的时候感觉又卡壳了，感觉读题还是不能读太急吧！做一个去重后就不用向左右判断不等于的了，去重后要保持顺序；

第二题：这个题自己想到的是一种模拟的思想，但是模拟过去后 ```RRRRS``` 这种情况，自己的操作后会变成 ```RRRSS``` ，这种还要从右往左过一遍。赛后看题解有大佬的思路是，在前缀去掉 ```LLLL``` ，后缀去掉 ```RRRR``` ，最后中间的不是 ```S``` 的数目就是碰撞的数目了，感觉真的是一个很奇妙的思路+数学解法；

第三题：原来 ```2^n``` 的这种做法被称作状态压缩，这个题能想到这种做法还是不容易的，感觉一方面是数据范围如果是 ```20-32``` 以内，就可以暴力遍历 ```2^20~30``` 这么多种情况，也算是一种数据上的提示吧。题目中的返回任意情况可能也说明了这一点；

第四题：未来有机会再试了TAT（周常鸽第四题）；

<!--more-->


# 第一题：6027.统计数组中峰和谷的数量

[题目链接](https://leetcode-cn.com/problems/count-hills-and-valleys-in-an-array/)

## 题目大意

给你一个下标从 **0** 开始的整数数组 ```nums``` 。如果两侧距 ```i``` 最近的不相等邻居的值均小于 ```nums[i]``` ，则下标 ```i``` 是 ```nums``` 中，某个峰的一部分。类似地，如果两侧距 ```i``` 最近的不相等邻居的值均大于 ```nums[i]``` ，则下标 ```i``` 是 ```nums``` 中某个谷的一部分。对于相邻下标 ```i``` 和 ```j``` ，如果 ```nums[i] == nums[j]``` ， 则认为这两下标属于 **同一个** 峰或谷。

注意，要使某个下标所做峰或谷的一部分，那么它左右两侧必须 **都** 存在不相等邻居。

返回 ```nums``` 中峰和谷的数量。

示例1：
```
输入：nums = [2,4,1,1,6,5]
输出：3
解释：
在下标 0 ：由于 2 的左侧不存在不相等邻居，所以下标 0 既不是峰也不是谷。
在下标 1 ：4 的最近不相等邻居是 2 和 1 。由于 4 > 2 且 4 > 1 ，下标 1 是一个峰。
在下标 2 ：1 的最近不相等邻居是 4 和 6 。由于 1 < 4 且 1 < 6 ，下标 2 是一个谷。
在下标 3 ：1 的最近不相等邻居是 4 和 6 。由于 1 < 4 且 1 < 6 ，下标 3 符合谷的定义，但需要注意它和下标 2 是同一个谷的一部分。
在下标 4 ：6 的最近不相等邻居是 1 和 5 。由于 6 > 1 且 6 > 5 ，下标 4 是一个峰。
在下标 5 ：由于 5 的右侧不存在不相等邻居，所以下标 5 既不是峰也不是谷。
共有 3 个峰和谷，所以返回 3 。
```

示例2：
```
输入：nums = [6,6,5,5,4,1]
输出：0
解释：
在下标 0 ：由于 6 的左侧不存在不相等邻居，所以下标 0 既不是峰也不是谷。
在下标 1 ：由于 6 的左侧不存在不相等邻居，所以下标 1 既不是峰也不是谷。
在下标 2 ：5 的最近不相等邻居是 6 和 4 。由于 5 < 6 且 5 > 4 ，下标 2 既不是峰也不是谷。
在下标 3 ：5 的最近不相等邻居是 6 和 4 。由于 5 < 6 且 5 > 4 ，下标 3 既不是峰也不是谷。
在下标 4 ：4 的最近不相等邻居是 5 和 1 。由于 4 < 5 且 4 > 1 ，下标 4 既不是峰也不是谷。
在下标 5 ：由于 1 的右侧不存在不相等邻居，所以下标 5 既不是峰也不是谷。
共有 0 个峰和谷，所以返回 0 。
```

提示：
- 3 <= nums.length <= 100
- 1 <= nums[i] <= 100


## 分析和解答

比较简单，但是一开始读题的时候感觉又卡壳了，感觉读题还是不能读太急吧！做一个去重后就不用向左右判断不等于的了，去重后要保持顺序；

下边这个代码实际上冗余了很多；

```python
class Solution(object):
    def countHillValley(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        tmp_nums = []
        tmp = -1
        for i in range(len(nums)):
            if tmp == -1:
                tmp = nums[i]
                tmp_nums.append(nums[i])
                continue
            else:
                if nums[i] == tmp:
                    continue
                else:
                    tmp = nums[i]
                    tmp_nums.append(nums[i])
        
        print(tmp_nums)
        
        
        cnt = 0
        for i in range(1, len(tmp_nums)-1):
            # 找到左边不相等的
            
            t1 = -1
            for j in range(i-1, -1, -1):
                if tmp_nums[j] != tmp_nums[i]:
                    t1 = tmp_nums[j]
                    break
                    
            t2 = -1
            for j in range(i+1, len(tmp_nums)):
                if tmp_nums[j] != tmp_nums[i]:
                    t2 = tmp_nums[j]
                    break
                    
            print(tmp_nums[i], t1, t2)
            
            if (tmp_nums[i] > t1 and tmp_nums[i] > t2 and t1 != -1 and t2 != -1) or (tmp_nums[i] < t1 and tmp_nums[i] < t2 and t1 != -1 and t2 != -1):
                cnt +=1
        
        return cnt
```


# 第二题：6028.统计道路上的碰撞次数

[题目链接](https://leetcode-cn.com/problems/count-collisions-on-a-road/submissions/)

## 题目大意

在一条无限长的公路上有 ```n``` 辆汽车正在行驶。汽车按从左到右的顺序按从 ```0``` 到 ```n - 1``` 编号，每辆车都在一个 **独特的** 位置。

给你一个下标从 **0** 开始的字符串 ```directions``` ，长度为 ```n``` 。```directions[i]``` 可以是 ```'L'``` 、 ```'R'``` 或 ```'S'``` 分别表示第 ```i``` 辆车是向 **左** 、向 **右** 或者 **停留** 在当前位置。每辆车移动时 **速度相同** 。

碰撞次数可以按下述方式计算：

- 当两辆移动方向 相反 的车相撞时，碰撞次数加 ```2``` 。
- 当一辆移动的车和一辆静止的车相撞时，碰撞次数加 ```1``` 。

碰撞发生后，涉及的车辆将无法继续移动并停留在碰撞位置。除此之外，汽车不能改变它们的状态或移动方向。

返回在这条道路上发生的 **碰撞总次数** 。

示例1：
```
输入：directions = "RLRSLL"
输出：5
解释：
将会在道路上发生的碰撞列出如下：
- 车 0 和车 1 会互相碰撞。由于它们按相反方向移动，碰撞数量变为 0 + 2 = 2 。
- 车 2 和车 3 会互相碰撞。由于 3 是静止的，碰撞数量变为 2 + 1 = 3 。
- 车 3 和车 4 会互相碰撞。由于 3 是静止的，碰撞数量变为 3 + 1 = 4 。
- 车 4 和车 5 会互相碰撞。在车 4 和车 3 碰撞之后，车 4 会待在碰撞位置，接着和车 5 碰撞。碰撞数量变为 4 + 1 = 5 。
因此，将会在道路上发生的碰撞总次数是 5 。
```

示例2：
```
输入：directions = "LLRR"
输出：0
解释：
不存在会发生碰撞的车辆。因此，将会在道路上发生的碰撞总次数是 0 。
```

提示：
- ```1 <= directions.length <= 10^5```
- ```directions[i]``` 的值为 ```'L'``` 、```'R'``` 或 ```'S'```

## 分析和解答

这个题自己想到的是一种模拟的思想，但是模拟过去后 ```RRRRS``` 这种情况，自己的操作后会变成 ```RRRSS``` ，这种还要从右往左过一遍。赛后看题解有大佬的思路是，在前缀去掉 ```LLLL``` ，后缀去掉 ```RRRR``` ，最后中间的不是 ```S``` 的数目就是碰撞的数目了，感觉真的是一个很奇妙的思路+数学解法；

```python
class Solution(object):
    def countCollisions(self, directions):
        """
        :type directions: str
        :rtype: int
        """
        
        # directions += 'R'  # 哨兵
        
        init_status = [[i, i, 'S'] for i in range(len(directions))]  # 第几个车，第几个车移动后，这个车的移动方向是
        
        new_status = []
        for i in range(len(init_status)):
            if directions[i] == 'L':
                new_status.append([init_status[i][0], init_status[i][0]-1, 'L'])
            elif directions[i] == 'R':
                new_status.append([init_status[i][0], init_status[i][0]+1, 'R'])
            elif directions[i] == 'S':
                new_status.append(init_status[i])
        
        res = 0
        for i in range(len(new_status)-1):
            if new_status[i][1] >= new_status[i+1][1]:
                # 这两个会撞上，判断两个状态
                if new_status[i][2] != 'S' and new_status[i+1][2] != 'S' and new_status[i+1][2] != new_status[i][2]:
                    # print("2: ", new_status[i], new_status[i+1])
                    res += 2
                else:
                    # print("1: ", new_status[i], new_status[i+1])
                    res += 1
                # 汇总成之前的，不应该是之前的
                new_status[i] = init_status[i]
                new_status[i][2] = 'S'
                new_status[i+1] = init_status[i+1]
                new_status[i+1][2] = 'S'
                # print("i, new_status: ", i, new_status)
                
#         # 还需要再过一遍
#         # 正着过
        for i in range(1, len(new_status)):
            if new_status[i][2] == 'L' and new_status[i-1][2] == 'S':
                res += 1
                new_status[i][2] == 'S'
        
        for i in range(len(new_status)-1, 0, -1):
            if new_status[i-1][2] == 'R' and new_status[i][2] == 'S':
                res += 1
                new_status[i-1][2] = 'S'
  
        return res
```


# 第三题：6029.射箭比赛中的最大得分

[题目链接](https://leetcode-cn.com/problems/maximum-points-in-an-archery-competition/)

## 题目大意

Alice 和 Bob 是一场射箭比赛中的对手。比赛规则如下：

1. Alice 先射 ```numArrows``` 支箭，然后 Bob 也射 ```numArrows``` 支箭。
2. 分数按下述规则计算：
   1. 箭靶有若干整数计分区域，范围从 0 到 11 （含 0 和 11）。
   2. 箭靶上每个区域都对应一个得分 ```k```（范围是 ```0``` 到 ```11```），Alice 和 Bob 分别在得分 ```k``` 区域射中 ```ak``` 和 ```bk``` 支箭。如果 ```ak >= bk``` ，那么 Alice 得 ```k``` 分。如果 ```ak < bk``` ，则 Bob 得 ```k``` 分
   3. 如果 ```ak == bk == 0``` ，那么无人得到 ```k``` 分。

- 例如，Alice 和 Bob 都向计分为 ```11``` 的区域射 ```2``` 支箭，那么 Alice 得 ```11``` 分。如果 Alice 向计分为 ```11``` 的区域射 ```0``` 支箭，但 Bob 向同一个区域射 ```2``` 支箭，那么 Bob 得 ```11``` 分。

给你整数 ```numArrows``` 和一个长度为 ```12``` 的整数数组 ```aliceArrows``` ，该数组表示 Alice 射中 ```0``` 到 ```11``` 每个计分区域的箭数量。现在，Bob 想要尽可能 **最大化** 他所能获得的总分。

返回数组 ```bobArrows``` ，该数组表示 Bob 射中 ```0``` 到 ```11``` **每个** 计分区域的箭数量。且 ```bobArrows``` 的总和应当等于 ```numArrows``` 。

如果存在多种方法都可以使 Bob 获得最大总分，返回其中 **任意一种** 即可。

示例1：

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-20-23-45-26.png)
```
输入：numArrows = 9, aliceArrows = [1,1,0,1,0,0,2,1,0,1,2,0]
输出：[0,0,0,0,1,1,0,0,1,2,3,1]
解释：上表显示了比赛得分情况。
Bob 获得总分 4 + 5 + 8 + 9 + 10 + 11 = 47 。
可以证明 Bob 无法获得比 47 更高的分数。
```


示例2：

![](2022-03-20-23-45-58.png)
```
输入：numArrows = 3, aliceArrows = [0,0,1,0,0,0,0,0,0,0,0,2]
输出：[0,0,0,0,0,0,0,0,1,1,1,0]
解释：上表显示了比赛得分情况。
Bob 获得总分 8 + 9 + 10 = 27 。
可以证明 Bob 无法获得比 27 更高的分数。
```

提示：

- ```1 <= numArrows <= 10^5```
- ```aliceArrows.length == bobArrows.length == 12```
- ```0 <= aliceArrows[i], bobArrows[i] <= numArrows```
- ```sum(aliceArrows[i]) == numArrows```


## 分析和解答

原来 ```2^n``` 的这种做法被称作状态压缩，这个题能想到这种做法还是不容易的，感觉一方面是数据范围如果是 ```20-32``` 以内，就可以暴力遍历 ```2^20~30``` 这么多种情况，也算是一种数据上的提示吧。题目中的返回任意情况可能也说明了这一点；

```python
class Solution(object):
    def maximumBobPoints(self, numArrows, aliceArrows):
        """
        :type numArrows: int
        :type aliceArrows: List[int]
        :rtype: List[int]
        """
        max_score = 0
        
        # 直接暴力枚举，11种二进制转状态，判断在哪些状态能得分（是合理的）
        for i in range(2**len(aliceArrows)):
            tmp_bin = []
            tmp = i
            while tmp > 0:
                tmp_bin.append(tmp&1)
                tmp >>= 1
            tmp_bin += [0 for _ in range(len(aliceArrows)-len(tmp_bin))]
            
            # 1代表要在这些位置上击败alice，然后做合理性判断就行了
            flag = True
            cnt = 0
            tmp_score = 0
            tmp_list = []
            for j in range(len(aliceArrows)):
                if tmp_bin[j] == 1:
                    cnt += aliceArrows[j] + 1
                    tmp_score += j
                    tmp_list.append(aliceArrows[j] + 1)
                else:
                    tmp_list.append(0)
            # 这里判断合理性
            if cnt <= numArrows:
                if tmp_score > max_score:
                    max_score= tmp_score
                    res = tmp_list
                    res[0] = numArrows - sum(res)

        return res
```