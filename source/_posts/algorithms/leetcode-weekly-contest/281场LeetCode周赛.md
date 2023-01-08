---
title: 281场LeetCode周赛
date: 2022-02-27 19:12:42
tags:
    - 数学
    - 模拟
    - 链表
    - 贪心
    - 字符串
    - 计数
    - 堆
    - 优先队列
    - EASY
    - MEDIUM
categories:
	- algorithms
	- leetcode-weekly-contest
---

周赛综述&总结：

依旧是前两个题很简单，然后第三题直接卡到死的节奏，感觉近几次周赛第三题感觉都不是很好，可能需要阶段性的复习一下？

总结下来的话算法似乎都是在可以知道范围内的，比如这次题印象比较深的就是要正确理解题意或者说不要走到和题意错误的路上，有些题可能需要灵光一现用一些广泛用的Coutner、二分、前缀和、有限队列单调栈类的，有些题可能写着写着会有思路吧。

总之感觉刷题这个事还是要持续进行，总有瓶颈也总需要突破，冲！

第一题：看到```n < 1000```感觉就直接暴力模拟就行了，模拟起来还是相对简单的，python对字符的操作太方便了；

第二题：一个链表的操作题，很简单就能搞定了，纯链表题现在感觉没有太难的；

第三题：这个题这次又卡的时间太长了，开始想的是排序后原地交换的思路，然后感觉就思维掉到一个坑里边了，一直在排序后原地交换一些细节条件里考虑来考虑去。后来大佬提醒说可以用Counter后排序贪心的思路，感觉这个思路一下就简单并且明确了；

第四题：鸽了鸽了。

<!--more-->

# 第一题：2180.统计各位数字之和为偶数的整数个数

