---
title: 279场LeetCode周赛
date: 2022-02-07 16:41:48
tags:
    - 数组
    - 排序
    - 数学
    - 设计
    - 哈希表
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

这次手感还可以，在比赛时间做出了三个题，回看复盘的时候感觉当时状态可能确实好hhh，思路一路做下来没有中断的感觉。一些常见的操作包括filp一类的，还有就是下边这段代码可以直接取出来奇偶下标的！（实际是在280周赛复盘发现的）

```python
nums[::2]  # 取出偶数下标的value
nums[1::2]  # 取出奇数下标的value
```

还有个是index截断的重拼接可以使用下边这种方式

```python
s[index] + ''.join(s[:index]) + ''.join(s[index+1:])
```

第一题：印象比较深的是要把奇数偶数下标的分开拍，注意审题的感觉；

第二题：也是一个排序的思路，可以分大于0小于0两种情况分类讨论，还是很容易区分状态的；

第三题：一种设计题的思路，第一次很简单的做很快就超时了，超时后复盘发现可以用一些标识为来进行一些标志，有一个filp操作，标志出filp_status后，其他的操作都可以通过cnt直接O(1)的返回了，做题的时候状态比较好，感觉这个题还是没那么好想，也可能是做的时候根据badcase发现了优化思路吧；

第四题：鸽！

<!--more-->

# 第一题：2164.对奇偶下标分别排序

