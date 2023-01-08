---
title: 算法模板整理-python
date: 2023-01-01 11:36:11
tags:
	- algorithms
categories:
	- algorithms
---

# 算法模板整理-python

## 1. 常用ACM模式输入/输出

- 输入

```python
n = int(input())

s = input().strip()

nums = list(map(int, input().strip().split()))

# 这里直接try-except可能会让别的报错弹不出来
while True:
    try:
        a, b = map(int, input().strip().split())
        print("input_EOFError: ", a, b)
    except EOFError:
        break
```

- 小数输出相关+小数计算，需要`from decimal import Decimal`

```python
# 小数的输出相关，有一次leetcode算斜率被这个精度问题给卡了，希望不会这么折磨人
## 四舍五入，由于直接用.2f和round可能会有不准确的舍入问题，这里可能需要decimal模块
>>> from decimal import Decimal
>>> a = Decimal(0.333333333)
>>> b = Decimal(0.222222222)
>>> c = a - b
>>> c
Decimal('0.1111111110000000123942953678')

## 保留几位小数由像第二个括号中的几位小数决定，即保留两位小数，精确到0.01
## 如果想要四舍五入保留整数，那么第二个括号中就应该为"1."
>>> a = 0.125
>>> a_t = Decimal(a).quantize(Decimal("0.01"), rounding = "ROUND_HALF_UP")
>>> print(a_t)
0.13

## rounding参数，熟练使用ROUND_DOWN和ROUND_UP模式，感觉是向下舍和向上直接进位
>>> a = 0.128
>>> a_t = Decimal(a).quantize(Decimal("0.01"), rounding = "ROUND_DOWN")
>>> print(a_t)
0.12

>>> a = 0.121
>>> a_t = Decimal(a).quantize(Decimal("0.01"), rounding = "ROUND_UP")
>>> print(a_t)
0.13

decimal.ROUND_CEILING			| 		舍入方向为 Infinity。
decimal.ROUND_DOWN				| 		舍入方向为零。
decimal.ROUND_FLOOR				| 		舍入方向为 -Infinity。
decimal.ROUND_HALF_DOWN			| 		舍入到最接近的数，同样接近则舍入方向为零。
decimal.ROUND_HALF_EVEN			| 		舍入到最接近的数，同样接近则舍入到最接近的偶数。
decimal.ROUND_HALF_UP			| 		舍入到最接近的数，同样接近则舍入到零的反方向。
decimal.ROUND_UP				| 		舍入到零的反方向。
decimal.ROUND_05UP				| 		如果最后一位朝零的方向舍入后为 0 或 5 则舍入到零的反方向；否则舍入方向为零。
```

## 2. 数学/素数相关

### 2.1 判断/筛是否是素数

```python
# 简单版 log(n)
import math
def is_prime(x):
    if x==2:
        return True
    for i in range(2,int(math.sqrt(x))+1):
        if x%i==0:
           return False
    else:
        return True
    
# 素数筛选，筛选出小于等于一个数字的素数
# 典型题目是可以是筛选一个区间[L, U]之内的素数
def sieve_of_eratosthenes(n):
    pick = [1] * (n+1)
    pick[0] = pick[1] = 0
    for i in range(n+1):
        if pick[i] != 0:
        	for j in range(i*i, n+1, i):
          	  pick[j] = 0
    return [x for x in range(n+1) if pick[x]]

>>> sieve_of_eratosthenes(100)
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97]
```

### 2.2 质因数分解

```python
def breakdown(N):
    result = []
    for i in range(2, int(sqrt(N)) + 1):
        if N % i == 0: # 如果 i 能够整除 N，说明 i 为 N 的一个质因子。
            while N % i == 0:
                N = N // i
                result.append(i)
    if N != 1: # 说明再经过操作之后 N 留下了一个素数
        result.append(N)
    return result

if __name__ == '__main__':
    print(breakdown(120))  # 输出[2, 2, 2, 3, 5]

```

### 2.3 最大公因数，最小公倍数

```python
# 最大公因数，直接使用math.gcd()
import math
gcd = math.gcd(5, 25)
print(gcd)  # 5

# 最小公倍数就是乘在一起，然后除最大公因数
lcm = (x * y) / gcd(x, y)
```

### 2.4 快速幂取模

```python
def fast_power(base, power, mod):
    res = 1
    while power > 0:
        if power % 2 == 1:
            res = res * base % mod
        power = power // 2
        base = base * base % mod
    return res % mod
```

### 2.5 韩信点兵问题（中国剩余定理）

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221026214611115.png" alt="image-20221026214611115" style="zoom:50%;" />

其中$m1, m2, mk$两两互素

```python
# 求两个数的最大公约数gcd函数
def gcd(a,b):
    if b == 0:
        return a
    else :
        return gcd(b,a % b)

 判断一个列表任意两个数是否两两互质
 def compare(list):
     for i in range(0,len(list)):
         flag = 1
         for j in range(i+1,len(list)):
             if gcd(list[i],list[j]) != 1:
                 print('不能直接使用中国剩余定理！')
                exit()
#如果满足条件，就会继续执行，否则退出程序


# 求出输入的m1,m2,..,mk 的乘积m
def product_m(list):
    m = 1
    for i in list:
        m *= i
    return m


# 求M1,M2,..,MK 的值 Mj = m / mj 并返回一个名为shang的列表
def get_divsion(list,m):
    div = []
    for i in list:
        div.append(m // i)
    return div


def get_inverse(a, m):  # 求一个数a 的逆  再模m 的值  这个函数返回的是一个值不是列表
    if gcd(a, m) != 1:
        return None
    u1, u2, u3 = 1, 0, a
    v1, v2, v3 = 0, 1, m
    while v3 != 0:
        q = u3 // v3
        v1, v2, v3, u1, u2, u3 = (u1 - q * v1), (u2 - q * v2), (u3 - q * v3), v1, v2, v3
    return u1 % m


# 求Xj   算法为：Xj = (M * M_INVERSE * a) % mj
def get_x(M:int ,M_inverse:int ,a :int,m:int):
    product_x = (M * M_inverse * a) % m
    return product_x



# 算出最终答案X = X1+X2+...Xk
def get_solution(list_m, list_a):
                                                        # compare(list_m)
    m = product_m(list_m)

    list_M = get_divsion(list_m, m)

    list_M_inverse = []
    list_X = []
    total = 0

    for i in range(0,len(list_M)):
        list_M_inverse.append(get_inverse(list_M[i], list_m[i]))

    for i in range(len(list_M)):
        list_X.append(get_x(list_M[i],list_M_inverse[i],list_a[i],m))

    for x in list_X:
        total += x

    return total % m

# 测试数据
#list_a = [2,3,2]
#list_m = [3,5,7]
#print(get_solution(list_m, list_a))

# 调用get_solution()函数即可使用中国剩余定理
# get_solution()函数要传入得是两个列表list_a,list_m
# 读取与输入list_a,list_m;并将其变为整型的数据在
```

针对他提供的这组测试数据，输出的是23，所以结果应该是23 + 105n，其中105是m的最小公倍数！

### 2.6 欧拉函数&欧拉定理

- 欧拉函数

在数论，对正整数n，欧拉函数是小于n的正整数中与n互质的数的数目

如果$p$是素数，那么$\varphi(p)=p-1$

如果$p$不是素数，那么要先分解质因数满足<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221026210535237.png" alt="image-20221026210535237" style="zoom:20%;" />，

然后<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221026211155567.png" alt="image-20221026211155567" style="zoom:20%;" />

- 欧拉定理

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221026212441327.png" alt="image-20221026212441327" style="zoom:50%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221026212501292.png" alt="image-20221026212501292" style="zoom:50%;" />

### 2.7 分块打表

这个leetcode题目是一个分块打表的思路，比如数据范围是$10^9$这个规模的，那么就可以分成$2000(2*10^3)$个，$500000(5*10^5)$规模的数据块，这样处理完后，可以把一些结果先给跑出来，然后分块的来做，也是竞赛常用的手段之一

- Leetcode2376 - 统计特殊整数

如果一个正整数中每一位是**互不相同**的，称他是**特殊整数**。

给定一个正整数`n`，返回`[1, n]`之间特殊整数的数目。

```shell
输入：n = 20
输出：19
解释：1 到 20 之间所有整数除了 11 以外都是特殊整数。所以总共有 19 个特殊整数。

输入：n = 5
输出：5
解释：1 到 5 所有整数都是特殊整数。

输入：n = 135
输出：110
解释：从 1 到 135 总共有 110 个整数是特殊整数。
不特殊的部分数字为：22 ，114 和 131 。
```

