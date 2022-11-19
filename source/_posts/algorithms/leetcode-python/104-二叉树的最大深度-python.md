---
title: 104.二叉树的最大深度-python
date: 2022-01-16 23:43:31
tags:
    - 树
    - 深度优先搜索
    - 广度优先搜索
    - 二叉树
    - EASY
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

104.二叉树的最大深度（简单）

题目大意：

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

示例：
给定二叉树```[3,9,20,null,null,15,7]```，
```
    3
   / \
  9  20
    /  \
   15   7
```
返回它的最大深度3。

# 题目分析和解答

这个题是一个典型的”自递归“系列的题目，首先复习一下树节点的写法（建议每个二叉树的题目都复习一下）

```python
class TreeNode(object):
    def __init__(self, val=-1, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

复习Solution的写法和题解，注意这个maxDepth函数，他的返回值是一个int，代表直接调用这个函数就要得到最终答案的depth返回值，所以感觉是一个自递归的题目了，这种的递归dfs还是很好写的
```python
class Solution(object):
    def maxDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if root is None:
            return 0
        else:
            return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```