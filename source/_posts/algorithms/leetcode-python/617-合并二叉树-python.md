---
title: 617.合并二叉树-python
date: 2022-02-14 14:49:48
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

617.合并二叉树（简单）

题目大意：

给你两棵二叉树： ```root1``` 和 ```root2``` 。

想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，**不为** null 的节点将直接作为新二叉树的节点。

返回合并后的二叉树。

**注意**: 合并过程必须从两个树的根节点开始。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/merge-two-binary-trees/)

给你两棵二叉树： ```root1``` 和 ```root2``` 。

想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，**不为** null 的节点将直接作为新二叉树的节点。

返回合并后的二叉树。

**注意**: 合并过程必须从两个树的根节点开始。

示例1：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-02-14-15-33-49.png)
```
输入：root1 = [1,3,2,5], root2 = [2,1,3,null,4,null,7]
输出：[3,4,5,5,4,null,7]
```

示例2：
```
输入：root1 = [1], root2 = [1,2]
输出：[2,2]
```

提示：
- 两棵树中的节点数目在范围 ```[0, 2000]``` 内
- -10^4 <= Node.val <= 10^4


# 题目分析和解答

这个题有一种自递归的感觉，题目的初始Solution类如下：

```python
class Solution(object):
    def mergeTrees(self, root1, root2):
        """
        :type root1: TreeNode
        :type root2: TreeNode
        :rtype: TreeNode
        """
```

rtype是一个TreeNode类型的，所以能有一种自递归的感觉，先建立TreeNode，然后

这种自递归的题还是可以先把主干写好了，再决定递归出口那样的感觉。

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution(object):
    def mergeTrees(self, root1, root2):
        """
        :type root1: TreeNode
        :type root2: TreeNode
        :rtype: TreeNode
        """
        # 整个题目有一种自递归的感觉
        if root1 is None:
            return root2
        if root2 is None:
            return root1
        
        merged_node = TreeNode(val=root1.val + root2.val)
        merged_node.left = self.mergeTrees(root1.left, root2.left)
        merged_node.right = self.mergeTrees(root1.right, root2.right)

        return  merged_node
```