```python
class Solution(object):
    def countSpecialNumbers(self, n):
        """
        :type n: int
        :rtype: int
        """
        table = [0, 92970,75600,26880,33600,26880,33600,26880,33600,26880,33600,33600,26880,33600,26880,33600,26880,33600,26880,33600,26880,7560,12600,0,0,7560,12600,7560,12600,7560,12600,10080,10080,10080,10080,10080,10080,10080, ...]

        res = 0
        # 首先找到n是属于哪个block的
        # 甚至可以前缀和，或者遍历一下
        block_size = 5 * (10 ** 5)
        bs_cnt = n // block_size
        base_res = 0
        for i in range(bs_cnt+1):
            base_res += table[i]
            # print(i)


        # 之后在那个区间上单独求解
        for i in range(bs_cnt*block_size+1, n+1):
            # 判重逻辑写法1，可能在大数据的条件下，这个会要求效率再高一些吧
            s = str(i)
            if len(list(set(list(s)))) == len(s):
                res += 1

        res += base_res
        return res
```

### 2.8 基础数学定理&常用数学公式

- 海伦公式

已知三边长分别为`a` `b` `c`，求这个三角形的面积（实际操作的时候还要注意三边不满足三角形的情况）

```python
import math

# s 定义为周长的一半
s = 0.5 * (a+b+c)

## ss为所求面积
ss = math.sqrt(s*(s-a)*(s-b)*(s-c))
```

- 哥德巴赫猜想（CF735D-Taxes）

题意：已知n元需缴税为n的最大因子x元。现通过将n元分成k份的方式来减少缴税。问通过这种处理方式需缴纳的税费。

分析：

1、若n为素数，不需分解，可得1

2、若n为偶数，由哥德巴赫猜想：一个大于2的偶数可以分解成两个素数的和，可得2

3、若n为奇数，n-2为素数，则为2，否则为3。(因为若为奇数要拆，不能拆成1+偶数，至少拆为2+奇数，若此奇数为素数，则输出为2，否则继续拆成3+偶数，那么结果为3(充分利用哥德巴赫猜想的结论))

## 3. 区间查询相关

### 3.1 线段树（Python版，很有可能超时，不太好使）

```python
import sys

class SegmentTreeNode:
    def __init__(self, start, end, max_val):
        """
        segment tree node structure
        @param: int start
        @param: int end
        @param: int/double max_val

        """
        self.start = start
        self.end = end 
        self.max = max_val
        self.left = None
        self.right = None


class RangeMaxQuery:
    def __init__(self, A):
        """
        initialize the data stucture
        @param: list A
        """
        
        self.root = self.build_helper(0, len(A) - 1, A)

    def build_helper(self, left, right, A):
        """
        build the data structure
        @param: int left: left index
        @param: int right: right index
        @param: list A

        """
        if left > right:
            return None 

        root = SegmentTreeNode(left, right, A[left])

        if left == right:
            return root 

        mid = (left + right) // 2
        root.left = self.build_helper(left, mid, A)
        root.right = self.build_helper(mid+1, right, A)
        root.max = max(root.left.max, root.right.max)

        return root 

    def update(self, index, value):
        """
        update value at index
        @param: int index
        @param: double/int value

        """
        self._modify(self.root, index, value)
        

    def _modify(self, root, index, value):
        """
        helper funcf for updating new value
        @param: SegmentTreeNode root
        @param: int index
        @param: int/double value

        """
        if root.start == root.end and root.start == index:
            root.max = value
            return

        mid = (root.start + root.end) // 2
        if index <= mid:
            self._modify(root.left, index, value)
            root.max = max(root.left.max, root.right.max)
        else:
            self._modify(root.right, index, value)
            root.max = max(root.left.max, root.right.max)
        return

    def range_max_query(self, start, end):
        """
        maximum value query in range(start, end)
        @param: int start
        @param: int end
        """
        return self._max_query(self.root, start, end)
        
    def _max_query(self, root, start, end):
        """
        help func for range max query
        @param: SegmentTreeNode root
        @param: int start
        @param: int end
        """
        if start <= root.start and root.end <= end:
            return root.max
        mid = (root.start + root.end) // 2
        ans = -sys.maxsize
        if mid >= start:
            ans = max(ans, self._max_query(root.left, start, end))
        if mid + 1 <= end:
            ans = max(ans, self._max_query(root.right, start, end))
        return ans

# 这里的用法是闭区间的用法
>>> A = [2, 3, 4, 1, 7]         
>>> arr = RangeMaxQuery(A)      # initialize data structure
>>> arr.range_max_query(1, 4)     # maximum query in range(1, 4)
7
>>> arr.update(1, 10)           # set A[1] = 10 and update the data structure
>>> arr.range_max_query(1, 4)
```

### 3.2 ST表+RMQ

和线段树比，比较大的问题是他不好修改，就是初始化一次了之后，可能就这样了

对于所有maxquery问题，可以把正数变成负数，就变成了minquery问题

```python
def ST(A):
    log = 0
    l = len(A)
    
    l >>= 1
    while l != 0:
        l >>= 1
        log += 1
    
    dp = [[0 for _ in range(log+5)] for _ in range(len(A))]
    for i in range(0, len(A)):
        dp[i][0] = A[i]
    for j in range(1, log+1):
        i = 0
        while i + (1<<j) - 1 < len(A):
            dp[i][j] = max(dp[i][j-1], dp[i+(1<<(j-1))][j-1])
            i += 1
    
    for i in range(0, len(A)):
        for j in range(log+1, log+4):
            dp[i][j] = dp[i][j-1]

    return dp

def RMQ(dp, l, r):
    k = 0
    while (1 << (k+1)) <= r-l+1:
        k += 1
    try:
        return max(dp[l][k], dp[r-(1<<k)+1][k])
    except:
        return 0

# 这个也是闭区间，区间都用闭区间还是比较好用
>>> A = [2, 3, 4, 1, 7]
>>> st_table = ST(A)
>>> print(RMQ(st_table, 0, 1))
3
```

### 3.3 前缀和

很多区间相关的题目都和前缀和相关，建议定义前缀和的长度和原数组的大小长度是一样的

主要也是统计一个区间中的一个数目

## 4. 数据结构

### 4.1 排序

```python
nums = [[2, "c"], [1, "c"], [1, "b"], [1, "a"]]
nums.sort(key=lambda x: (x[0], x[1]))
print(nums)  # [[1, 'a'], [1, 'b'], [1, 'c'], [2, 'c']]
```

### 4.2 Trie树

```python
class TrieNode(object):
    def __init__(self):
        self.is_end = False  # 标志是否是结束位置
        self.mapping = {}  # 字符->node的mapping

class Trie(object):
    def __init__(self):
        """
        初始化前缀树对象，root为一个TrieNode
        """
        self.root = TrieNode()
    
    def insert(self, word):
        """
        向前缀树种插入字符串word
        """
        node = self.root
        for i in range(len(word)):
            if node.mapping.get(word[i]) is None:
                node.mapping[word[i]] = TrieNode()
            node = node.mapping[word[i]]
        node.is_end = True
    

    def search(self, word):
        """
        如果字符串word在前缀树中，返回True
        否则，返回False
        """
        node = self.root
        for i in range(len(word)):
            if node.mapping.get(word[i]) is None:
                return False
            node = node.mapping[word[i]]
        
        if node.is_end == True:
            return True
        else:
            return False
        
    
    def startsWith(self, word):
        """
        如果之前插入的字符串word的前缀之一为prefix，返回True
        否则，返回Fasle
        """
        node = self.root
        for i in range(len(word)):
            if node.mapping.get(word[i]) is None:
                return False
            node = node.mapping[word[i]]
        
        return True


# Your Trie object will be instantiated and called as such:
# obj = Trie()
# obj.insert(word)
# param_2 = obj.search(word)
# param_3 = obj.startsWith(prefix)
```

### 4.3 二分

- 二分左右查询（类似于C++中的lower_bound那种函数）

```python
import bisect

nums = [1, 3, 3, 4, 6, 8, 12, 15]

# bisect.bisect_left(nums, x)
# 在nums中查找x，如果x存在则返回x第一次出现的位置
# 否则，返回应该插入的位置
bisect.bisect_left(nums, 3)  # 1
bisect.bisect_left(nums, 4)  # 3

bisect.bisect_left(nums, 2)  # 1


# bisect.bisect_right(nums, x)
# 在nums中查找x，如果x存在，则返回x最后一次出现的位置，后面的一个位置
# 否则，返回应该插入的位置
bisect.bisect_right(nums, 3)  # 3
bisect.bisect_right(nums, 1)  # 1

bisect.bisect_right(nums, 2)  # 1
```

- 二分答案（这个是自己的，不一定好使，主要还是+1和-1没整明白）

基本思路就是说，二分查找比如[1, 1e9]这个区间，假如mid满足的话，让res=mid，然后left=mid+1，给卡过去

