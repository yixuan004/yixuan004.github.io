---
title: 429.N叉树的层序遍历-python
date: 2022-03-14 20:55:27
tags:
    - 树
    - 广度优先搜索
    - MEDIUM
categories:
	- LeetCode-python
---

429.N叉树的层序遍历（中等）

题目大意：

给定一个 N 叉树，返回其节点值的层序遍历。（即从左到右，逐层遍历）。

树的序列化输入是用层序遍历，每组子节点都由 null 值分隔（参见示例）。

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/n-ary-tree-level-order-traversal/)

给定一个 N 叉树，返回其节点值的层序遍历。（即从左到右，逐层遍历）。

树的序列化输入是用层序遍历，每组子节点都由 null 值分隔（参见示例）。

示例1：

![](/images/2022-03-14-21-02-01.png)
```
输入：root = [1,null,3,2,4,null,5,6]
输出：[[1],[3,2,4],[5,6]]
```

示例2：

![](/images/2022-03-14-21-02-42.png)
```
输入：root = [1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14]
输出：[[1],[2,3,4,5],[6,7,8,9,10],[11,12,13],[14]]
```

提示：
- 树的高度不会超过 ```1000```
- 树的节点总数在 ```[0, 10^4]``` 之间

# 分析和解答

层序遍历本质上是广度优先，广度优先本质上就是bfs（？好像在说废话hhh）

这个题需要特别注意下把层序遍历 ```append``` 成这个嵌套的样子 ```[[], [], []] ``` ，这里的思路是每次记录下当前层的数目，然后在 ```pop-appendtail``` 的时候只把当前层这么多个的给 ```pop``` 出来，使用一个中间的 ```tmp_ans``` 来存储每层，然后一层一层的 ```append```


```python
"""
# Definition for a Node.
class Node(object):
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution(object):
    def levelOrder(self, root):
        """
        :type root: Node
        :rtype: List[List[int]]
        """
        res = []
        queue = []  # 左出，右进
        if root is not None:
            queue.append(root)

        # 层序遍历代码写成一体化的，因为层序遍历还要兼顾层级的输出
        while len(queue):
            # 每次都把当前这一层走完，然后append
            tmp_ans = []
            now_level_len = len(queue)
            for i in range(now_level_len):  # 只是为了执行这么多次
                tmp_q = queue[0]
                queue = queue[1:]
                tmp_ans.append(tmp_q.val)
                for ch in tmp_q.children:
                    queue.append(ch)
            res.append(tmp_ans)

        return res
```