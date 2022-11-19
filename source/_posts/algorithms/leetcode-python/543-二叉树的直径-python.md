---
title: 543.二叉树的直径-python
date: 2022-04-30 10:20:42
tags:
    - 树
    - 深度优先搜索
    - 二叉树
    - EASY
categories:
	- LeetCode-python
---

543.二叉树的直径（简单）

题目大意：给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

示例：
给定二叉树
```
          1
         / \
        2   3
       / \     
      4   5    

```

返回 **3**，它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]

**注意：**两结点之间的路径长度是以它们之间边的数目表示。


# 分析和解答

题目中给出了一个提示，就是说 **这条路径可能穿过也可能不穿过根节点**，那么换句话说，每个节点都有可能是 **路径的根节点**，这样应该可以判断出是经典的 **树形子结构dfs/dp题目**，关于该类型题目比较经典的感觉是[124.二叉树中的最大路径和 hard](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

**题目类型** 的细节总结和 **相似题目分析** 可见leetcode124题的博客

这个题目来说，坑点在于不是节点数目，而是边数目的表示，最后需要加减个1一般通过调试就可以完成了，然后路径上的话一般子结构返回值就是 `1+xxx` 这样的；另外想说的是这个题居然是简单题，和124本质上没有什么区别啊

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution(object):
    def __init__(self):
        self.max_len = -1e9  # 这里要新定义一个？
    
    def diameterOfBinaryTree(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        def dfs(root):
            if root is None:
                return 0
            max_left_len = dfs(root.left)
            max_right_len = dfs(root.right)

            self.max_len = max(self.max_len, 1 + max_left_len + max_right_len)


            return 1 + max(max_left_len, max_right_len)  # 子结构的加上根

        dfs(root)
        return self.max_len - 1
```