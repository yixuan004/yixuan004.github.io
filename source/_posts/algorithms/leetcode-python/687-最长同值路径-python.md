---
title: 687.最长同值路径-python
date: 2022-04-30 10:49:27
tags:
    - 树
    - 深度优先搜索
    - 二叉树
    - MEDIUM
categories:
	- LeetCode-python
---

687.最长同值路径（中等）

题目大意：

给定一个二叉树的 `root` ，返回 最长的路径的长度 ，这个路径中的 每个节点具有相同值 。 这条路径可以经过也可以不经过根节点。

**两个节点之间的路径长度** 由它们之间的边数表示。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/longest-univalue-path/)

给定一个二叉树的 `root` ，返回 最长的路径的长度 ，这个路径中的 每个节点具有相同值 。 这条路径可以经过也可以不经过根节点。

**两个节点之间的路径长度** 由它们之间的边数表示。

示例1：

![](/images/2022-04-30-10-51-58.png)
```
输入：root = [5,4,5,1,1,5]
输出：2
```

示例2：

![](/images/2022-04-30-10-52-12.png)
```
输入：root = [1,4,5,4,4,5]
输出：2
```

提示：
- 树的节点数的范围是 `[0, 10^4]`
- `-1000 <= Node.val <= 1000`
- 树的深度将不超过 `1000`

# 分析和解答

比较经典能看出来是 `树中子结构dfs/dp的题目`，关于该类型题目比较经典的感觉是[124.二叉树中的最大路径和 hard](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

**题目类型** 的细节总结和 **相似题目分析** 可见leetcode124题的博客

对于这个题目来说，子结构下的返回值可能会有none的情况，另外也需要全局比较，总结来说和124题目比较像，如果再做到这题的话不知道能不能做出来了

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution(object):
    """
    典型树中子结构搜索的题目
    """
    def __init__(self):
        self.max_res = 0

    def longestUnivaluePath(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """

        # 1. 先写dfs递归函数
        def dfs(root):
            if root is None:
                return 0

            # 3. 子结构搜索任务要用后序遍历，这里写递归两次
            left = dfs(root.left)
            right = dfs(root.right)

            # 4. 写全局的那个逻辑，全局结构要把左右都考虑上
            tmp_res = 0  # 路径长度，所以这里是0开始
            if root.left is not None and root.val == root.left.val:
                tmp_res += left
            if root.right is not None and root.val == root.right.val:
                tmp_res += right
            self.max_res = max(self.max_res, tmp_res)

            # 2. 对于树中的子结构搜索任务，写返回值，看看不同种情况下子结构该怎么返回
            if (root.left is not None and root.val == root.left.val) and (root.right is not None and root.val == root.right.val):
                return max(left, right) + 1
            elif root.left is not None and root.val == root.left.val:
                return left + 1
            elif root.right is not None and root.val == root.right.val:
                return right + 1
            else:
                return 1  # 为什么是return1，只能暂时理解为每个的base都是1了
        
        dfs(root)
        return self.max_res
```