[题目链接](https://leetcode-cn.com/problems/count-integers-with-even-digit-sum/)

## 题目大意

给你一个正整数 ```num``` ，请你统计并返回 **小于或等于** ```num``` 且各位数字之和为 **偶数** 的正整数的数目。

正整数的 **各位数字之和** 是其所有位上的对应数字相加的结果。

示例1：
```
输入：num = 4
输出：2
解释：
只有 2 和 4 满足小于等于 4 且各位数字之和为偶数。 
```

示例2：
```
输入：num = 30
输出：14
解释：
只有 14 个整数满足小于等于 30 且各位数字之和为偶数，分别是： 
2、4、6、8、11、13、15、17、19、20、22、24、26 和 28 。
```

提示：
- ```1 <= num <= 1000```

## 分析和解答

考虑到数据范围的1000，直接暴力就可以了

```python
class Solution(object):
    def countEven(self, num):
        """
        :type num: int
        :rtype: int
        """
        
        def judge(n):
            res = 0
            str_n = str(n)
            for i in range(len(str_n)):
                res += int(str_n[i])
            return res % 2 == 0
        
        res = 0
        for i in range(1, num+1):
            if judge(i):
                res += 1
        
        return res
```


# 第二题：2181.合并零之间的节点

[题目链接](https://leetcode-cn.com/problems/merge-nodes-in-between-zeros/)

## 题目大意

给你一个链表的头节点 ```head``` ，该链表包含由 ```0``` 分隔开的一连串整数。链表的 **开端** 和 **末尾** 的节点都满足 ```Node.val == 0``` 。

对于每两个相邻的 ```0`` ，请你将它们之间的所有节点合并成一个节点，其值是所有已合并节点的值之和。然后将所有 0 移除，修改后的链表不应该含有任何 0 。

返回修改后链表的头节点 ```head``` 。

示例1：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-02-27-22-48-01.png)
```
输入：head = [0,3,1,0,4,5,2,0]
输出：[4,11]
解释：
上图表示输入的链表。修改后的链表包含：
- 标记为绿色的节点之和：3 + 1 = 4
- 标记为红色的节点之和：4 + 5 + 2 = 11
```

示例2：
![](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/2022-02-27-22-48-28.png)
```
输入：head = [0,1,0,3,0,2,2,0]
输出：[1,3,4]
解释：
上图表示输入的链表。修改后的链表包含：
- 标记为绿色的节点之和：1 = 1
- 标记为红色的节点之和：3 = 3
- 标记为黄色的节点之和：2 + 2 = 4
```

提示：
- 列表中的节点数目在范围 ```[3, 2 * 10^5]``` 内
- ```0 <= Node.val <= 1000```
- **不** 存在连续两个 ```Node.val == 0``` 的节点
- 链表的 **开端** 和 **末尾** 节点都满足 ```Node.val == 0```

## 分析和解答

看起来提示里很多条件都是避免特殊情况用的，链表题比较复杂的地方在于经常有一些很极端的case只能靠测试样例来debug，很难一次想到，这个题没有这个问题不过也需要注意下

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def mergeNodes(self, head):
        """
        :type head: Optional[ListNode]
        :rtype: Optional[ListNode]
        """
        new_head = ListNode(val=0, next=None)
        q = new_head

        tmp = 0
        head = head.next
        while head is not None:
            if head.val == 0:
                tmp_node = ListNode(val=tmp)
                q.next = tmp_node
                q = q.next
                tmp = 0
            else:
                tmp += head.val

            head = head.next

        return new_head.next
```


# 第三题：2182.构造限制重复的字符串

[题目链接](https://leetcode-cn.com/problems/construct-string-with-repeat-limit/)

## 题目大意

给你一个字符串 ```s``` 和一个整数 ```repeatLimit``` ，用 ```s``` 中的字符构造一个新字符串 ```repeatLimitedString``` ，使任何字母 **连续** 出现的次数都不超过 ```repeatLimit``` 次。你不必使用 ```s``` 中的全部字符。

返回 **字典序最大的** ```repeatLimitedString``` 。

如果在字符串 ```a``` 和 ```b``` 不同的第一个位置，字符串 ```a``` 中的字母在字母表中出现时间比字符串 ```b``` 对应的字母晚，则认为字符串 ```a``` 比字符串 ```b``` **字典序更大** 。如果字符串中前 ```min(a.length, b.length)``` 个字符都相同，那么较长的字符串字典序更大。

示例1：
```
输入：s = "cczazcc", repeatLimit = 3
输出："zzcccac"
解释：使用 s 中的所有字符来构造 repeatLimitedString "zzcccac"。
字母 'a' 连续出现至多 1 次。
字母 'c' 连续出现至多 3 次。
字母 'z' 连续出现至多 2 次。
因此，没有字母连续出现超过 repeatLimit 次，字符串是一个有效的 repeatLimitedString 。
该字符串是字典序最大的 repeatLimitedString ，所以返回 "zzcccac" 。
注意，尽管 "zzcccca" 字典序更大，但字母 'c' 连续出现超过 3 次，所以它不是一个有效的 repeatLimitedString 。
```

示例2：
```
输入：s = "aababab", repeatLimit = 2
输出："bbabaa"
解释：
使用 s 中的一些字符来构造 repeatLimitedString "bbabaa"。 
字母 'a' 连续出现至多 2 次。 
字母 'b' 连续出现至多 2 次。 
因此，没有字母连续出现超过 repeatLimit 次，字符串是一个有效的 repeatLimitedString 。 
该字符串是字典序最大的 repeatLimitedString ，所以返回 "bbabaa" 。 
注意，尽管 "bbabaaa" 字典序更大，但字母 'a' 连续出现超过 2 次，所以它不是一个有效的 repeatLimitedString 。
```

提示：
- 1 <= repeatLimit <= s.length <= 10^5
- ```s```由小写英文字母组成

## 分析和解答

这个题现场做的时候又做了好久好久，最后还是gg了，当时主要考虑的是一种排序后交换的思路，但是交换的思路最后就越换越乱，比如换完了后在那个位置的“指针”是否要++这样的；

后来大佬给了一种Counter计数排序后贪心的思路感觉能想到的话代码实现就比较简单了，每次取最头的元素，然后取够个数了后就交替取一个下一个，如果没有下一个就代表已经取不了了，这样实际上有一种类似于队列的小思想在里边

另外删除列表中的一个元素可以用```del tmp[i]```这样的操作来做

```python
class Solution(object):
    def repeatLimitedString(self, s, repeatLimit):
        """
        :type s: str
        :type repeatLimit: int
        :rtype: str
        """
        # 还是要多锻炼多总结思路hhh
        # 一种贪心算法的思想，Counter后排序，然后相对贪心一点的拿就行了
        # 开始的思路想的是排序后有一种原地交换的感觉，但后来还是换着换着就乱了
        
        counter_s = Counter(s)
        # print(counter_s)
        counter_s_list = sorted([list(x) for x in counter_s.items()], key=lambda x: x[0])
        counter_s_list.reverse()
        # print(counter_s_list)

        result = []
        while len(counter_s_list):  # 循环直到为空为止，这里不用循环变量i，直接用0就行了
            if counter_s_list[0][1] <= repeatLimit:
                result.append(counter_s_list[0][0] * counter_s_list[0][1])
                counter_s_list = counter_s_list[1:]
            else:
                result.append(counter_s_list[0][0] * repeatLimit)
                counter_s_list[0][1] -= repeatLimit
                if counter_s_list[0][1] > 0: # 如果还有的话，如果此时列表就1个了，就结束，否则和下一个交换
                    if len(counter_s_list) > 1:
                        result.append(counter_s_list[1][0])
                        counter_s_list[1][1] -= 1
                        if counter_s_list[1][1] == 0:
                            # counter_s_list = counter_s_list[:1] + counter_s_list[2:]  # 去掉，这里写的其实不够简化
                            del counter_s_list[1]
                    else:
                        break

        
        # print(result)
        return ''.join(result)
```