再详细说一下的话，就是说我如果mid满足，那么比他大的还是有可能满足的，比他小的就可以排除掉了，也就是找到最大的满足条件的

```python
l = 0
r = len(nums)
res = 0
while l <= r:
    mid = (l + r) // 2
    flag = True if check() else False    
    if flag:
        res = mid
        l = mid+1
    else:
        r = mid-1
return res
```

### 4.4 heapq

#### 4.5.1 基础语法

优先队列，基础语法

- 创建堆

heapq有两种方式创建堆， 一种是使用一个空列表，然后使用heapq.heappush()函数把值加入堆中，另外一种就是使用heap.heapify(list)转换列表成为堆结构

```python
import heapq

# 第一种
"""
函数定义：
heapq.heappush(heap, item)
    - Push the value item onto the heap, maintaining the heap invariant.
heapq.heappop(heap)
    - Pop and return the smallest item from the heap, maintaining the heap invariant.
    If the heap is empty, IndexError is raised. To access the smallest item without popping it, use heap[0].
"""
nums = [2, 3, 5, 1, 54, 23, 132]
heap = []
for num in nums:
    heapq.heappush(heap, num)  # 加入堆

print(heap[0])  # 如果只是想获取最小值而不是弹出，使用heap[0]
print([heapq.heappop(heap) for _ in range(len(nums))])  # 堆排序结果
# out: [1, 2, 3, 5, 23, 54, 132]


# 第二种
nums = [2, 3, 5, 1, 54, 23, 132]
heapq.heapify(nums)
print([heapq.heappop(nums) for _ in range(len(nums))])  # 堆排序结果
# out: [1, 2, 3, 5, 23, 54, 132]
```

heapq 模块还有一个`heapq.merge(*iterables)` 方法，用于合并多个排序后的序列成一个排序后的序列， 返回排序后的值的迭代器。
 类似于`sorted(itertools.chain(*iterables))`，但返回的是可迭代的。

```python
"""
函数定义：
heapq.merge(*iterables)
    - Merge multiple sorted inputs into a single sorted output (for example, merge timestamped entries from multiple log files). Returns an iterator over the sorted values.
    - Similar to sorted(itertools.chain(*iterables)) but returns an iterable, does not pull the data into memory all at once, and assumes that each of the input streams is already sorted (smallest to largest).
"""
import heapq

num1 = [32, 3, 5, 34, 54, 23, 132]
num2 = [23, 2, 12, 656, 324, 23, 54]
num1 = sorted(num1)
num2 = sorted(num2)

res = heapq.merge(num1, num2)
print(list(res))
```

- 访问堆内容

堆建好后，可以通过`heapq.heappop() `函数弹出堆中最小值。

```python
import heapq
nums = [2, 43, 45, 23, 12]
heapq.heapify(nums)

print(heapq.heappop(nums))
# out: 2

# 如果需要所有堆排序后的元素
result = [heapq.heappop(nums) for _ in range(len(nums))]
print(result)
# out: [12, 23, 43, 45]
```

如果需要删除堆中最小元素并加入一个元素，可以使用`heapq.heaprepalce()` 函数

```python
import heapq

nums = [1, 2, 4, 5, 3]
heapq.heapify(nums)

heapq.heapreplace(nums, 23)

print([heapq.heappop(nums) for _ in range(len(nums))])
# out: [2, 3, 4, 5, 23]
```

- 获取堆最大或最小值

如果需要获取堆中最大或最小的范围值，则可以使用`heapq.nlargest()` 或`heapq.nsmallest()` 函数

```python
"""
函数定义：
heapq.nlargest(n, iterable[, key])¶
    - Return a list with the n largest elements from the dataset defined by iterable. 
    - key if provided, specifies a function of one argument that is used to extract a comparison key from each element in the iterable: key=str.lower
    - Equivalent to: sorted(iterable, key=key, reverse=True)[:n]
"""
import heapq

nums = [1, 3, 4, 5, 2]
print(heapq.nlargest(3, nums))
print(heapq.nsmallest(3, nums))

"""
输出：
[5, 4, 3]
[1, 2, 3]
"""
```

这两个函数还接受一个key参数，用于dict或其他数据结构类型使用

```python
import heapq
from pprint import pprint
portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
cheap = heapq.nsmallest(3, portfolio, key=lambda s: s['price'])
expensive = heapq.nlargest(3, portfolio, key=lambda s: s['price'])
pprint(cheap)
pprint(expensive)

"""
输出：
[{'name': 'YHOO', 'price': 16.35, 'shares': 45},
 {'name': 'FB', 'price': 21.09, 'shares': 200},
 {'name': 'HPQ', 'price': 31.75, 'shares': 35}]
[{'name': 'AAPL', 'price': 543.22, 'shares': 50},
 {'name': 'ACME', 'price': 115.65, 'shares': 75},
 {'name': 'IBM', 'price': 91.1, 'shares': 100}]
"""
```

#### 4.5.2 题目应用-寻找数据流中的中位数

如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。

例如，

[2,3,4] 的中位数是 3

