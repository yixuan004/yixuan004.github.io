---
title: 288场LeetCode周赛
date: 2022-04-11 22:15:10
tags:
    - 排序
    - 堆
    - 优先队列
    - 字符串
    - 枚举
    - 贪心
    - 数组
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

周赛综述&总结：

上一周清明节扫墓鸽了一周，这周又回来了~ 周中尝试做了一个笔试题找了下思路的手感，然后发现leetcode说不定隔一阵做一次手感会更好哈哈哈。这次的前三个题感觉都是很快就有思路的，然后感觉自己做的顺手程度和代码的变量命名关系很大，如果变量命名的很顺利，那说明这次周赛可能就会很顺利了；

另外来说的话周赛确实要总结下数据结构，感觉前三题都是各种数据结构几乎差不多就能做出来了，而且要熟悉各个数据结构的时间复杂度，像这次的第三题实际上堆的想法是在脑子里一闪而过的，但是没有深思考下去就放弃了，有点可惜；

这次还积累到一个很坑的地方，就是除【1e9+7】和除【1000000007还有除10**9+7】可能是有精度问题的！！！最后比赛结束后才因为精度问题把这个题改过了做出来，实在是太坑了，或者说可能要用int把1e9+7转一下吧

第一题：比较水，奇偶位置（交错位置）分开靠考虑的题遇到过好几次了，这种题就纯是锻炼手速和快速思路，应该写的再快一点的；

第二题：加括号一个比较恶心的题，这种题就是角标要算来算去调来调去，然后又要考虑边界情况的，这次还没看清楚输出结果，以为是要输出那个最小可能数字，没想到是输出表达式还坑了一下。这个题如果给初学者做估计对双重循环的理解能更进一步吧；

第三题：根据数学积累还是能想到把数字均匀散开的时候乘积最大的，就像评论区说的就和周长固定的时候，什么时候矩形的面积最大一样，自己的想法是把每个地方均匀散开（向下一个数字补齐），排序后使用一种类似于前缀的思想简化时间复杂度，然后算长度来做乘法；另外大佬的heap（优先队列）做法实在是太眼前一亮了，没想到根据k的数据范围可以直接这么暴力，这样一下就简化很多了；另外就是大数精度的问题了，发现这个bug是因为这么多数乘在一起，不能就和标准答案差几吧；

第四题：未来有机会再试了TAT（周常鸽第四题）；

<!--more-->

# 第一题：2231.按奇偶性交换后的最大数字

