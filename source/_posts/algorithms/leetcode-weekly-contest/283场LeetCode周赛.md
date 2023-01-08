---
title: 283场LeetCode周赛
date: 2022-03-07 18:35:08
tags:
    - 字符串
    - 贪心
    - 数组
    - 数学
    - 排序
    - 树
    - 深度优先搜索
    - 广度优先搜索
    - 哈希表
    - 二叉树
    - EASY
    - MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

这次感觉就是从头卡到尾哈哈哈，每个题或多或少都卡了一下，感觉近几次做题真的思想越来越僵化了，也可能和太久没写代码了有关吧，2月轻松了一个月，3月开始真的忙碌了hhh，等哪天闲下来要完整总结下做题用到的一些常用结构了；

看了下有写笔记的是273到283，算上新年鸽了一次正好十次，感觉可以十次总结下，之后找时间先写个新的博客~

第一题：上来就不顺了下，要考虑python怎么做这种字符串的操作，查了一下才知道```ord()```可以把字母转化为ASCII表，然后再用```chr()```把ASCII字符转换回串，然后实际上是一个简单的二重循环遍历；

第二题：这个题就感受到上难度了hhh，一上来必然很容易想到暴力遍历法，然后看了下数据范围估计超时间复杂度了，之后就是一个想优化的过程，大佬提了下排序后倒是能想到思路，就是直接用求和公式统计下差，然后最后看看全都补完了还剩多少个剩余的，剩余的其实就是现有max+1在往后求和k个就行了，中间写起来细节感觉很多，还是被坑了不少的。另外还有一个哨兵机制，能简化下第一个位置的计算的感觉？；

第三题：好像还是在周赛里第一次见到这种构建的题，这次想到的比较慢，以后应该想构建题就直接搞哈希表（mapping），开始是一种超级暴力的思路，每次找一个节点，都去dfs一遍看看能不能插入，能插入就插入，不能插入就下一个，但实际上mapping后每个value都能找到对应的节点，这样插入的时候实际上就是把“地址”拼接在一起了；

第四题：鸽了鸽了；

<!--more-->

# 第一题：2194.Excel表中某个范围内的单元格

[题目链接](https://leetcode-cn.com/problems/cells-in-a-range-on-an-excel-sheet/)

## 题目大意

Excel 表中的一个单元格 ```(r, c)``` 会以字符串 ```"<col><row>"``` 的形式进行表示，其中：

- ```<col>``` 即单元格的列号 ```c``` 。用英文字母表中的 **字母** 标识。
  - 例如，第 ```1``` 列用 ```'A'``` 表示，第 ```2``` 列用 ```'B'``` 表示，第 ```3``` 列用 ```'C'``` 表示，以此类推。
- ```<row>``` 即单元格的行号 ```r``` 。第 ```r``` 行就用 整数 ```r``` 标识。

给你一个格式为 ```"<col1><row1>:<col2><row2>"``` 的字符串 ```s``` ，其中 ```<col1>``` 表示 ```c1``` 列，```<row1>``` 表示 ```r1``` 行，```<col2>``` 表示 ```c2``` 列，```<row2>``` 表示 ```r2``` 行，并满足 ```r1 <= r2``` 且 ```c1 <= c2``` 。

找出所有满足 ```r1 <= x <= r2``` 且 ```c1 <= y <= c2``` 的单元格，并以列表形式返回。单元格应该按前面描述的格式用 **字符串** 表示，并以 **非递减** 顺序排列（先按列排，再按行排）。

示例1：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-07-19-17-22.png)
```
输入：s = "K1:L2"
输出：["K1","K2","L1","L2"]
解释：
上图显示了列表中应该出现的单元格。
红色箭头指示单元格的出现顺序。
```

示例2：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-07-19-19-50.png)
```
输入：s = "A1:F1"
输出：["A1","B1","C1","D1","E1","F1"]
解释：
上图显示了列表中应该出现的单元格。 
红色箭头指示单元格的出现顺序。
```

提示：
- ```s.length == 5```
- ```'A' <= s[0] <= s[3] <= 'Z'```
- ```'1' <= s[1] <= s[4] <= '9'```
- ```s``` 由大写英文字母、数字、和 ```':'``` 组成

## 分析和解答

```ord("a")```是把一个字符转化为ASCII码表示，例如该句会输出```97```

```chr(97)```是把一个ASCII码（或者也不一定，就是数字）转化为字符表示，例如该句会输出```a```

知道上边两个操作后怎么遍历就不是难事了，代码如下所示
```python
class Solution(object):
    def cellsInRange(self, s):
        """
        :type s: str
        :rtype: List[str]
        """
        s_1 = s[0]
        n_1 = s[1]
        s_2 = s[3]
        n_2 = s[4]
        
        res = []
        for i in range(ord(s_1), ord(s_2)+1):
            for j in range(int(n_1), int(n_2)+1):
                # print(chr(i))
                # print(str(j))
                res.append("".join([chr(i), str(j)])) 
                
        return res
```


# 第二题：6017.向数组中追加K个整数

