---
title: 589.N叉树的前序遍历-python
date: 2022-03-14 20:56:17
tags:
    - 栈
    - 树
    - 深度优先搜索
    - EASY
categories:
	- LeetCode-python
	- LeetCode每日一题
---

589.N叉树的前序遍历（简单）

题目大意：

给定一个 n 叉树的根节点  ```root``` ，返回 其节点值的 **前序遍历** 。

n 叉树 在输入中按层序遍历进行序列化表示，每组子节点由空值 ```null``` 分隔（请参见示例）。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/)

给定一个 n 叉树的根节点  ```root``` ，返回 其节点值的 **前序遍历** 。

n 叉树 在输入中按层序遍历进行序列化表示，每组子节点由空值 ```null``` 分隔（请参见示例）。

示例1：

![](/images/2022-03-14-21-13-50.png)
```
输入：root = [1,null,3,2,4,null,5,6]
输出：[1,3,5,6,2,4]
```

示例2：

![](/images/2022-03-14-21-14-19.png)
```
输入：root = [1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14]
输出：[1,2,3,6,7,11,14,4,8,12,5,9,13,10]
```

提示：
- 节点总数在范围 ```[0, 10^4]``` 内
- ```0 <= Node.val <= 10^4```
- n 叉树的高度小于或等于 ```1000```


# 分析和解答

## 解法1：递归

这个题要是用递归的话那是绝对的简单题，N叉树就感觉把所有children走一遍就行了，和二叉树的前序遍历递归完全是一个模板

```python
"""
# Definition for a Node.
class Node(object):
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution(object):
    def preorder(self, root):
        """
        :type root: Node
        :rtype: List[int]
        """
        # 递归
        res = []
        def dfs_preorder(root):
            if root is None:
                return
            res.append(root.val)
            for ch in root.children:
                dfs_preorder(ch)     
        dfs_preorder(root)
        return res
```


## 解法2：迭代

N叉树的前序遍历和后序遍历的迭代法本质就是栈的应用，前序遍历是每次弹出来就加，而后序遍历要在每次加的基础上最后reverse一下

在自己设定好栈顶和栈底的基础上，按照操作来就可以了，手写模拟下很快就能找到规律

```python
"""
# Definition for a Node.
class Node(object):
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution(object):
    def preorder(self, root):
        """
        :type root: Node
        :rtype: List[int]
        """
        # 前序遍历用栈实现非递归
        res = []
        stack = []
        if root is not None:
            stack.append(root)
        while len(stack):
            tmp = stack[0]  # 左边是栈顶，右边是栈底
            stack = stack[1:]
            res.append(tmp.val)
            # tmp.children是一个list，倒序入栈，这个还是相对好像的吧
            for i in range(len(tmp.children)-1, -1, -1):
                if tmp.children[i] is not None:
                    stack.insert(0, tmp.children[i])  # 在位置0insert
                    # print([a.val for a in stack])

        return res
```