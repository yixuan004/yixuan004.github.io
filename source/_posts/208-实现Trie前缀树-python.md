---
title: 208.实现Trie前缀树-python
date: 2021-12-13 11:37:15
tags:
    - 设计
    - 字典树
    - 哈希表
    - 字符串
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode-热题 HOT 100
---

208.最长递增子序列（中等）

题目大意：Trie（发音类似 "try"）或者说 前缀树 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。

请你实现 Trie 类：

Trie() 初始化前缀树对象。
- void insert(String word) 向前缀树中插入字符串 word 。
- boolean search(String word) 如果字符串 word 在前缀树中，返回 true（即，在检索之前已经插入）；否则，返回 false 。
- boolean startsWith(String prefix) 如果之前已经插入的字符串 word 的前缀之一为 prefix ，返回 true ；否则，返回 false 

<!--more-->

# 题目

[题目链接](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

Trie（发音类似 "try"）或者说 前缀树 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。

请你实现 Trie 类：

Trie() 初始化前缀树对象。
- void insert(String word) 向前缀树中插入字符串 word 。
- boolean search(String word) 如果字符串 word 在前缀树中，返回 true（即，在检索之前已经插入）；否则，返回 false 。
- boolean startsWith(String prefix) 如果之前已经插入的字符串 word 的前缀之一为 prefix ，返回 true ；否则，返回 false 

示例：
```
输入
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
输出
[null, null, true, false, true, null, true]

解释
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // 返回 True
trie.search("app");     // 返回 False
trie.startsWith("app"); // 返回 True
trie.insert("app");
trie.search("app");     // 返回 True
```

提示：
- 1 <= word.length, prefix.length <= 2000
- word 和 prefix 仅由小写英文字母组成
- insert、search 和 startsWith 调用次数 总计 不超过 3 * 104 次

# 分析和解答

Trie树还是很经典的，同样是一个妙脆妙妙角一样妙的结构。这个题主要需要记住的是首先构建一个TrieNode，代表Trie树中的每个节点：

```python
class TrieNode(object):
    def __init__(self):
        self.mapping = {}
        self.is_end = False
```

在之后想想每次插入过程，**每次都从root开始进行插入**，第一次插入一个单词如“apple”的时候，首先是“a“，令```node=root```，因为现在node肯定是空的，所以使得```node.mapping['a']```为一个新的TrieNode，之后走到这个TrieNode上，```node=node.mapping.get(c)```。**特别注意，这里的mapping key是每个char，value是TrieNode类型的**，在插入的最后，要使得node.is_end=True，标志出这个单词的结尾

在这样的插入条件下，第一次插入就是构建了一个超长的串。第二次插入的时候由于第一次插入过程中，有些节点已经可以共用了，所以就有“前缀”的感觉了，这样的感觉就是顺着串走，直到遇到新的后，就建立走到新的分支上，要想到Trie树并不是只插入一个字符串就结束了

找prefix和word都很简单，找word要多加一个判断is_end


题目解答：

```python
class TrieNode(object):
    """
    实现一个TrieNode进行封装
    """
    def __init__(self):
        self.mapping = {}
        self.is_end = False

class Trie(object):

    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        """
        :type word: str
        :rtype: None
        注意这个insert函数会多次执行，
        """
        node = self.root
        for i, c in enumerate(word):
            if node.mapping.get(c) is None:
                node.mapping[c] = TrieNode()  # 这里每次放入的是TreeNode
            node = node.mapping[c]
        node.is_end = True


    def search(self, word):
        """
        :type word: str
        :rtype: bool
        """
        node = self.root
        for i, c in enumerate(word):
            if node.mapping.get(c) is None:
                return False
            node = node.mapping[c]
        if node.is_end == True:
            return True
        else:
            return False
·

    def startsWith(self, prefix):
        """
        :type prefix: str
        :rtype: bool
        """
        node = self.root
        for i, c in enumerate(prefix):
            if node.mapping.get(c) is None:
                return False
            node = node.mapping[c]
        return True


# Your Trie object will be instantiated and called as such:
# obj = Trie()
# obj.insert(word)
# param_2 = obj.search(word)
# param_3 = obj.startsWith(prefix)
```