[题目链接](https://leetcode-cn.com/problems/append-k-integers-with-minimal-sum/)

## 题目大意

给你一个整数数组 ```nums``` 和一个整数 ```k``` 。请你向 nums 中追加 k 个 未 出现在 nums 中的、**互不相同** 的 **正** 整数，并使结果数组的元素和 **最小** 。

返回追加到 ```nums``` 中的 ```k``` 个整数之和。

示例1：
```
输入：nums = [1,4,25,10,25], k = 2
输出：5
解释：在该解法中，向数组中追加的两个互不相同且未出现的正整数是 2 和 3 。
nums 最终元素和为 1 + 4 + 25 + 10 + 25 + 2 + 3 = 70 ，这是所有情况中的最小值。
所以追加到数组中的两个整数之和是 2 + 3 = 5 ，所以返回 5 。
```

示例2：
```
输入：nums = [5,6], k = 6
输出：25
解释：在该解法中，向数组中追加的两个互不相同且未出现的正整数是 1 、2 、3 、4 、7 和 8 。
nums 最终元素和为 5 + 6 + 1 + 2 + 3 + 4 + 7 + 8 = 36 ，这是所有情况中的最小值。
所以追加到数组中的两个整数之和是 1 + 2 + 3 + 4 + 7 + 8 = 25 ，所以返回 25 。
```

提示：
- ```1 <= nums.length <= 10^5```
- ```1 <= nums[i], k <= 10^9```

## 分析和解答

这个题就感受到上难度了hhh，一上来必然很容易想到暴力遍历法，然后看了下数据范围估计超时间复杂度了

之后就是一个想优化的过程，大佬提了下排序后倒是能想到思路，就是直接用求和公式统计下差，然后最后看看全都补完了还剩多少个剩余的，剩余的其实就是现有max+1在往后求和k个就行了

中间写起来细节感觉很多，还是被坑了不少的。

另外还有一个哨兵机制，能简化下第一个位置的计算的感觉？；

```python
class Solution(object):
    def minimalKSum(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        orig_k = k
        # res = sum(nums)
        res = 0
        nums.append(0)
        nums.sort()
        # print(nums)
        
        for i in range(1, len(nums)):
            tmp = nums[i] - nums[i-1]  - 1  # 差
            # print(tmp)
            if k > tmp:
                # 求和tmp个
                if (nums[i] - 1) >= nums[i-1] + 1:  
                    res += (((nums[i-1] + 1) +  (nums[i] - 1)) * tmp) // 2
                    # print('a', nums[i-1] + 1, nums[i] - 1)
                    k -= tmp
            else:
                # 求和k个
                res += (((nums[i-1] + 1) +  (nums[i-1] + k)) * k) // 2
                # print('b', nums[i-1] + 1,nums[i-1] + k)
                k -= tmp
                break
          
        # print(res)
        # print(k)
        if k > 0:  # 某些问题？
            # 如果全部中间不够了
            res += (max(nums) + 1 + max(nums) + k) * k // 2
            
        return res
```


# 第三题：2196.根据描述创建二叉树

[题目链接](https://leetcode-cn.com/problems/create-binary-tree-from-descriptions/)

## 题目大意

给你一个二维整数数组 descriptions ，其中 descriptions[i] = [parenti, childi, isLefti] 表示 parenti 是 childi 在 二叉树 中的 父节点，二叉树中各节点的值 互不相同 。此外：

- 如果 isLefti == 1 ，那么 childi 就是 parenti 的左子节点。
- 如果 isLefti == 0 ，那么 childi 就是 parenti 的右子节点。

请你根据 ```descriptions``` 的描述来构造二叉树并返回其 **根节点** 。

测试用例会保证可以构造出 **有效** 的二叉树。

示例1：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-07-19-40-30.png)
```
输入：descriptions = [[20,15,1],[20,17,0],[50,20,1],[50,80,0],[80,19,1]]
输出：[50,20,80,15,17,19]
解释：根节点是值为 50 的节点，因为它没有父节点。
结果二叉树如上图所示。
```

示例2：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-07-19-41-17.png)
```
输入：descriptions = [[1,2,1],[2,3,0],[3,4,1]]
输出：[1,2,null,null,3,4]
解释：根节点是值为 1 的节点，因为它没有父节点。 
结果二叉树如上图所示。
```

提示：
- ```1 <= descriptions.length <= 10^4```
- ```descriptions[i].length == 3```
- ```1 <= parenti, childi <= 10^5```
- ```0 <= isLefti <= 1```
- ```descriptions``` 所描述的二叉树是一棵有效二叉树


## 分析和解答

好像还是在周赛里第一次见到这种构建的题，这次想到的比较慢，以后应该想构建题就直接搞哈希表（mapping）

开始是一种超级暴力的思路，每次找一个节点，都去dfs一遍看看能不能插入，能插入就插入，不能插入就下一个

但实际上mapping后每个value都能找到对应的节点，这样插入的时候实际上就是把“地址”拼接在一起了；

两轮，第一轮存节点+直接建树，然后第二轮找root作为返回；

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution(object):
    def createBinaryTree(self, descriptions):
        """
        :type descriptions: List[List[int]]
        :rtype: Optional[TreeNode]
        """
        
        def pre_insert(root, item):
            if root is None:
                return False
            
            if root.val == item[0]:
                # pre_insert(root, )
                if item[2] == 1:
                    root.left = TreeNode(val=item[1])
                else:
                    root.right = TreeNode(val=item[1])
                return True
            
            return pre_insert(root.left, item) or pre_insert(root.right, item)
        
        
        mapping = {}
        have_father = {}
        root = None
        
        for i, des in enumerate(descriptions):
            
            if mapping.get(des[0]) is None:
                mapping[des[0]] = TreeNode(val=des[0])
            if mapping.get(des[1]) is None:  
                mapping[des[1]] = TreeNode(val=des[1])
            
            # 边记录边建树
            if des[2] == 1:
                mapping[des[0]].left = mapping[des[1]]  
            else:
                mapping[des[0]].right = mapping[des[1]]
            
            have_father[des[1]] = True
        
        
        # print(have_father)
        
        
        for i, des in enumerate(descriptions):
            if have_father.get(des[0]) is None:
                return mapping[des[0]]
```
