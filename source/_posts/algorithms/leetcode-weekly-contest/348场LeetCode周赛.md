---
title: 348场LeetCode周赛
date: 2023-06-11 21:00:45
tags:
	- EASY
	- MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

https://leetcode.cn/contest/weekly-contest-348/

保分大师is me，一上来看到t1就是一个脑瓜壳嗡嗡，然后先把t2做了再做t1，最后t3还算是一个比较好想的题，就是从后往前推，因为最后的状态就一定是已经确定下来的了，然后当时忽略了一种情况就是重复行还是什么的情况，反正就是只有第一次会减掉一些计数，把这个地方考虑进去后就AC了

![image-20230611214813503](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230611214813503.png)

第一题：自己想的太复杂了，实际上是能直接得到结论但是证明不好证明的那种问题，最后实际上只是不同字母的个数（自己的做法贼复杂）

第二题：第二个也是个easy题，就是找到最大值和最小值，如果最大值在最小值的左边，则移动可以少一次这样的

第三题：从后往前倒着想，想到倒着想之后基本已经完成了90%了，但是还需要处理重复行的情况

第四题：g

<!--more-->

# 第一题：2716. 最小化字符串长度

https://leetcode.cn/problems/minimize-string-length/description/

## 题目大意

![image-20230612003941651](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612003941651.png)

## 分析和解答

自己想的太复杂了，实际上是能直接得到结论但是证明不好证明的那种问题，最后实际上只是不同字母的个数（自己的做法贼复杂）

```python
class Solution(object):
    def minimizedStringLength(self, s):
        """
        :type s: str
        :rtype: int
        """
        flag = [1 for _ in range(len(s))]
        for i in range(len(s)):
            #
            if not flag[i]:
                continue
            
            
            # 往前走
            for j in range(i-1, -1, -1):
                if not flag[j]:
                    continue
                if s[j] == s[i]:
                    flag[j] = 0
                    break
            
            # 往后走
            for k in range(i+1, len(s)):
                if not flag[k]:
                    continue
                if s[k] == s[i]:
                    flag[k] = 0
                    break
        
        # print(flag)
        
        return sum(flag)
```

```python
class Solution:
    def minimizedStringLength(self, s: str) -> int:
        return len(set(s))

作者：灵茶山艾府
链接：https://leetcode.cn/problems/minimize-string-length/solutions/2296066/o1-kong-jian-wei-yun-suan-xie-fa-pythonj-7t4p/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

# 第二题：2717. 半有序排列

https://leetcode.cn/problems/semi-ordered-permutation/description/

## 题目大意

![image-20230612004008383](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612004008383.png)

## 分析和解答

第二个也是个easy题，就是找到最大值和最小值，如果最大值在最小值的左边，则移动可以少一次这样的

```python
class Solution(object):
    def semiOrderedPermutation(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        _min = min(nums)
        _max = max(nums)
        
        min_index = nums.index(_min)
        max_index = nums.index(_max)
        
        if min_index > max_index:
            res = min_index + (len(nums)-1-max_index) - 1
        else:
            res = min_index + (len(nums)-1-max_index)
            
        
        return res
```

# 第三题：2718. 查询后矩阵的和

https://leetcode.cn/problems/sum-of-matrix-after-queries/

## 题目大意

![image-20230612004030500](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612004030500.png)

![image-20230612004046680](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20230612004046680.png)

## 分析和解答

从后往前倒着想，想到倒着想之后基本已经完成了90%了，但是还需要处理重复行的情况

```python
class Solution:
    def matrixSumQueries(self, n: int, queries: List[List[int]]) -> int:
        res = 0
        
        # left_i = [n for _ in range(n)]  # i行还有几个没填的值，直接查询
        _i = 0
        
        # left_j = [n for _ in range(n)]  # j列还有几个没填的值，直接查询
        _j = 0
        
        vis_heng = defaultdict(int)
        vis_zong = defaultdict(int)
        
        
        for i in range(len(queries)-1, -1, -1):  # 从后往前
            _type, index, val = queries[i]  # 0是把index行全改成val，1是吧index列全改成val
            
            if _type == 1:
                # 查看index列还有几个空位
                if vis_heng[index] == 1:
                    continue
                
                # print(f"index列剩余空位: {max((n-_j), 0)}")
                # print(f"index行剩余空位: {max((n-_i), 0)}")
                res += ((n-_j) * val)
                # 修改行，每行减一个
                if vis_heng[index] == 0:
                    _i += 1
                    vis_heng[index] = 1
            else:
                
                if vis_zong[index] == 1:
                    continue
                    
                # 查看index行还有几个空位
                # print(f"index列剩余空位: {max((n-_j), 0)}")
                # print(f"index行剩余空位: {max((n-_i), 0)}")
                res += ((n-_i) * val)
                # 修改行，每行减一个，必须是没出现过的
                if vis_zong[index] == 0:
                    _j += 1
                    vis_zong[index] = 1
            # print("")
        
        return res
```