[题目链接](https://leetcode-cn.com/problems/sort-even-and-odd-indices-independently/)

## 题目大意

给你一个下标从 **0** 开始的整数数组 ```nums``` 。根据下述规则重排 ```nums``` 中的值：

1. 按 非递增 顺序排列 ```nums``` **奇数下标** 上的所有值。
- 举个例子，如果排序前 ```nums = [4,1,2,3]``` ，对奇数下标的值排序后变为 ```[4,3,2,1]``` 。奇数下标 ```1``` 和 ```3``` 的值按照非递增顺序重排。

2. 按 非递减 顺序排列 ```nums``` **偶数下标** 上的所有值。
- 举个例子，如果排序前 ```nums = [4,1,2,3]``` ，对偶数下标的值排序后变为 ```[2,1,4,3]``` 。偶数下标 ```0``` 和 ```2``` 的值按照非递减顺序重排。

返回重排 ```nums``` 的值之后形成的数组。

示例1：
```
输入：nums = [4,1,2,3]
输出：[2,3,4,1]
解释：
首先，按非递增顺序重排奇数下标（1 和 3）的值。
所以，nums 从 [4,1,2,3] 变为 [4,3,2,1] 。
然后，按非递减顺序重排偶数下标（0 和 2）的值。
所以，nums 从 [4,1,2,3] 变为 [2,3,4,1] 。
因此，重排之后形成的数组是 [2,3,4,1] 。
```

示例2：
```
输入：nums = [2,1]
输出：[2,1]
解释：
由于只有一个奇数下标和一个偶数下标，所以不会发生重排。
形成的结果数组是 [2,1] ，和初始数组一样。 
```

提示：
- 1 <= nums.length <= 100
- 1 <= nums[i] <= 100



## 分析和解答

这个题主要是审题，开始审题错了还被坑了一回，注意是奇偶数下标这样的。

然后就是280场周赛get到的一个技巧，用下边这个代码操作可以把奇数偶数间隔的取出来

```python
nums[::2]  # 取出偶数下标的value
nums[1::2]  # 取出奇数下标的value
```

```python
class Solution(object):
    def sortEvenOdd(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        o_index = nums[::2]
        j_index = nums[1::2]
        o_index.sort()
        j_index.sort(key=lambda x: -x)

        result = []
        j = 0
        k = 0
        for i in range(len(nums)):
            if i % 2 == 0:
                result.append(o_index[j])
                j += 1
            else:
                result.append(j_index[k])
                k += 1
        
        return result
```


# 第二题：2165.重排数字的最小值

[题目链接](https://leetcode-cn.com/problems/smallest-value-of-the-rearranged-number/)

## 题目大意

给你一个整数 ```num``` 。**重排** ```num``` 中的各位数字，使其值 **最小化** 且不含 **任何** 前导零。

返回不含前导零且值最小的重排数字。

注意，重排各位数字后，```num``` 的符号不会改变。

示例1：
```
输入：num = 310
输出：103
解释：310 中各位数字的可行排列有：013、031、103、130、301、310 。
不含任何前导零且值最小的重排数字是 103 。
```

示例2：
```
输入：num = -7605
输出：-7650
解释：-7605 中各位数字的部分可行排列为：-7650、-6705、-5076、-0567。
不含任何前导零且值最小的重排数字是 -7650 。
```

提示：
- ```-10^15 <= num <= 10^15```


## 分析和解答

要分开处理正数和负数的情况，然后贪心思路的感觉。在str和int类型之间来回转化。

如果是正数的处理要稍微复杂一点点，就是第一位不能以0起头，要选择一个不是0的index作为起头。

注意列表的拼接，可以使用如下的操作方式：

```python
s[index] + ''.join(s[:index]) + ''.join(s[index+1:]
```

题目解答如下

```python
class Solution(object):
    def smallestNumber(self, num):
        """
        :type num: int
        :rtype: int
        """
        r_list = []

        if num < 0:
            s = list(str(-num))
            s.sort(key=lambda x: -int(x))
            # 选一个最大的（第一个就是），进行重组
            return_int = -int(''.join(s))
            # print(return_int)
            
        else:  # num >= 0
            s = list(str(num))
            s.sort()
            
            index = -1
            for i in range(len(s)):
                if int(s[i]) != 0:
                    index = i
                    break
            print(index)
            return_int = int(s[index] + ''.join(s[:index]) + ''.join(s[index+1:]))
            # print(return_int)
    
        return return_int
```


# 第三题：2166.设计位集

[题目链接](https://leetcode-cn.com/problems/design-bitset/)

## 题目大意

**位集 Bitset** 是一种能以紧凑形式存储位的数据结构。

请你实现 ```Bitset``` 类。

- ```Bitset(int size)``` 用 ```size``` 个位初始化 Bitset ，所有位都是 ```0``` 。
- ```void fix(int idx)``` 将下标为 ```idx``` 的位上的值更新为 ```1``` 。如果值已经是 ```1``` ，则不会发生任何改变。
- ```void unfix(int idx)``` 将下标为 ```idx``` 的位上的值更新为 ```0``` 。如果值已经是 ```0``` ，则不会发生任何改变。
- ```void flip()``` 翻转 Bitset 中每一位上的值。换句话说，所有值为 ```0``` 的位将会变成 ```1``` ，反之亦然。
- ```boolean all()``` 检查 Bitset 中 **每一位** 的值是否都是 ```1``` 。如果满足此条件，返回 ```true``` ；否则，返回 ```false``` 。
- ```boolean one()``` 检查 Bitset 中 是否 **至少一位** 的值是 ```1``` 。如果满足此条件，返回 ```true``` ；否则，返回 ```false``` 。
- ```int count()``` 返回 Bitset 中值为 1 的位的 **总数** 。
- ```String toString()``` 返回 Bitset 的当前组成情况。注意，在结果字符串中，第 ```i``` 个下标处的字符应该与 Bitset 中的第 ```i``` 位一致。

示例：
```
输入
["Bitset", "fix", "fix", "flip", "all", "unfix", "flip", "one", "unfix", "count", "toString"]
[[5], [3], [1], [], [], [0], [], [], [0], [], []]
输出
[null, null, null, null, false, null, null, true, null, 2, "01010"]

解释
Bitset bs = new Bitset(5); // bitset = "00000".
bs.fix(3);     // 将 idx = 3 处的值更新为 1 ，此时 bitset = "00010" 。
bs.fix(1);     // 将 idx = 1 处的值更新为 1 ，此时 bitset = "01010" 。
bs.flip();     // 翻转每一位上的值，此时 bitset = "10101" 。
bs.all();      // 返回 False ，bitset 中的值不全为 1 。
bs.unfix(0);   // 将 idx = 0 处的值更新为 0 ，此时 bitset = "00101" 。
bs.flip();     // 翻转每一位上的值，此时 bitset = "11010" 。
bs.one();      // 返回 True ，至少存在一位的值为 1 。
bs.unfix(0);   // 将 idx = 0 处的值更新为 0 ，此时 bitset = "01010" 。
bs.count();    // 返回 2 ，当前有 2 位的值为 1 。
bs.toString(); // 返回 "01010" ，即 bitset 的当前组成情况。
```

提示：
- ```1 <= size <= 10^5```
- ```0 <= idx <= size - 1```
- 至多调用 ```fix```、```unfix```、```flip```、```all```、```one```、```count``` 和 ```toString``` 方法 总共 ```10^5``` 次
- 至少调用 ```all```、```one```、```count``` 或 ```toString``` 方法一次
- 至多调用 ```toString``` 方法 ```5``` 次


## 分析和解答

要通过一个filp的操作把很多操作都变成O(1)的，

- fix和unfix的操作因为是数组角标的操作，所以是O(1)的，但是注意这个操作要根据filp的状态来决定
- filp直接把self.filp_status修改了就行O(1)
- all、one、count根据cnt值和filp_status共同确定也可以O(1)

```python
class Bitset(object):

    def __init__(self, size):
        """
        :type size: int
        """
        # 都放在初始化里？
        self.bs = [0 for _ in range(size)]
        self.cnt = 0
        self.filp_status = 0
        

    def fix(self, idx):  # 这个没的改了
        """
        :type idx: int
        :rtype: None
        """
        if self.bs[idx] != 1 and self.filp_status == 0:
            self.bs[idx] = 1
            self.cnt += 1    
        elif self.filp_status == 1 and self.bs[idx] != 0:
            self.bs[idx] = 0
            self.cnt += 1
        
    def unfix(self, idx):  # 这个没的改了
        """
        :type idx: int
        :rtype: None
        """
        if self.bs[idx] == 1 and self.filp_status == 0:
            self.bs[idx] = 0
            self.cnt -= 1
        elif self.filp_status == 1 and self.bs[idx] != 1:
            self.bs[idx] = 1
            self.cnt -= 1


    def flip(self):
        """
        :rtype: None
        """
        # 翻转了
        if self.filp_status == 0:
            self.filp_status = 1
        else:
            self.filp_status = 0
        self.cnt = len(self.bs) - self.cnt


    def all(self):
        """
        :rtype: bool
        """
        if self.cnt == len(self.bs):
            return True
        else:
            return False
        

    def one(self):
        """
        :rtype: bool
        """
        if self.cnt >= 1:
            return True
        else:
            return False
        

    def count(self):
        """
        :rtype: int
        """
        return self.cnt
        
        

    def toString(self):
        """
        :rtype: str
        """
        if self.filp_status == 0:
            return ''.join([str(x) for x in self.bs])
        else:
            return ''.join([str(abs(1-x)) for x in self.bs])

# Your Bitset object will be instantiated and called as such:
# obj = Bitset(size)
# obj.fix(idx)
# obj.unfix(idx)
# obj.flip()
# param_4 = obj.all()
# param_5 = obj.one()
# param_6 = obj.count()
# param_7 = obj.toString()
```