[2,3] 的中位数是 (2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：

void addNum(int num) - 从数据流中添加一个整数到数据结构中。
double findMedian() - 返回目前所有元素的中位数。

示例1：

```shell
输入：
["MedianFinder","addNum","addNum","findMedian","addNum","findMedian"]
[[],[1],[2],[],[3],[]]
输出：[null,null,null,1.50000,null,2.00000]
```

示例2：

```python
输入：
["MedianFinder","addNum","findMedian","addNum","findMedian"]
[[],[2],[],[3],[]]
输出：[null,null,2.00000,null,2.50000]
```

```python
class MedianFinder:

    def __init__(self):
        self.queMin = list()
        self.queMax = list()

    def addNum(self, num: int) -> None:
        queMin_ = self.queMin
        queMax_ = self.queMax

        if not queMin_ or num <= -queMin_[0]:
            heapq.heappush(queMin_, -num)
            if len(queMax_) + 1 < len(queMin_):
                heapq.heappush(queMax_, -heapq.heappop(queMin_))
        else:
            heapq.heappush(queMax_, num)
            if len(queMax_) > len(queMin_):
                heapq.heappush(queMin_, -heapq.heappop(queMax_))
        
    def findMedian(self) -> float:
        queMin_ = self.queMin
        queMax_ = self.queMax

        if len(queMin_) > len(queMax_):
            return -queMin_[0]
        return (-queMin_[0] + queMax_[0]) / 2
```

### 4.6 SortedList

```python
# 基础说明如下
1. 添加值
SortedList.add(value) # 添加新元素，并排序。时间复杂度O(log(n)).
SortedList.update(iterable) # 对添加的可迭代的所有元素排序。时间复杂度O(k*log(n)).

2. 移除值
SortedList.clear() # 移除所有元素。时间复杂度O(n).
SortedList.discard(value) # 移除一个值元素，如果元素不存在，不报错。时间复杂度O(log(n)).
SortedList.remove(value) # 移除一个值元素，如果元素不存在，报错ValueError。时间复杂度O(log(n)).
SortedList.pop(index=-1) # 移除一个指定下标元素，如果有序序列为空或者下标超限，报错IndexError. 时间复杂度O(log(n))

3. 查找值
SortedList.bisect_left(value) # 查找元素可以插入的位置下标，如果这个value已经存在，则插入已经存在的所有values之前（左侧）.时间复杂度O(log(n)).
SortedList.bisect_right(value) # 查找元素可以插入的位置下标，如果这个value已经存在，则插入已经存在的所有values之后（右侧）。时间复杂度O(log(n))
SortedList.count(value) # 查找元素出现的次数。时间复杂度O(log(n)).
SortedList.index(value, start=None, Stop=None) # 查找索引范围[start,stop）内第一次出现value的索引，如果value不存在，报错ValueError. 时间复杂度O(log(n)).
```

比较重要的特点是维护一个list的最大值和最小值，也就是SortedList中[0]和[-1]这样的索引

```python
from sortedcontainers import SortedList

sorted_list = SortedList([])

price = 100
timestamp = 0

# .add
sorted_list.add([price, timestamp])

# .discard
sorted_list.discard([prev_price, prev_timestamp])

# 对于字符串的字典序，该怎么反过来呢
尝试了很多方法，都没有找到太合适的答案，就是说我希望数字是升序的，字典序是反序的，甚至不知道在SortedList中是否是可以实现的
这里是一个TODO内容，希望能在未来找到答案，目前只建议用sortedList把数字都反过来，那么字符串的升序降序就相对的反过来了
```

字符串的相关使用方法看这里

```python
from sortedcontainers import SortedList

sorted_list = SortedList([])

sorted_list.add([2, "a"])
sorted_list.add([1, "c"])
sorted_list.add([1, "b"])

print(sorted_list)  # SortedList([[1, 'b'], [1, 'c'], [2, 'a']])

sorted_list.discard([1, "c"])
print(sorted_list)  # SortedList([[1, 'b'], [2, 'a']])
```

### 4.7 并查集

```python
# 并查集的题目大部分比较明显
def get_root(a):
    if par[a] != a:
        par[a] = get_root(par[a])
    return par[a]

def merge(a, b):
    p1, p2 = get_root(a), get_root(b)
    if p1 == p2:
        return
    else:
        # p2的parent是p1
        # 如果涉及到每个group的值，还要把这里也累加上
        par[p2] = p1  

# 初始化自己的par都是自己
par = {}
for i in range(len(s)):
    par[i] = i

# 两两merge
# pairs: [[0, 1], [1, 2], [3, 4], ...]
for i in range(len(pairs)):
    merge(pairs[i][0], pairs[i][1])
```

### 4.8 重定义类中的比较运算符

在python中似乎有

```python
class Tmp(object):
    """
        1、对应如下
        __lt__()、__le__()、__eq__()、__ne__()、__gt__()、__ge__()
        <、<=、=、!=、>、>=

        2、__ne__()会默认调用__eq__()，并对结果求反，因此定义了__eq__()就相当于定义了__ne__()

        3、由于__lt__()与__ge__()互补，__le__()与__gt__()互补，故只需要定义__gt__()和__ge__()
    """
    def __init__(self, num, s):
        self.num = num
        self.s =  s

    def __eq__(self, other):
        return (self.num == other.num) and (self.s == other.s)


    def __gt__(self, other):
        """
        >: 原来是大于
        """
        if self.num == other.num:
            return self.s <= other.s
        else:
            return self.num > other.num

    def __ge__(self, other):
        """
        >=: 原来是大于等于
        """
        if self.num == other.num:
            return self.s < other.s
        else:
            return self.num > other.num
```

## 5. 图论常用算法

### 5.1 dijkstra

- Leetcode 1514. 概率最大的路径（只能保存一条路径）

给你一个由 n 个节点（下标从 0 开始）组成的无向加权图，该图由一个描述边的列表组成，其中 edges[i] = [a, b] 表示连接节点 a 和 b 的一条无向边，且该边遍历成功的概率为 succProb[i] 。

指定两个节点分别作为起点 start 和终点 end ，请你找出从起点到终点成功概率最大的路径，并返回其成功概率。

如果不存在从 start 到 end 的路径，请 返回 0 。只要答案与标准答案的误差不超过 1e-5 ，就会被视作正确答案。

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029233005521.png" alt="image-20221029233005521" style="zoom:80%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029233023988.png" alt="image-20221029233023988" style="zoom:80%;" />

```python
class Solution(object):
    def maxProbability(self, n, edges, succProb, start, end):
        """
        :type n: int
        :type edges: List[List[int]]
        :type succProb: List[float]
        :type start: int
        :type end: int
        :rtype: float
        """
        from collections import defaultdict
        import heapq

        # 邻接表（这里用dict可以默认为邻接表了）
        d = defaultdict(dict)
        for i, prob in enumerate(succProb):
            x, y = edges[i]
            d[x][y] = prob
            d[y][x] = prob

        # dijkstra
        stack = [[-1, start]]  # 这里-1就是大根堆的意思，这个题要求走最大的路径，负数越大越小所以可以dijkstra
        heapq.heapify(stack)
        visit = set()  # vis

        path = []
        while stack:
            # 取出来节点
            prob, i = heapq.heappop(stack)
            
            # 如果已经是结尾，则返回；如果是其他题或许可以魔改这里
            if i == end:
                path.append(i)
                print("path: ", path)
                return -prob

            # 如果被访问过，就不走
            if i in visit:
                continue
            
            # 加入vis
            visit.add(i)
            path.append(i)
            
            # 每次都从最大的开始走
            for j in d[i]:
                heapq.heappush(stack, [prob*d[i][j], j])

        print("path: ", path)
        return 0
```

- TODO，未来有机会把下面这个翻译成python了

https://blog.csdn.net/weixin_45745854/article/details/126264026?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-1-126264026-blog-125927568.pc_relevant_landingrelevant&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-1-126264026-blog-125927568.pc_relevant_landingrelevant&utm_relevant_index=1

### 5.2 最小生成树Kruscal&Prim

最小生成树可能需要用并查集来实现

- Kruscal

```python
def find(x):                          #寻找节点的根节点
    if(x!=p[x]):
        p[x]=find(p[x])
    return p[x]
n,m=map(int,input().split())          #n为节点个数，m为边的个数
edges=[]
a=[]
for i in range(m):                    #有m条边循环m次
    a,b,c=map(int,input().split())    #输入两个节点，以及对应的权值
    edges.append([a,b,c])             #将每条边储存在列表中
edges=sorted(edges,key=lambda x:x[2]) #以列表元素的第三个元素，也就是边的权值为依据进行排序
res=0                                 #权值的和起始值为0
cnt=0
p=[i for i in range(n+1)]             #初始化,每个节点是孤立的,即父节点是自己
for i in range(m):
    a=edges[i][0]
    b=edges[i][1]
    w=edges[i][2]
    a=find(a)                         #a的根节点
    b=find(b)                         #b的根节点
    if(a!=b):                         #如果a的根节点不等于b的根节点,满足条件
        res+=w                        #权值相加
        cnt+=1                        #计数器(目前已连接边的数量)加一
        p[a]=b
if(cnt<n-1):                          #n个点必须有n-1条边
    print("impossible")
else:
    print(res)
```

### 5.3 DFS - Lake Counting类

- Leetcode200. 岛屿数量

给你一个由`"1"`（陆地）和`"0"`水组成的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成

此外，你可以假设网格的四条边被水包围。

示例1：

```shell
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

示例2：

```shell
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

数据范围

```shell
m == grid.length
n == grid[i].length
1 <= m, n <= 300
grid[i][j] 的值为 '0' 或 '1'
```

解答

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        
        dx = [0, 1, -1, 0]
        dy = [-1, 0, 0, 1]
        m = len(grid)
        n = len(grid[0])

        def dfs(x: int, y: int) -> None:
            grid[x][y] = "0"

            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]
				
                # 注意这里
                if 0 <= nx < m and 0 <= ny < n and grid[nx][ny] == "1":
                    dfs(nx, ny)

            return
        
        res = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == "1":
                    dfs(i, j)
                    res += 1

        return res
```

### 5.4 DFS - 硬暴力类（Codeforces1646C）

```python
"""
题目大意：
C. Factorials and Powers of Two：阶乘和2的幂

一个数字被称作是powerful的，如果他是【某个数的阶乘】或【2的幂】。换句话来说，如果数字m是powerful的，那么就是存在一个d，使得 m=2^d 或者 m=d!（0!=1）。

举例来说，1，4，6是powerful的，因为1，6分别是1，3的阶乘；4是2的2次方；；7，10，18不是powerful的；

给定一个正整数n，找到最小的k，使得n可以被表示为k个各不相同powerful number的和。

输入：
输入包含多组数据，第一行代表数据的组数t，之后每一行有一个数字n（1<=n<=10^12）；
4
7
11
240
17179869184

输出：
如果表示不了，输出-1；否则输出最小的k值；
2
3
4
1
"""

# 阶乘预处理，存储1-14的阶乘，用dp的思路一次遍历
f = [0 for i in range(15)]
f[0] = 1
for i in range(1, 15):
    f[i] = i * f[i-1]

# 统计二进制中1的个数
def count_one_num(num):
    cnt = 0
    while num > 0:
        if num & 1 == 1:
            cnt += 1
        num >>= 1
    return cnt

class Solution(object):

    def cf1646C(self, n):
        global res
        res = 1e9
        def dfs(now_k, now_sum, pos):  # 当前的选择数字数目，当前的加和值，当前的f[pos]角标
            global res
            if now_sum > n:
                return
            if pos == 15:
                res = min(res, now_k+count_one_num(n-now_sum))
                return

            # 选择
            dfs(now_k+1, now_sum+f[pos], pos+1)

            # 不选
            dfs(now_k, now_sum, pos+1)

        dfs(0, 0, 0)
        return res


if __name__ == '__main__':
    t = int(input())
    S = Solution()

    for _ in range(t):
        n = int(input())
        res = S.cf1646C(n)
        print(res)
```

### 5.5 BFS - 迷宫

#### 5.5.1 基础版迷宫

当你站在一个迷宫里的时候，往往会被错综复杂的道路弄得失去方向感，如果你能得到迷宫地图，事情就会变得非常简单。
假设你已经得到了一个n*m的迷宫的图纸，请你找出从起点到出口的最短路。

第一行是两个整数n和m(1<=n,m<=100)，表示迷宫的行数和列数。
接下来n行，每行一个长为m的字符串，表示整个迷宫的布局。字符'.'表示空地，'#'表示墙，'S'表示起点,'T'表示出口。

输出从起点到出口最少需要走的步数。

```shell
# 输入
3 3
S#T
.#.
...

