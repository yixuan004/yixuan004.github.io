---
title: python-ACM模式的输入输出
date: 2022-03-23 10:45:31
tags:
	- LeetCode-python
---

在笔试环节做题的时候，题目大多数情况会是以ACM模式的输入输出构造，

<!--more-->

Reference: https://blog.csdn.net/mmmmonkeyfei/article/details/118863773

# 各种类型情况总结

TODO


# PTA的平台可以用python交，POJ用python交不了

## example1: POJ2524：并查集题目

[题目链接](http://poj.org/problem?id=2524)

除了数据输入输出的提醒以外，也还提醒了以前很多在C语言算法题的时候，用的数组都可以用哈希表替代

```python
# 这里是哈希表
par = {}
vis = {}


def get_root(a):
	"""
	找根（找部落）
	"""
	if par[a] != a:
		par[a] = get_root(par[a])
	return par[a]


def merge(a, b):
	"""
	把p2往p1聚类
	"""
	p1 = get_root(a)
	p2 = get_root(b)
	if p1 != p2:
		par[p2] = p1

if __name__ == '__main__':
	
	js = 0
	while True:
		n, m = map(int, input().split(" "))
		if n == 0 and m == 0:
			break

		# 这里重置了
		vis = {}
		for i in range(1, n+1):
			par[i] = i

		for i in range(m):
			a, b = map(int, input().split(" "))
			merge(a, b)

		count = 0
		for i in range(1, n+1):
			if vis.get(get_root(i)) == 1:
				continue
			else:
				count += 1
				vis[get_root(i)] = 1

		js += 1

		print("Case %d: %d\n"  % (js, count), end='')
```


## example2-尝试输入输出字符串，GPLT轮着输出几次

[题目链接](https://pintia.cn/problem-sets/994805046380707840/problems/994805113036587008)

```python
from collections import Counter

class Solution(object):
    def __init__(self):
        pass
    
    def output_gplt(self, s):
        cnt_dict = Counter(s)
        return_list = []
        cnt_g = cnt_dict.get('g', 0) + cnt_dict.get('G', 0)
        cnt_p = cnt_dict.get('p', 0) + cnt_dict.get('P', 0)
        cnt_l = cnt_dict.get('l', 0) + cnt_dict.get('L', 0)
        cnt_t = cnt_dict.get('t', 0) + cnt_dict.get('T', 0)


        while cnt_g != 0 or cnt_p != 0 or cnt_l != 0 or cnt_t != 0:
            if cnt_g != 0:
                return_list.append('G')
                cnt_g -= 1

            if cnt_p != 0:
                return_list.append('P')
                cnt_p -= 1

            if cnt_l != 0:
                return_list.append('L')
                cnt_l -= 1

            if cnt_t != 0:
                return_list.append('T')
                cnt_t -= 1

        return ''.join(return_list)


if __name__ == '__main__':
    s = str(input())
    res = Solution().output_gplt(s)
    print(res)
```


## example3-