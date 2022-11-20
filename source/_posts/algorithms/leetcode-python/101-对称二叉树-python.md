---
title: 101.对称二叉树-python
date: 2022-02-14 14:52:02
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

101.对称二叉树（简单）

题目大意：

给你一个二叉树的根节点 ```root``` ， 检查它是否轴对称。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/symmetric-tree/)

给你一个二叉树的根节点 ```root``` ， 检查它是否轴对称。

示例1：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-02-14-15-53-42.png)
```
输入：root = [1,2,2,3,4,4,3]
输出：true
```

示例2：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-02-14-15-53-53.png)
```
输入：root = [1,2,2,null,3,null,3]
输出：false
```

提示：
- 树中节点数目在范围 ```[1, 1000]``` 内
- -100 <= Node.val <= 100


# 题目分析和解答

这种题又和完全的自递归题目有些区别，需要再构造一个dfscheck的函数，来套一下递归。

这个```dfs_check()```函数的输入是两个root这样的感觉，先判断当前节点的左右是否相等，然后往下嵌套递归，判断当前节点的左的左和当前节点右的右，还有当前节点左的右和当前节点右的左是不是一样就行了，在前边要加入一些None的出口递归条件

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution(object):
    def isSymmetric(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """

        def dfs_check(root1, root2):
            """
            用于判断一个点的左右子树是否轴对称，首次输入两个都是这个树的根
            """
            if root1 is None and root2 is None:
                return True
            if root1 is None or root2 is None:
                return False
            
            # 当前节点的左右，然后递归下一层的左右！
            return root1.val == root2.val and dfs_check(root1.left, root2.right) and dfs_check(root1.right, root2.left)

        return dfs_check(root, root)
```