# 输出
6
```

```C++
#include <iostream>
#include <queue>
using namespace std;
const int MAXN = 105;
const int INF = 10005;
char maze[MAXN][MAXN];
int d[MAXN][MAXN];
int dx[4] = {1,0,-1,0};
int dy[4] = {0,1,0,-1};
int n,m;
struct node
{
	int x,y;
	node(int a,int b)
	{
		x = a;
		y = b;
	}
	node(){}
};
node s,t;
int bfs()
{
	queue <node> que;
	que.push(s);
	d[s.x][s.y] = 0;
	while(que.size())
	{
		node temp = que.front();
		que.pop();
		if(temp.x==t.x&&temp.y==t.y)	break;
		for(int i=0;i<4;i++)
		{
			int nx = temp.x + dx[i];
			int ny = temp.y + dy[i];
			if(nx>=0&&nx<n&&ny>=0&&ny<m&&maze[nx][ny]!='#'&&d[nx][ny]==INF) 
			{
				d[nx][ny] = d[temp.x][temp.y] + 1;
				que.push(node(nx,ny));
			}
		}
	}
	return d[t.x][t.y];
}
int main(void)
{
	scanf("%d%d",&n,&m);
	for(int i=0;i<n;i++)
	{
		scanf("%s",maze[i]);
	}
	for(int i=0;i<n;i++)
	{
		for(int j=0;j<m;j++)
		{
			d[i][j] = INF;
			if(maze[i][j]=='S')	s = node(i,j);
			else if(maze[i][j]=='T')t = node(i,j);
		}
	}
	printf("%d",bfs());
}
```

#### 5.5.2 迷宫-鸣人和佐助

已知一张地图（以二维矩阵的形式表示）以及佐助和鸣人的位置。地图上的每个位置都可以走到，只不过有些位置上有大蛇丸的手下，需要先打败大蛇丸的手下才能到这些位置。鸣人有一定数量的查克拉，每一个单位的查克拉可以打败一个大蛇丸的手下。假设鸣人可以往上下左右四个方向移动，每移动一个距离需要花费1个单位时间，打败大蛇丸的手下不需要时间。如果鸣人查克拉消耗完了，则只可以走到没有大蛇丸手下的位置，不可以再移动到有大蛇丸手下的位置。佐助在此期间不移动，大蛇丸的手下也不移动。请问，鸣人要追上佐助最少需要花费多少时间？

输入的第一行包含三个整数：M，N，T。代表M行N列的地图和鸣人初始的查克拉数量T。0 < M,N < 200，0 ≤ T < 10
后面是M行N列的地图，其中@代表鸣人，+代表佐助。*代表通路，#代表大蛇丸的手下。

输出包含一个整数R，代表鸣人追上佐助最少需要花费的时间。如果鸣人无法追上佐助，则输出-1。

```shell
# 样例输入1
4 4 1
#@##
**##
###+
****

# 样例输出1
6

# 样例输入2
4 4 2
#@##
**##
###+
****

# 样例输出2
4
```

```c++
#include <iostream>
#include <queue>
#include <cstring>
using namespace std;

int m,n,t;
char mp[205][205];
int vis[205][205];
int dx[4] = {1,0,-1,0};
int dy[4] = {0,1,0,-1};
struct node
{
	int x;
	int y;
	int chakela;
	int step;
	node(int a,int b,int c,int d)
	{
		x = a;
		y = b;
		chakela = c;
		step = d;
	}
	node(){}
	bool operator < (const node &a) const //按照查克拉最小值优先 结构体优先队列 
	{
		if(a.step==step)
		{
			return a.chakela<chakela;//路径相同情况下 查克拉消耗小的优先 
		}
		else
			return a.step<step; 
	}
}s,tt,temp;
priority_queue <node> pq;
void bfs()
{
	pq.push(s);
	vis[s.x][s.y] = 1;
	while(pq.size())
	{
		temp = pq.top();
		pq.pop();
		if(temp.x==tt.x&&temp.y==tt.y)
		{
			printf("%d\n",temp.step);
			exit(0) ;
		} 
		for(int i=0;i<4;i++)
		{
			int nx = temp.x+dx[i];
			int ny = temp.y+dy[i];
			if(nx>=0&&nx<m&&ny>=0&&ny<n&&vis[nx][ny]==0)//消耗的查克拉需要能走到 
			{
				if(mp[nx][ny]=='#'&&temp.chakela+1<=t)
				{
					pq.push(node(nx,ny,temp.chakela+1,temp.step+1));
					vis[nx][ny] = 1;
				}
				if(mp[nx][ny]=='*'&&temp.chakela<=t)
				{
					pq.push(node(nx,ny,temp.chakela,temp.step+1));
					vis[nx][ny] = 1;
				}
				if(mp[nx][ny]=='+'&&temp.chakela<=t)
				{
					pq.push(node(nx,ny,temp.chakela,temp.step+1));
					vis[nx][ny] = 1;	
				} 	
			}  
		}
	}
	return ;
} 
int main(void)
{
	memset(vis,0,sizeof(vis));
	scanf("%d%d%d",&m,&n,&t);//m行n列 且一开始拥有的查克拉数量为t
	for(int i=0;i<m;i++)
	{
		scanf("%s",mp[i]);
	}
	for(int i=0;i<m;i++)
	{
		for(int j=0;j<n;j++)
		{
			if(mp[i][j]=='@')
			{ 
				s = node(i,j,0,0);//找到一个起始点 
			}
			if(mp[i][j]=='+')
			{
				tt = node(i,j,t,0);//这里似乎只有ij是有用的 
			} 
		}
	}
	bfs();
	printf("-1\n");
} 
```

#### 5.5.3 迷宫-鸣人和佐助python题目Leetcode1293. 网格中的最短路径

给你一个 m * n 的网格，其中每个单元格不是 0（空）就是 1（障碍物）。每一步，您都可以在空白单元格中上、下、左、右移动。

如果您 最多 可以消除 k 个障碍物，请找出从左上角 (0, 0) 到右下角 (m-1, n-1) 的最短路径，并返回通过该路径所需的步数。如果找不到这样的路径，则返回 -1 。

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029234434965.png" alt="image-20221029234434965" style="zoom:80%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029234446218.png" alt="image-20221029234446218" style="zoom:80%;" />



- `grid.length == m`

- `grid[0].length == n`

- `1 <= m, n <= 40`

- `1 <= k <= m*n`

- `grid[i][j] 是 0 或 1`

- `grid[0][0] == grid[m-1][n-1] == 0`

方法一：广度优先搜索
对于二维网格中的最短路问题，我们一般可以使用广度优先搜索 + 队列的方法解决。

本题中，玩家在移动时可以消除障碍物，这会导致网格的结构发生变化，看起来我们需要在广度优先搜索时额外存储网格的变化。但实际上，由于玩家在最短路中显然不会经过同一位置超过一次，因此最多消除 k 个障碍物等价于最多经过 k 个障碍物。

这样我们就可以使用三元组 (x, y, rest) 表示一个搜索状态，其中 (x, y) 表示玩家的位置，rest 表示玩家还可以经过 rest 个障碍物，它的值必须为非负整数。对于当前的状态 (x, y, rest)，它可以向最多四个新状态进行搜索，即将玩家 (x, y) 向四个方向移动一格。假设移动的方向为 (dx, dy)，那么玩家的新位置为 (mx + dx, my + dy)。如果该位置为障碍物，那么新的状态为 (mx + dx, my + dy, rest - 1)，否则新的状态为 (mx + dx, my + dy, rest)。我们从初始状态 (0, 0, k) 开始搜索，当我们第一次到达状态 (m - 1, n - 1, k')，其中 k' 是任意非负整数时，就得到了从左上角 (0, 0) 到右下角 (m - 1, n - 1) 且最多经过 k 个障碍物的最短路径。

此外，我们还可以对搜索空间进行优化。注意到题目中 k 的上限为 m * n，但考虑一条从 (0, 0) 向下走到 (m - 1, 0) 再向右走到 (m - 1, n - 1) 的路径，它经过了 m + n - 1 个位置，其中起点 (0, 0) 和终点 (m - 1, n - 1) 没有障碍物，那么这条路径上最多只会有 m + n - 3 个障碍物。因此我们可以将 k 的值设置为 m + n - 3 与其本身的较小值 min(k, m + n - 3)，将广度优先搜索的时间复杂度从 O(MNK)O(MNK) 降低至 O(MN * \min(M + N, K))O(MN∗min(M+N,K))。

```python
class Solution:
    def shortestPath(self, grid: List[List[int]], k: int) -> int:
        m, n = len(grid), len(grid[0])
        if m == 1 and n == 1:
            return 0
        
        k = min(k, m + n - 3)
        visited = set([(0, 0, k)])
        q = collections.deque([(0, 0, k)])

        step = 0
        while len(q) > 0:
            step += 1
            cnt = len(q)
            for _ in range(cnt):
                x, y, rest = q.popleft()
                for dx, dy in [(-1, 0), (1, 0), (0, -1), (0, 1)]:
                    nx, ny = x + dx, y + dy
                    if 0 <= nx < m and 0 <= ny < n:
                        if grid[nx][ny] == 0 and (nx, ny, rest) not in visited:
                            if nx == m - 1 and ny == n - 1:
                                return step
                            q.append((nx, ny, rest))
                            visited.add((nx, ny, rest))
                        elif grid[nx][ny] == 1 and rest > 0 and (nx, ny, rest - 1) not in visited:
                            q.append((nx, ny, rest - 1))
                            visited.add((nx, ny, rest - 1))
        return -1