[题目链接](https://leetcode-cn.com/problems/largest-number-after-digit-swaps-by-parity/)

## 题目大意

给你一个正整数 `num` 。你可以交换 `num` 中 **奇偶性** 相同的任意两位数字（即，都是奇数或者偶数）。

返回交换 **任意** 次之后 `num` 的 **最大** 可能值。

示例1：
```
输入：num = 1234
输出：3412
解释：交换数字 3 和数字 1 ，结果得到 3214 。
交换数字 2 和数字 4 ，结果得到 3412 。
注意，可能存在其他交换序列，但是可以证明 3412 是最大可能值。
注意，不能交换数字 4 和数字 1 ，因为它们奇偶性不同。
```

示例2：
```
输入：num = 65875
输出：87655
解释：交换数字 8 和数字 6 ，结果得到 85675 。
交换数字 5 和数字 7 ，结果得到 87655 。
注意，可能存在其他交换序列，但是可以证明 87655 是最大可能值。
```

提示：
- `1 <= num <= 10^9`

## 分析和解答

比较水，奇偶位置（交错位置）分开靠考虑的题遇到过好几次了，这种题就纯是锻炼手速和快速思路，应该写的再快一点的；

```python
class Solution(object):
    def largestInteger(self, num):
        """
        :type num: int
        :rtype: int
        """
        
        nums = list(str(num))
        
        tmp = [0 for i in range(len(nums))]  # 记录角标奇偶性
        oushu = []
        jishu = []
        for i in range(len(nums)):
            if int(nums[i]) % 2 == 0:
                tmp[i] = 0
                oushu.append(int(nums[i]))
            else:
                tmp[i] = 1
                jishu.append(int(nums[i]))
        
        
        # 贪心
        k = 0
        jishu.sort(reverse=True)
        
        j = 0
        oushu.sort(reverse=True)
        
        
        
        res = []
        for i in range(len(nums)):
            if tmp[i] == 0:
                res.append(oushu[j])
                j += 1
            else:
                res.append(jishu[k])
                k += 1
        res = [str(item) for item in res]
        return int(''.join(res))
```


# 第二题：2232.向表达式添加括号后的最小结果

[题目链接](https://leetcode-cn.com/problems/minimize-result-by-adding-parentheses-to-expression/)

## 题目大意

给你一个下标从 **0** 开始的字符串 `expression` ，格式为 `"<num1>+<num2>"` ，其中 `<num1>` 和 `<num2>` 表示正整数。

请你向 `expression` 中添加一对括号，使得在添加之后， `expression` 仍然是一个有效的数学表达式，并且计算后可以得到 **最小** 可能值。左括号 **必须** 添加在 `'+'` 的左侧，而右括号必须添加在 `'+'` 的右侧。

返回添加一对括号后形成的表达式 `expression` ，且满足 `expression` 计算得到 **最小** 可能值。如果存在多个答案都能产生相同结果，返回任意一个答案。

生成的输入满足：`expression` 的原始值和添加满足要求的任一对括号之后 `expression` 的值，都符合 32-bit 带符号整数范围。

示例1：
```
输入：expression = "247+38"
输出："2(47+38)"
解释：表达式计算得到 2 * (47 + 38) = 2 * 85 = 170 。
注意 "2(4)7+38" 不是有效的结果，因为右括号必须添加在 '+' 的右侧。
可以证明 170 是最小可能值。
```

示例2：
```
输入：expression = "12+34"
输出："1(2+3)4"
解释：表达式计算得到 1 * (2 + 3) * 4 = 1 * 5 * 4 = 20 。
```

示例3：
```
输入：expression = "999+999"
输出："(999+999)"
解释：表达式计算得到 999 + 999 = 1998 。
```

提示：
- `3 <= expression.length <= 10`
- `expression` 仅由数字 `'1'` 到 `'9'` 和 `'+'` 组成
- `expression` 由数字开始和结束
- `expression` 恰好仅含有一个 `'+'`.
- `expression` 的原始值和添加满足要求的任一对括号之后 expression 的值，都符合 32-bit 带符号整数范围

## 分析和解答

加括号一个比较恶心的题，这种题就是角标要算来算去调来调去，然后又要考虑边界情况的，这次还没看清楚输出结果，以为是要输出那个最小可能数字，没想到是输出表达式还坑了一下。这个题如果给初学者做估计对双重循环的理解能更进一步吧；

```python
class Solution(object):
    def minimizeResult(self, expression):
        """
        :type expression: str
        :rtype: str
        """
        # 遍历每个可添加位置，然后找一个最小的
        
        
        expression_list = expression.split('+')
        left_list = list(expression_list[0])
        right_list = list(expression_list[1])
        
        # print(left_list)
        # print(right_list)
        
        
        tmp_res = -1
        min_res = 1e9
        
        
        # left
        # 哨兵
        left_res = 0
        right_res = len(right_list)-1
        min_res = int(''.join(left_list)) + int(''.join(right_list))
        
        
        
        # min_res = min(min_res, int(''.join(left_list[:1])) * (int(''.join(left_list[1:])) + int(''.join(right_list))))
        # min_res = min(min_res, (int(''.join(left_list)) + int(''.join(right_list[:len(right_list)-1]))) * int(''.join(right_list[len(right_list)-1:])))
        
        for i in range(len(left_list)):  # i代表插在前边
            for j in range(len(right_list)):  # j代表插在后边
                # if i != 0 and j != len(right_list) - 1:
                    
                pre = int(''.join(left_list[:i])) if len(''.join(left_list[:i])) else 1
                inter = (int(''.join(left_list[i:])) + int(''.join(right_list[:j+1])))
                last = int(''.join(right_list[j+1:])) if len(''.join(right_list[j+1:])) else 1


                tmp_res = pre * inter * last

                if tmp_res < min_res:
                    min_res = tmp_res
                    left_res = i
                    right_res = j
                    
                        
                    # min_res = min(min_res, tmp_res)
                  
                    
                    # print(left_list[:i])
                    # print(left_list[i:])
                    # print(right_list[:j+1])
                    # print(right_list[j+1:])
                    # assert False
        # print(left_res, right_res)
        
        left_list.insert(left_res, '(')
        right_list.insert(right_res+1, ')')
        res_list = left_list + ['+'] + right_list
        return ''.join(res_list)    
```


# 第三题：2233.K 次增加后的最大乘积

[题目链接](https://leetcode-cn.com/problems/maximum-product-after-k-increments/)

## 题目大意

给你一个非负整数数组 `nums` 和一个整数 `k` 。每次操作，你可以选择 `nums` 中 **任一** 元素并将它 **增加** `1` 。

请你返回 **至多** `k` 次操作后，能得到的 `nums` 的 **最大乘积** 。由于答案可能很大，请你将答案对 `10^9 + 7` 取余后返回。

示例1：
```
输入：nums = [0,4], k = 5
输出：20
解释：将第一个数增加 5 次。
得到 nums = [5, 4] ，乘积为 5 * 4 = 20 。
可以证明 20 是能得到的最大乘积，所以我们返回 20 。
存在其他增加 nums 的方法，也能得到最大乘积。
```

示例2：
```
输入：nums = [6,3,3,2], k = 2
输出：216
解释：将第二个数增加 1 次，将第四个数增加 1 次。
得到 nums = [6, 4, 3, 3] ，乘积为 6 * 4 * 3 * 3 = 216 。
可以证明 216 是能得到的最大乘积，所以我们返回 216 。
存在其他增加 nums 的方法，也能得到最大乘积。
```

提示：
- `1 <= nums.length, k <= 10^5`
- `0 <= nums[i] <= 10^6`

## 分析和解答

根据数学积累还是能想到把数字均匀散开的时候乘积最大的，就像评论区说的就和周长固定的时候，什么时候矩形的面积最大一样，自己的想法是把每个地方均匀散开（向下一个数字补齐），排序后使用一种类似于前缀的思想简化时间复杂度，然后算长度来做乘法；另外大佬的heap（优先队列）做法实在是太眼前一亮了，没想到根据k的数据范围可以直接这么暴力，这样一下就简化很多了；另外就是大数精度的问题了，发现这个bug是因为这么多数乘在一起，不能就和标准答案差几吧；

自己的算法：
```python
class Solution(object):
    def maximumProduct(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        # test = [43, 57, 60, 70, 72, 84, 86, 92]
        # ttt_1 = 1
        # for i in range(len(test)):
        #     ttt_1 *= (test[i] % (1e9+7))
        # print("ttt_1: ", ttt_1%(1e9+7))
        
        c = 1000000007 # 这里换成1e9 + 7就过不了
        
        # 平均了乘积最大，好像有前缀和那感觉
        nums.sort()  # 从小到大排序
        print("nums: ", nums)
        tmp_idx = -1
        for i in range(len(nums)-1):
            if nums[i] < nums[i+1]:
                # 这里还是有问题？
                # print("k: ", k)
                # print("(nums[i+1] - nums[i]) * i: ", (nums[i+1] - nums[i]) * (i+1))
                if k > (nums[i+1] - nums[i]) * (i+1):
                    k -= (nums[i+1] - nums[i]) * (i+1)
                    nums[i] = nums[i+1]
                    
                else:
                    tmp_idx = i
                    break
        # print("first nums: ", nums)
        # print("tmp_idx: ", tmp_idx)
        # print("k:", k)
        
        if tmp_idx == -1:
            # 把剩余的k均匀展开到各个地方
            bias = k // len(nums)  # 每个能增加几
            k -= bias * len(nums)
            # print((nums[0] + bias + 1)**k)
            # print((nums[0] + bias)**(len(nums) - k))
            
            # res = ((nums[-1] + bias + 1)**k)  *  ((nums[-1] + bias)**(len(nums) - k))
            # 下边两行替代
            res = 1
            a1 = (nums[-1] + bias + 1)
            a2 = (nums[-1] + bias)
            for kl in range(k):
                res  = ((res % c) * (a1 % c)) % c
            for kl in range(len(nums) - k):
                res  = ((res % c) * (a2 % c)) % c
            
        else:
            # print("nums: ", nums)
            # print("nums[tmp_idx+1:]: ", nums[tmp_idx+1:])
            res = 1
            for i in range(tmp_idx+1, len(nums)):
                res = ((res%c) * (nums[i]%c)) % c
            
            t_nums = copy.deepcopy(nums[:tmp_idx+1])
            # print("t_nums: ", t_nums)
            
            bias = k // len(t_nums)  # 每个能增加几
            k -= bias * len(t_nums)
            
            t_res =  1
            a1 = (t_nums[-1] + bias + 1)
            a2 = (t_nums[-1] + bias)
            # t_res = ((t_nums[-1] + bias + 1)**k)  *  ((t_nums[-1] + bias)**(len(t_nums) - k))
            for kl in range(k):
                t_res  = ((t_res % c) * (a1 % c)) % c
            for kl in range(len(t_nums) - k):
                t_res  = ((t_res % c) * (a2 % c)) % c
            
            res = ((res%c) * (t_res%c)) % c
        
        return int(res)
```


抄大佬的堆的思路：
```python
class Solution(object):
    def maximumProduct(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        c = 1000000007 # 这里换成1e9 + 7就过不了
        import heapq
        heapq.heapify(nums)
        
        # 直接暴力模拟，k < 10^5
        while k > 0:
            tmp = heapq.heappop(nums)
            heapq.heappush(nums, tmp+1)
            k -= 1
        
        res = 1
        for i in range(len(nums)):
            res = ((res%c) * (nums[i]%c)) % c
        
        return res
```