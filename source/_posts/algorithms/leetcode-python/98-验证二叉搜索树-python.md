---
title: 98.验证二叉搜索树-python
date: 2022-03-07 22:11:18
tags:
    - 树
    - 深度优先搜索
    - 二叉搜索树
    - 二叉树
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

98.验证二叉搜索树（中等）

题目大意：

给你一个二叉树的根节点 ```root``` ，判断其是否是一个有效的二叉搜索树。

有效 二叉搜索树定义如下：

- 节点的左子树只包含 **小于** 当前节点的数。
- 节点的右子树只包含 **大于** 当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

<!--more-->

# 题目

给你一个二叉树的根节点 ```root``` ，判断其是否是一个有效的二叉搜索树。

有效 二叉搜索树定义如下：

- 节点的左子树只包含 **小于** 当前节点的数。
- 节点的右子树只包含 **大于** 当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

示例1：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-07-22-56-46.png)
```
输入：root = [2,1,3]
输出：true
```

示例2：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-03-07-23-10-32.png)
```
输入：root = [5,1,4,null,null,3,6]
输出：false
解释：根节点的值是 5 ，但是右子节点的值是 4 。
```

提示：
- 树中节点数目范围在 ```[1, 10^4]``` 内
- ```-2^31 <= Node.val <= 2^31 - 1```


# 分析和解答

## 解法一：自己称为上界下界法

该种方法不需要用到额外的变量那种感觉， 开始从一个最小和一个最大开始作为入口，然后递归的判断每个节点是不是在是不是在这个范围内即可

（二叉搜索树的性质：各个节点的```val```都不能相同）

对于根节点，其肯定包含在最开始的上界/下界内，然后就开始递归，根的左子树必须在```(最小, 根的val)```开区间内，右子树必须在```(根的val, 最大)```开区间内

另外注意，因为是判断的题，最后要返回一个```bool```的值，所以自递归的时候每个地方也要返回一个```True/False```

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution(object):
    def isValidBST(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """

        # 上界-下界法
        def dfs(root, lower, upper):

            # 出口条件    
            if root is None:
                return True

            # False的条件
            if root.val <= lower or root.val >= upper:
                return False
            
            # 左子树的值应该在lower，root.val开区间之间
            
            if dfs(root.left, lower, root.val) is False:
                return False

            # 右子树的值应该在root.val，upper开区间之间
            if dfs(root.right, root.val, upper) is False:
                return False

            return True

        return dfs(root, -1e15, 1e15)
```


## 解法二：全局变量法

二叉搜索树本质上就是中序遍历要是递增的，所以使用中序遍历的思想搞一个自递归的函数就差不多了

同样是注意，因为自递归的，每个地方要有个```bool```类的返回值

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution(object):
    def __init__(self):
        self.pre_val = -1e15  # 写在这里的当做全局变量了


    def isValidBST(self, root):
        """
        # 这里使用二叉树中序遍历的思想，把这个函数制作为一个自递归的
        :type root: TreeNode
        :rtype: bool
        """
        # 出口
        if root is None:
            return True
        
        if self.isValidBST(root.left) is True: # 中序的基础上要有返回值
            if root.val > self.pre_val:
                self.pre_val = root.val
            else:
                return False
            
            return self.isValidBST(root.right)

        return False
```