```

### 5.6 拓扑排序

简化题目要求：给定`colConditions = [[2, 1], [3, 2]]`，代表2要在1的左边，3要在2的左边，然后给出任意一个满足这样条件排序

这里本质上是一个指向问题，对于这种入度出度的指向问题，可以看做一种拓扑排序

```python
import collections
from collections import deque

colConditions = [[2, 1], [3, 2]]
k = 3


edges=collections.defaultdict(list)

#存储当前每个节点的入度
indegree=[0]*k  # 一共最多k个节点

#遍历赋予初始值
for item in colConditions:
    edges[item[0]-1].append(item[1]-1)
    indegree[item[1]-1]+=1

#找到当前入度为0的节点
# queen=collections.deque([u for u in range(numCourses) if indegree[u] == 0])
queen=deque()
# for u in indegree
for u in range(k):
    if indegree[u]==0:
        queen.append(u)
visited = 0
res_col = []
while queen:
    visited +=1
    pre=queen.popleft()
    res_col.append(pre+1)
    for cur in edges[pre]:
        indegree[cur]-=1
        if indegree[cur]==0:
            queen.append(cur)

print("res_col: ", res_col)  # [3, 2, 1]
```

### 5.7 记忆化搜索

#### 5.7.1 Leetcode2435. 矩阵中和能被K整除的路径

给你一个下标从 0 开始的 m x n 整数矩阵 grid 和一个整数 k 。你从起点 (0, 0) 出发，每一步只能往 下 或者往 右 ，你想要到达终点 (m - 1, n - 1) 。

请你返回路径和能被 k 整除的路径数目，由于答案可能很大，返回答案对 109 + 7 取余 的结果

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029233319005.png" alt="image-20221029233319005" style="zoom:80%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029233328194.png" alt="image-20221029233328194" style="zoom:80%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029233336441.png" alt="image-20221029233336441" style="zoom:80%;" />

数据范围

- `m == grid.length`

- `n == grid[i].length`

- `1 <= m, n <= 5 * 10^4`

- `1 <= m * n <= 5 * 10^4`

- `0 <= grid[i][j] <= 100`

- `1 <= k <= 50`

```python
"""
记忆化搜索题目的思路

整除 -> 路径和对k取模，这里注意到k很小，所以可以O(mnk)，把取模结果当成状态中的一部分
(i, j, v)是我们的状态，走到(ij)位置处，当前路径和模k，结果为v时，符合题目条件的路径个数

"""
MOD = 10 ** 9 + 7

class Solution:
    def numberOfPaths(self, grid: List[List[int]], k: int) -> int:

        n, m = len(grid), len(grid[0])

        # 记忆化搜索
        @cache
        def dfs(i: int, j: int, v: int) -> int:
            if i < 0 or j < 0:
                return 0
            
            v = (v + grid[i][j]) % k
            
            if i == 0 and j == 0:  # 记忆化搜索的原点套路，代表从0到这个位置
                if v == 0:
                   return 1
                else:
                    return 0

            return (dfs(i-1, j, v) + dfs(i, j-1, v)) % MOD
        
        res = dfs(n-1, m-1, 0)
        dfs.cache_clear()  # 容易爆内存
        return res
```

#### 5.7.2 Leetcode2267. 检查是否有合法括号字符串路径

一个括号字符串是一个 非空 且只包含 '(' 和 ')' 的字符串。如果下面 任意 条件为 真 ，那么这个括号字符串就是 合法的 。

​	字符串是 () 。
​	字符串可以表示为 AB（A 连接 B），A 和 B 都是合法括号序列。
​	字符串可以表示为 (A) ，其中 A 是合法括号序列。

给你一个 m x n 的括号网格图矩阵 grid 。网格图中一个 合法括号路径 是满足以下所有条件的一条路径：

​	路径开始于左上角格子 (0, 0) 。
​	路径结束于右下角格子 (m - 1, n - 1) 。
​	路径每次只会向 下 或者向 右 移动。
​	路径经过的格子组成的括号字符串是 合法 的。
如果网格图中存在一条 合法括号路径 ，请返回 true ，否则返回 false 。

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029234033703.png" alt="image-20221029234033703" style="zoom:80%;" />

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029234043801.png" alt="image-20221029234043801" style="zoom:80%;" />

- `m == grid.length`

- `n == grid[i].length`

- `1 <= m, n <= 100`

- `grid[i][j] 要么是 '(' ，要么是 ')' 。`

解答：

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029234147392.png" alt="image-20221029234147392" style="zoom:80%;" />

```python
class Solution:
    def hasValidPath(self, grid: List[List[str]]) -> bool:
        m, n = len(grid), len(grid[0])
        if (m + n) % 2 == 0 or grid[0][0] == ')' or grid[m - 1][n - 1] == '(': return False  # 剪枝

        @cache  # 效果类似 vis 数组
        def dfs(x: int, y: int, c: int) -> bool:
            if c > m - x + n - y - 1: return False  # 剪枝：即使后面都是 ')' 也不能将 c 减为 0
            if x == m - 1 and y == n - 1: return c == 1  # 终点一定是 ')'
            c += 1 if grid[x][y] == '(' else -1
            return c >= 0 and (x < m - 1 and dfs(x + 1, y, c) or y < n - 1 and dfs(x, y + 1, c))  # 往下或者往右
        return dfs(0, 0, 0)  # 起点
```

## 6. 字符串

### 6.1 KMP算法/正则表达式相关函数的使用

给定两个字符串`haystack`和`needle`，在`haystack`中找出`needle`字符串第一个匹配的下标，如果不存在，返回`-1`

可以拓展到查找多个的场景

- KMP

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        a=len(needle)
        b=len(haystack)
        if a==0:
            return 0
        next=self.getnext(a,needle)
        p=-1
        for j in range(b):
            while p>=0 and needle[p+1]!=haystack[j]:
                p=next[p]
            if needle[p+1]==haystack[j]:
                p+=1
            if p==a-1:
                return j-a+1
        return -1

    def getnext(self,a,needle):
        next=['' for i in range(a)]
        k=-1
        next[0]=k
        for i in range(1,len(needle)):
            while (k>-1 and needle[k+1]!=needle[i]):
                k=next[k]
            if needle[k+1]==needle[i]:
                k+=1
            next[i]=k
        return next
```

- `str.find()`

```python
class Solution(object):
    def strStr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype: int
        """
        return haystack.find(needle)  # 会返回第一次出现的位置，查找多个可以截断推导
```

- `re.finditer()`找到所有出现的位置

```python
import re 
 
# defining string  
str1 = "good. This dress looks good; you have good taste in clothes."
 
#defining substring 
substr = "good"

result = [_.start() for _ in re.finditer(substr, str1)] 
>>> [0, 23, 38]
```

### 6.2 马拉车算法（On求解最长最长回文子串）

```python
def manacher(s):
 
    assert '$' not in s and '^' not in s and '#' not in s
    if s == '':
        return (0, 1)
    t = "^#" + "#".join(s) + "#s"
    c = 0
    d = 0
    p = [0] * len(t)
    for i in range(1, len(t) - 1):
        mirror = 2 * c - i
        p[i] = max(0, min(d - i, p[mirror]))
        while t[i + 1 + p[i]] == t[i - 1 - p[i]]:
            p[i] += 1
        if i + p[i] > d:
            c = i
            d = i + p[i]
    (k, i) = max((p[i], i) for i in range(1, len(t) - 1))

    substr = s[(i - k) // 2 : (i + k) // 2]
    len_of_substr = len(substr)
    return substr, len_of_substr

>>> substr, len_of_substr = manacher('cabaabaabaa')
>>> print(substr, len_of_substr) 
abaabaaba 9
```

