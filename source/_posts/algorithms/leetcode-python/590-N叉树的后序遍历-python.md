---
title: 590.N叉树的后序遍历-python
date: 2022-03-14 20:55:53
tags:
    - 栈
    - 树
    - 深度优先搜索
    - EASY
categories:
	- LeetCode-python
	- LeetCode每日一题
---

590.N叉树的后序遍历（简单）

题目大意：

给定一个 n 叉树的根节点  ```root``` ，返回 其节点值的 **后序遍历** 。

n 叉树 在输入中按层序遍历进行序列化表示，每组子节点由空值 ```null``` 分隔（请参见示例）。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/n-ary-tree-postorder-traversal/)

给定一个 n 叉树的根节点  ```root``` ，返回 其节点值的 **后序遍历** 。

n 叉树 在输入中按层序遍历进行序列化表示，每组子节点由空值 ```null``` 分隔（请参见示例）。

示例1：

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-14-21-13-50.png)
```
输入：root = [1,null,3,2,4,null,5,6]
输出：[5,6,3,2,4,1]
```

示例2：

![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-14-21-14-19.png)
```
输入：root = [1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14]
输出：[2,6,14,11,7,3,12,8,4,13,9,10,5,1]
```

提示：
- 节点总数在范围 ```[0, 10^4]``` 内
- ```0 <= Node.val <= 10^4```
- n 叉树的高度小于或等于 ```1000```

# 分析和解答

## 解法1：递归

这个题写完内部的dfs函数居然忘了调用了哈哈哈，很简单和二叉树的一样就行

```python
"""
# Definition for a Node.
class Node(object):
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution(object):
    def postorder(self, root):
        """
        :type root: Node
        :rtype: List[int]
        """
        # 递归
        res = []
        def dfs_postorder(root):
            if root is None:
                return
            for ch in root.children:
                dfs_postorder(ch)
            res.append(root.val)
        dfs_postorder(root)  # 居然是因为忘写了这句话
        return res
```


## 解法2：迭代

使用了前序遍历迭代的思想，自己写一写模拟就好，最后需要reverse过来，也就是 ```t_list = t_list[::-1]```

```python
"""
# Definition for a Node.
class Node(object):
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution(object):
    def postorder(self, root):
        """
        :type root: Node
        :rtype: List[int]
        """
        # 迭代，这里可以借助前序遍历
        res = []
        stack = []
        if root is not None:
            stack.append(root)
        while len(stack):
            tmp = stack[0]  # 左边栈顶，右边栈底
            stack = stack[1:]
            res.append(tmp.val)
            for ch in tmp.children:
                if ch is not None:
                    stack.insert(0, ch)
        return res[::-1]
```