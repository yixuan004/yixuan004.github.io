---
title: 700.二叉搜索树中的搜索-python
date: 2021-11-29 22:46:10
tags:
    - 树
    - 二叉搜索树
    - 二叉树
    - EASY
categories:
	- LeetCode-python
---

700. 二叉树中的搜索（简单）

题目大意：给定二叉搜索树（BST）的根节点和一个值。 你需要在BST中找到节点值等于给定值的节点。 返回以该节点为根的子树。 如果节点不存在，则返回 NULL。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

给定二叉搜索树（BST）的根节点和一个值。 你需要在BST中找到节点值等于给定值的节点。 返回以该节点为根的子树。 如果节点不存在，则返回 NULL。

例如，
```
给定二叉搜索树:

        4
       / \
      2   7
     / \
    1   3

和值: 2
```
应该返回如下子树：
```
      2     
     / \   
    1   3
```

在上述示例中，如果要找的值是 5，但因为没有节点值为 5，我们应该返回 NULL。



# 分析和解答

这个题需要明白，return type如果是有类型的，例如这里是TreeNode类型，在此函数内部的递归循环中，每个返回值也要是TreeNode类型的，类似于这题里边的```return self.searchBST(root.left, val)```


TreeNode类，每次手写一下保持手感
```python
class TreeNode(object):
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

求解
```python
class Solution(object):
    def searchBST(self, root, val):
        """
        :type root: TreeNode
        :type val: int
        :rtype: TreeNode
        """
        if root is None: # None要放到前边
            return None
        if val == root.val:
            return root
        if val < root.val:
            return self.searchBST(root.left, val)
        elif val > root.val:
            return self.searchBST(root.right, val)
```