### 6.3 回文

- O(n/2)判断是否是回文，

```python
def isPalindromic(s):
    # 判断回文请用这个比较短的板子
    n = len(s)
    for i in range(n//2):
        if s[i] != s[n-1-i]:
            return False
    return True

if __name__ == '__main__':
    print(isPalindromic("aba"))  # True
    
    print(isPalindromic(""))  # True

    print(isPalindromic("a"))  # True

    print(isPalindromic("ab"))  # False
```

### 6.4 字符串哈希

大概的思路是，对比两个字符串是否相等的时候，在字符串有增减的场景下，可以不断的O1来判断

- Leetcode1392. 最长快乐前缀

**「快乐前缀」**是在原字符串中既是 **非空** 前缀也是后缀（不包括字符串本身）的字符串。

给你一个字符串`s`，请你返回它的 **最长快乐前缀** 。如果不存在满足题意的前缀，则返回一个空字符串`""`。

示例1：

```shell
输入：s = "level"
输出："l"
解释：
不包括 s 自己
一共有 4 个前缀（"l", "le", "lev", "leve"）和
4 个后缀（"l", "el", "vel", "evel"）。
最长的既是前缀也是后缀的字符串是 "l" 。
```

示例2：

```shell
输入：s = "ababab"
输出："abab"
解释：
"abab" 是最长的既是前缀也是后缀的字符串。
题目允许前后缀在原字符串中重叠。
```

解答

```python
class Solution:
    def longestPrefix(self, s: str) -> str:
        """
        :type s: str
        :rtype: str
        """
        mod = 10**9 + 7
        product = 31

        prefix = 0
        subfix = 0

        times = 1
        res_idx = 0
        for i in range(len(s)-1):
            prefix = prefix * product + (ord(s[i]) - ord('a'))  # 相当于往后拼
            prefix %= mod

            subfix = subfix + (ord(s[len(s)-1-i]) - ord('a')) * times  # 相当于往前拼
            times *= product
            times %= mod
            subfix %= mod
        
            # print(prefix)
            # print(subfix)

            if prefix == subfix:
                res_idx = i+1
        
        return s[:res_idx]
```

### 6.5 常用正则表达式

#### 6.5.1 正则表达式基本规则

- 字符: `a` `b` `c` `d` `1` `2` `3` `4` etc.

- 字符类: `.` `[abc]` `[a-z]` `\d` `\w` `\s`

- - `.` 代表任何字符
  - `\d 表示`“数字”
  - `\w` 表示”字母”, `[0-9A-Za-z_]`
  - `\s `表示 “空格, 制表符,回车或换行符”
  - 否定字符类: `[^abc]` `\D` `\W` `\S`

- 重复: `{4}` `{3,16}` `{1,}` `?` `*` `+`

- - `?` 表示 “零次或一次”
  - `*` 表示 “大于零次”
  - `+` 表示 “一次或一次以上”
  - 如果不加上？，所有的重复都是最长匹配的（贪婪）

- 分组: `(Septem|Octo|Novem|Decem)ber`

- 词，行以及文本的分隔: `\b` `^` `$` `\A` `\z`

- 转义字符: `\1` `\2` `\3` etc. (在匹配表达式和替换表达式中都可用)



- 元字符: `.` `\` `[` `]` `{` `}` `?` `*` `+` `|` `(` `)` `^` `$`
- 在字符类中使用元字符: `[` `]` `\` `-` `^`
- 使用反斜杠可以忽略元字符: `\`

#### 6.5.2 在Python中使用正则表达式

正则表达式在python通过下面这个方式来使用

- `re.sub(pattern, replace_str, input_str`BJUTACM-17蓝桥杯预选

判断一下一个网址是否符合 http://a.b.c.d/ 的格式吧。 (a, b, c, d 均为长度在 [1, 5] 且由数字 0-9 构成的字符串)

```python
import re

if __name__ == '__main__':
    pattern = re.compile(r'http://[0-9]{1,5}\.[0-9]{1,5}\.[0-9]{1,5}\.[0-9]{1,5}/')
    input_str = input().strip()
	
    # 使用替换的思路，替换成特殊字符或者空格，也是常用的技巧
    tmp_str = re.sub(pattern, '', input_str)  
    if len(tmp_str) == 0:
        print("YES")
    else:
        print("NO")

http://172.21.17.211/
YES   
```

- `re.match(pattern, input_str)`

```python
import re

line = "Cats are smarter than dogs"

pattern = re.compile(r"(.*) are (.*?) .*", re.M | re.I)  # re.I表示忽略大小写

match_obj = re.match(pattern, line)  

if match_obj:
    print("match_obj.group(): ", match_obj.group())
    print("match_obj.group(1): ", match_obj.group(1))
    print("match_obj.group(2): ", match_obj.group(2))
else:
    print("No match")

match_obj.group():  Cats are smarter than dogs
match_obj.group(1):  Cats
match_obj.group(2):  smarter
```

## 7. 经典算法（最长上升/公共子序列，多类背包，...）

### 7.1 最长公共子序列

- 不带记录

```python
class Solution(object):
    def longestCommonSubsequence(self, text1, text2):
        """
        :type text1: str
        :type text2: str
        :rtype: int
        """
        dp = [[0 for _ in range(len(text2)+1)] for _ in range(len(text1)+1)]  # h横代表text1，纵代表text2

        for i in range(1, len(text1)+1):
            for j in range(1, len(text2)+1):
                if text1[i-1] == text2[j-1]:
                    dp[i][j] = dp[i-1][j-1] + 1
                else:
                    dp[i][j] = max(dp[i][j-1], dp[i-1][j])
        # print(dp)
        return dp[len(text1)][len(text2)]
```

- 一维数组dp+不带记录

```python
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        n1 = len(text1)
        n2 = len(text2)
        dp = [0] * (n2+1) # 优化空间，仅仅使用一维数组进行dp
        for i in range(1, n1+1):
            prev = dp[0]
            for j in range(1, n2+1):
                tmp = dp[j]
                
                if text1[i-1] == text2[j-1]:
                    dp[j] = prev + 1
                else:
                    dp[j] = max(dp[j], dp[j-1])

                prev = tmp
        return dp[n2]
```

### 7.2 最长上升子序列

- 不带记录

```python
# Dynamic programming + Dichotomy.
class Solution:
    def lengthOfLIS(self, nums: [int]) -> int:
        tails, res = [0] * len(nums), 0
        for num in nums:
            i, j = 0, res
            while i < j:
                m = (i + j) // 2
                if tails[m] < num: i = m + 1 # 如果要求非严格递增，将此行 '<' 改为 '<=' 即可。
                else: j = m
            tails[i] = num
            if j == res: res += 1
        return res
```

- 带记录+库函数

```python
import bisect

class Solution:
    def lengthOfLIS(self, nums):
        dp = [nums[0]]
        for num in nums[1:]:
            i = bisect.bisect_left(dp, num)
            if i < len(dp):
                dp[i] = num
            else:
                dp.append(num)
        print(dp)
        return len(dp)


if __name__ == '__main__':
    S = Solution()

    nums = [10,9,2,5,3,7,101,18]
    print(S.lengthOfLIS(nums))
```

### 7.3 背包相关

- 0-1背包（注：可以用后面的多重背包给简化了， 简化成一维的）

N种物品和一个容量是V的背包，每种物品只可用**1次**

第i种物品的体积是v_i，价值是w_i

求解将哪些物品装入背包，可使这些物品总体积不超过背包容量，且总价值最大

输出最大价值

```shell
# 输入
4 5  # N, V
1 2  # vi, wi
2 4
3 4
4 5

# 输出
8
```

```python
# N件物品，一个容量为V的背包
N,V = map(int,input().split())
weight = []
val = []
# 创建两个列表，分别装入第i个物品的体积wi和价值vi
for _ in range(N):
    wi,vi = map(int,input().split())
    weight.append(wi)
    val.append(vi)
# base case: dp[0][...] = dp[...][0]=0
dp = [[0]*(V+1) for _ in range(N+1)] # (N+1)*(V+1) 
# dp[i][w]代表对于前i个物品，当背包容量为w时，可以装的最大价值
for i in range(1,N+1):
    for w in range(1,V+1):
        # 简化版本
        dp[i][w] = dp[i-1][w]
        if w-weight[i-1]>=0:
            dp[i][w] = max(dp[i][w], dp[i-1][w-weight[i-1]]+val[i-1])
print(dp[N][V])
```

- 完全背包

N种物品和一个容量是V的背包，每种物品都有**无限件**可用

第i种物品的体积是v_i，价值是w_i

求解将哪些物品装入背包，可使这些物品总体积不超过背包容量，且总价值最大

输出最大价值

```shell
# 输入
4 5  # N, V
1 2  # vi, wi
2 4
3 4
4 5

# 输出
10
```

```python
N,V = map(int,input().split())
weight = []
val = []
for _ in range(N):
    wi,vi = map(int,input().split())
    weight.append(wi)
    val.append(vi)
# dp[i][j]代表使用前i个物品，针对容量j的最大价值
# dp[0][...] 为0， dp[...][0]为0
dp = [[0]*(V+1) for _ in range(N+1)]

for i in range(1,N+1):
    for j in range(1,V+1):
        dp[i][j] = dp[i-1][j]
        if j>=weight[i-1]:
            dp[i][j] = max(dp[i][j], dp[i][j-weight[i-1]]+val[i-1])
# print(dp)
print(max(dp[N]))
```

- 多重背包-simple

有N种物品和一个容量是V的背包

第i种物品最多有s_i件，每件体积是v_i，价值是w_i

求解将哪些物品装入背包，可以使物品总体积不超过背包容量，且总价值最大

输出最大价值

```shell
# 输入
4 5  # N, V
1 2 3  # v_i, w_i, s_i
2 4 1
3 4 3
4 5 2

# 输出
10
```

```c++
#include <bits/stdc++.h>
using namespace std;
int dp[1005],n,t,v,w,s;
main()
{
    cin>>n>>t;
    while(n--)
    {
        cin>>w>>v>>s;
        while(s--)
            for(int j=t;j>=w;j--)
            	dp[j]=max(dp[j],dp[j-w]+v);
    }
    cout<<dp[t];
}
```

二进制优化：TODO

单调队列优化：TODO

## 8. DP

### 8.1 数位dp（暂时列举系列题目了）

#### 8.1.1 数字1的个数

给定一个整数 `n`，计算所有小于等于 `n` 的非负整数中数字 `1` 出现的个数。

**示例 1：**

```
输入：n = 13
输出：6
```

**示例 2：**

```
输入：n = 0
输出：0
```

**提示：**

- `0 <= n <= 109`

```python
class Solution:
    def countDigitOne(self, n: int) -> int:
        # 数位dp
        s = str(n)  # 把n转成字符串的形式
        
        # 返回从i开始填数字，i前面填的数字的集合是mask，此时能够造出的特殊整数的数目
        # is_limit 表示前面填的数字是否**都是**n对应位上的，如果为True那么当前位至多为int(s[i])，否则至多为9
        # is_num 表示前面是否填了数字（是否跳过），如果为True那么当前位可以从0开始，如果为False那么我们可以直接跳过或者从1开始填数字
        @cache
        def f(i: int, mask: int, is_limit: bool, is_num: bool, cnt1: int) -> int:
            # 判断终点，到达终点后如果之前填过数字那么返回1表示已经构造出一个特殊整数了，否则return0
            if i == len(s):
                return cnt1
            res = 0
            if not is_num:  # 如果不是数字的话，选择跳过不填数字
                res = f(i+1, None, False, False, cnt1)
            # 上界，如果is_limit则是s[i]这个位置的值，否则是9
            up = int(s[i]) if is_limit else 9
			# 枚举要填的数字，枚举的范围取决于is_limit和is_num
            for d in range(0 if is_num else 1, up+1):  # 这里决定了如果给1234，那么不会走到1235的情况
                if d == 1:
                    res += f(i+1, None, is_limit and d == up, True, cnt1+1)
                else:
                    res += f(i+1, None, is_limit and d == up, True, cnt1)

            return res
       
        return f(0, None, True, False, 0)  # 为什么is_limit要初始化成True因为后面填的要受到约束；
```

#### 8.1.2 最大为N的数字组合

给定一个按 非递减顺序 排列的数字数组 digits 。你可以用任意次数 digits[i] 来写的数字。例如，如果 digits = ['1','3','5']，我们可以写数字，如 '13', '551', 和 '1351315'。

返回 可以生成的小于或等于给定整数 n 的正整数的个数 。

示例 1：

输入：digits = ["1","3","5","7"], n = 100
输出：20
解释：
可写出的 20 个数字是：
1, 3, 5, 7, 11, 13, 15, 17, 31, 33, 35, 37, 51, 53, 55, 57, 71, 73, 75, 77.
示例 2：

输入：digits = ["1","4","9"], n = 1000000000
输出：29523
解释：
我们可以写 3 个一位数字，9 个两位数字，27 个三位数字，
81 个四位数字，243 个五位数字，729 个六位数字，
2187 个七位数字，6561 个八位数字和 19683 个九位数字。
总共，可以使用D中的数字写出 29523 个整数。
示例 3:

输入：digits = ["7"], n = 8
输出：1


提示：

1 <= digits.length <= 9
digits[i].length == 1
digits[i] 是从 '1' 到 '9' 的数
digits 中的所有值都 不同 
digits 按 非递减顺序 排列
1 <= n <= 109

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029234931350.png" alt="image-20221029234931350" style="zoom:80%;" />

```python
class Solution:
    def atMostNGivenDigitSet(self, digits: List[str], n: int) -> int:
        s = str(n)
        @cache
        def f(i: int, is_limit: bool, is_num: bool) -> int:
            if i == len(s): return int(is_num)  # 如果填了数字，则为 1 种合法方案
            res = 0
            if not is_num:  # 前面不填数字，那么可以跳过当前数位，也不填数字
                # is_limit 改为 False，因为没有填数字，位数都比 n 要短，自然不会受到 n 的约束
                # is_num 仍然为 False，因为没有填任何数字
                res = f(i + 1, False, False)
            up = s[i] if is_limit else '9'  # 根据是否受到约束，决定可以填的数字的上限
            # 注意：对于一般的题目而言，如果此时 is_num 为 False，则必须从 1 开始枚举，由于本题 digits 没有 0，所以无需处理这种情况
            for d in digits:  # 枚举要填入的数字 d
                if d > up: break  # d 超过上限，由于 digits 是有序的，后面的 d 都会超过上限，故退出循环
                # is_limit：如果当前受到 n 的约束，且填的数字等于上限，那么后面仍然会受到 n 的约束
                # is_num 为 True，因为填了数字
                res += f(i + 1, is_limit and d == up, True)
            return res
        return f(0, True, False)
```

#### 8.1.3 统计特殊整数



如果一个正整数每一个数位都是 互不相同 的，我们称它是 特殊整数 。

给你一个 正 整数 n ，请你返回区间 [1, n] 之间特殊整数的数目。

 

示例 1：

输入：n = 20
输出：19
解释：1 到 20 之间所有整数除了 11 以外都是特殊整数。所以总共有 19 个特殊整数。
示例 2：

输入：n = 5
输出：5
解释：1 到 5 所有整数都是特殊整数。
示例 3：

输入：n = 135
输出：110
解释：从 1 到 135 总共有 110 个整数是特殊整数。
不特殊的部分数字为：22 ，114 和 131 。


提示：

1 <= n <= 2 * 109

<img src="http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221029235036694.png" alt="image-20221029235036694" style="zoom:80%;" />

```python
# 数位dp模板如下，以这个题目为例，这里是记忆化搜索的话写成python3的形式了
class Solution:
    def countSpecialNumbers(self, n: int) -> int:
        # 数位dp
        s = str(n)  # 把n转成字符串的形式
        
        # 返回从i开始填数字，i前面填的数字的集合是mask，此时能够造出的特殊整数的数目
        # is_limit 表示前面填的数字是否**都是**n对应位上的，如果为True那么当前位至多为int(s[i])，否则至多为9
        # is_num 表示前面是否填了数字（是否跳过），如果为True那么当前位可以从0开始，如果为False那么我们可以直接跳过或者从1开始填数字
        @cache
        def f(i: int, mask: int, is_limit: bool, is_num: bool) -> int:
            # 判断终点，到达终点后如果之前填过数字那么返回1表示已经构造出一个特殊整数了，否则return0
            if i == len(s):
                return int(is_num)
            res = 0
            if not is_num:  # 如果不是数字的话，选择跳过不填数字
                res = f(i+1, mask, False, False)
            # 上界，如果is_limit则是s[i]这个位置的值，否则是9
            up = int(s[i]) if is_limit else 9
			# 枚举数字
            for d in range(0 if is_num else 1, up+1):  # 这里决定了如果给1234，那么不会走到1235的情况
                # 判断一下mask里面有没有d，首先这里mask虽然是一个int但是代表了一个数组！
                # mask很巧妙的0-9这种的，第几位就代表有没有
                if mask >> d & 1 == 0:  
                    res += f(i+1, mask|(1<<d), is_limit and d == up, True)  # 如果满足条件我要选的话，就把1左移d位那个地方放上
            return res
       
        return f(0, 0, True, False)
```

