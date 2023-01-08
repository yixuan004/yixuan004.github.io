---
title: 273-283场LeetCode周赛总结
date: 2022-05-04 17:44:54
tags:
    - EASY
    - MEDIUM
    - HARD
categories:
	- algorithms
	- leetcode-weekly-contest
---
273-283场LeetCode周赛综述&总结：

LeetCode感觉周赛做多了有时候思维就容易僵化，像中间有几次感觉对第二题、第三题直接走到不同思路上去了就直接坐牢；

温故而知新，定期总结下周赛题目的套路，希望早日能成为周赛三题&前1000名选手吧，现在感觉只有在手感特别好还有题目完全正好适配的时候才能前1000，剩下的情况能进前2000都是大胜利了，还是要不断刷题积累下了~

另外这里相对整理一下需要数据结构/特殊技巧才能做出来的题，比较水的题可能就不放在这里了，可以参考各次博客内容；

总结来看主要有这些操作：

【前后缀和】【数学&找规律】【哈希表mapping】【贪心】【Counter计数】【滑动窗口】【哨兵】【模拟】【动态规划】【设计&构造】【链表】【二分】【字符串】【水题】

<!--more-->

# 前后缀和
> 前后缀和大概是因为每次都要统计相同的位置，那么就是每次在基础上做累加的操作来避免每次对于相同位置的统计；

<font color='red'>※</font>**273场周赛第3题** [2121.相同元素的间隔之和](https://leetcode-cn.com/problems/intervals-between-identical-elements/)，这个题同时被归入了 `前后缀和` 、 `数学&找规律` 和 `哈希表mapping` 三个类型，这个题算是遇到相对比较难的第三题了，制作一种类似于 `{1: [1, 3], 2: [0, 4], 3: [2, 5, 6]}` 的mapping思路，然后可以在内部开始模拟，用数学的方法找一找规律，前后缀和在这个过程中可能就能发现前缀和相关的一些优化了，总结来说虽然可能是连续刷周赛的第一次周赛，但是这个题在各次来说也是比较难的第三题了，降时间复杂度就要想mapping，前后缀和这些思路；

<font color='red'>※</font>**280场周赛第3题** [2171.拿出最少数目的魔法豆](https://leetcode-cn.com/problems/removing-minimum-number-of-magic-beans/)，这个题同时被归入了 `前后缀和` 和 `贪心` 的思路，排序后前缀拿过去就可以了；

# 数学&找规律
> 数学和找规律的题一般都要现场推，自己动手模拟下看看，也没什么太多复盘的思路，每次遇到的感觉都不会太一样；

<font color='red'>※</font>**273场周赛第3题** [2121.相同元素的间隔之和](https://leetcode-cn.com/problems/intervals-between-identical-elements/)，这个题同时被归入了 `前后缀和` 、 `数学&找规律` 和 `哈希表mapping` 三个类型，这个题算是遇到相对比较难的第三题了，制作一种类似于 `{1: [1, 3], 2: [0, 4], 3: [2, 5, 6]}` 的mapping思路，然后可以在内部开始模拟，用数学的方法找一找规律，前后缀和在这个过程中可能就能发现前缀和相关的一些优化了，总结来说虽然可能是连续刷周赛的第一次周赛，但是这个题在各次来说也是比较难的第三题了，降时间复杂度就要想mapping，前后缀和这些思路；

**283场周赛第2题** [2195.向数组中追加 K 个整数](https://leetcode-cn.com/problems/append-k-integers-with-minimal-sum/)，暴力哈希的思路还是比较好像，但实际上不能这样，还要往后补的思路，总结数学题来说就是每次现场推就行了，复盘的时候不用想的太明白；

# 哈希表mapping
> 哈希表是个简化时间复杂度的好东西，万物皆可哈希hhhh；

<font color='red'>※</font>**273场周赛第3题** [2121.相同元素的间隔之和](https://leetcode-cn.com/problems/intervals-between-identical-elements/)，这个题同时被归入了 `前后缀和` 、 `数学&找规律` 和 `哈希表mapping` 三个类型，这个题算是遇到相对比较难的第三题了，制作一种类似于 `{1: [1, 3], 2: [0, 4], 3: [2, 5, 6]}` 的mapping思路，然后可以在内部开始模拟，用数学的方法找一找规律，前后缀和在这个过程中可能就能发现前缀和相关的一些优化了，总结来说虽然可能是连续刷周赛的第一次周赛，但是这个题在各次来说也是比较难的第三题了，降时间复杂度就要想mapping，前后缀和这些思路；

<font color='red'>※</font>**275场周赛第3题** [2135.统计追加字母可以获得的单词数](https://leetcode-cn.com/problems/count-words-obtained-after-adding-a-letter/)，这个题主要就是哈希表的思想，一方面来说 **重排** 这个事情可以在最开始的时候把大家都重排了这样一种预处理得到，另一方面来说set起到判重的作用，差一个字符上就是截断的拼接问题 `word[:i] + word[i+1:]` 这样可以把第i位的字符忽略掉，还是很难的这个题，这么看来说275场周赛的三个题都好难啊；

**277场周赛第3题** [2150.找出数组中的所有孤独数字](https://leetcode-cn.com/problems/find-all-lonely-numbers-in-the-array/)，这个题哈希表也太明显了，甚至有些情况够不上平常周赛第一题，第二题的难度，包装的也比较简单，其实可以算在水题里了；

# 贪心
> 贪心的事情，不用想的那么明白哈哈哈哈；

**274场周赛第3题** [2126.摧毁小行星](https://leetcode-cn.com/problems/destroying-asteroids/)，这个题算是很简单的第三题啊，274场周赛没有实时做，不然来说的话应该是一个靠前三题能rank前1000内的比赛，就是直接暴力贪心就可以了，中间带一个简单的排序，名言之贪心的事情不用搞那么明白！；

**276场周赛第2题** [2139.得到目标值的最少行动次数](https://leetcode-cn.com/problems/minimum-moves-to-reach-target-score/)，从大往小贪心就可以了，如果是偶数就除，如果是奇数就减就行了；

**279场周赛第2题** [2165.重排数字的最小值](https://leetcode-cn.com/problems/smallest-value-of-the-rearranged-number/)，这个题分正数负数贪心就可以了，前导0还是个约束条件；

**280场周赛第2题** [2170.使数组变成交替数组的最少操作数](https://leetcode-cn.com/problems/minimum-operations-to-make-the-array-alternating/)，第二题还是不能想太复杂了，奇数偶数位，选出分别两边出现最多的，但是 **不能相等的**，当时把自己坑惨了啊，还是要仔细读题，读歪了那可真是掉沟里了；

<font color='red'>※</font>**280场周赛第3题** [2171.拿出最少数目的魔法豆](https://leetcode-cn.com/problems/removing-minimum-number-of-magic-beans/)，这个题同时被归入了 `前后缀和` 和 `贪心` 的思路，排序后前缀拿过去就可以了；

<font color='red'>※</font>**281场周赛第3题** [2182.构造限制重复的字符串](https://leetcode-cn.com/problems/construct-string-with-repeat-limit/)，这个题真的难，不是特殊情况下一定不要往原地交换的思路想，要想往新构造的思路想，剩下就是贪心和随缘的面向答案编程了；

# Counter计数
> Counter是个很常用的数据结构，任何涉及到统计的的题目都可能会用到这个，不知道在其他语言Counter的对应是什么；

<font color='red'>※</font>**275场周赛第1题** [2133.检查是否每一行每一列都包含全部整数](https://leetcode-cn.com/problems/check-if-every-row-and-column-contains-all-numbers/)，是个比较难的第一题，甚至感觉很多情况下难度超过了第二题，一方面是对一行/一列取Counter计数的时候，应该是 `第i行：Counter(matrix[i])` 和 `第i列：Counter([matrix[x][i] for x in range(n)])`，另一方面来说Counter是可以加减，还有相比较的，这个是个很方便的特性不知道在其他语言里怎么使用这个内容，这个题的特色点在于只要遍历一次主对角线做Counter，就可以涵盖所有情况了；

<font color='red'>※</font>**275场周赛第2题** [2134.最少交换次数来组合所有的 1 II](https://leetcode-cn.com/problems/minimum-swaps-to-group-all-1s-together-ii/)，这个题同时被归入了 `滑动窗口`，`哨兵` 和 `Counter计数` 三种类型，算是相对比较难的第二题了，一方面在一个长度为有几个1的滑动的窗口中不断滑动过去，然后通过Counter看看把几个1聚在一起最少需要把几个0变成1，另一方面可以通过哨兵的机制，把环形的问题转成线型的问题，在这种需要计数类的问题的时候，Counter还是好用的！；

282场周赛第2题 [2186.使两字符串互为字母异位词的最少步骤数](https://leetcode-cn.com/problems/minimum-number-of-steps-to-make-two-strings-anagram-ii/)，经典Counter加减，莽过去！；

# 滑动窗口
> 主要看题目是否本身暗含了一种窗口的思想在里面；

<font color='red'>※</font>**275场周赛第2题** [2134.最少交换次数来组合所有的 1 II](https://leetcode-cn.com/problems/minimum-swaps-to-group-all-1s-together-ii/)，这个题同时被归入了 `滑动窗口`，`哨兵` 和 `Counter计数` 三种类型，算是相对比较难的第二题了，一方面在一个长度为有几个1的滑动的窗口中不断滑动过去，然后通过Counter看看把几个1聚在一起最少需要把几个0变成1，另一方面可以通过哨兵的机制，把环形的问题转成线型的问题，在这种需要计数类的问题的时候，Counter还是好用的！；

# 哨兵
> 主要是简化实现上的操作，比如把环变成数组，还有就是针对起始位置还有终点位置的特殊处理；

<font color='red'>※</font>**275场周赛第2题** [2134.最少交换次数来组合所有的 1 II](https://leetcode-cn.com/problems/minimum-swaps-to-group-all-1s-together-ii/)，这个题同时被归入了 `滑动窗口`，`哨兵` 和 `Counter计数` 三种类型，算是相对比较难的第二题了，一方面在一个长度为有几个1的滑动的窗口中不断滑动过去，然后通过Counter看看把几个1聚在一起最少需要把几个0变成1，另一方面可以通过哨兵的机制，把环形的问题转成线型的问题，在这种需要计数类的问题的时候，Counter还是好用的！；

**276场周赛第1题** [2138.将字符串拆分为若干长度为 k 的组](https://leetcode-cn.com/problems/divide-a-string-into-groups-of-size-k/)，也是水题但是可以通过哨兵补齐的思想，这样最后切分起来就很容易了，看了下当时的代码，不知道为什么当时能写那么复杂。。。；

# 模拟
> 一般按照题目来，跟着题目操作就可以；

**273场周赛第2题** [2120.执行所有后缀指令](https://leetcode-cn.com/problems/execution-of-all-suffix-instructions-staying-in-a-grid/)，使用 `[1:]` 不断截断，然后按照操作要求来模拟就行，这种题感觉也是不能着急，争取能在5分钟内完成第二题和第一题，要爆一下手速，第二题一般不会太难，不要太考虑时间复杂度的事情，想到方法基本就差不多了；

**274场周赛第2题** [2125.银行中的激光束数量](https://leetcode-cn.com/problems/number-of-laser-beams-in-a-bank/)，感觉是题目理解相对比较长的第二题，第二题可能存在题面复杂类的问题，但是通过这个题来看不能着急读题，读完题最好一遍能理解题意，尽快做出来；

**280场周赛第1题** [2169. 得到 0 的操作数](https://leetcode-cn.com/problems/count-operations-to-obtain-zero/)，两边来回减，按照题目的要求模拟就可以了；

# 动态规划
> 还是要多刷，包括普通dp，区间dp等等，跟一波wls了；

<font color='red'>※</font>**276场周赛第3题** [2140.解决智力问题](https://leetcode-cn.com/problems/solving-questions-with-brainpower/)，这个题dp还是相对能看出来的？每个位置是选和不选两种状态，但是这个题只能让自己觉得自己智力有问题，dp第三题还是难，看来276和275场周赛是把自己给虐了，这个题要存 **截止到当前位置的状态值！**

# 状态压缩（2^n那种类型的题目）
> 看数据范围有可能可以看出来是不是这类型的题目，特点就是每个状态下验证合理性的感觉吧；

<font color='red'>※</font>**277场周赛第4题** [2151.基于陈述统计最多好人数](https://leetcode-cn.com/problems/maximum-good-people-based-on-statements/)，还是比较难的，首先看数据范围如果是8，15这样比较小的数，那可能就要想能不能往2^n这种方向上做一做，这个题的关键思路还是当时室友给的，就是说：只需要判断假定所有说真话的人是否和条件有冲突就可以了，这种2^n很多情况都是判断每种情况是否合理的;

# 设计&构造
> 设计构造题目比较思路型，可能也是面试常考的题目，包括LRU也是设计类题目；

**279场周赛第3题** [2166.设计位集](https://leetcode-cn.com/problems/design-bitset/)，依照时间复杂度把一些能简化时间复杂度的操作转化为全局的，在 `__init__` 的时候设计全局变量，然后后边依照这个全局变量简化时间复杂度即可，设计的时候来说边设计应该就是能想到的；

<font color='red'>※</font>**283场周赛第3题** [2196.根据描述创建二叉树](https://leetcode-cn.com/problems/create-binary-tree-from-descriptions/)，这种设计的题，要把每个节点做成一个哈希，然后用哈希再往里插入就可以了，构造的题目好像要用这种哈希是比较普遍的思路；

# 链表
> 链表题还没在周赛见过太多，这个题是比较水的链表题；

**281场周赛第2题** [2181.合并零之间的节点](https://leetcode-cn.com/problems/merge-nodes-in-between-zeros/)，链表模拟操作，操作过去就行了；

# 二分
> 二分边界情况判断，最后是left还是right是真的难，二分也可以做在每个情况下，O(n)的合理性验证吧；

<font color='red'>※</font>**282场周赛第3题** [2187.完成旅途的最少时间](https://leetcode-cn.com/problems/minimum-time-to-complete-trips/)，自己想的时候没有看出来哪里可以二分，在大佬的提醒下二分验证每个时刻，O(n)判断judge是否合理，实际上有点像2^n那种题的验证思路了；

# 字符串
> 字符串题目还是很多的，一方面要会a-z A-Z这种ord chr的转化，另外一方面也要自己多练习一些正则的题目；

<font color='red'>※</font>**283场周赛第1题** [2194.Excel 表中某个范围内的单元格](https://leetcode-cn.com/problems/cells-in-a-range-on-an-excel-sheet/)，本来是个简单的二维遍历问题，但是居然不知道python怎么遍历字符。。。查了下是 `ord()` 把字符转数字, `chr()` 把数字转字符，这个还是要记住的，特别是一些a-z，A-Z的遍历题目；

# 水题

**273场周赛第1题** [2119.反转两次的数字](https://leetcode-cn.com/problems/a-number-after-a-double-reversal/)，稍微操作一下就可以完成这个模拟操作了，第一题千万注意的就是不要急躁，耐下心来一遍把题读完，然后2分钟之内刷出来；

**274场周赛第1题** [2124.检查是否所有 A 都在 B 之前](https://leetcode-cn.com/problems/check-if-all-as-appears-before-all-bs/)，标准水题，这种题感觉要半分钟读题，一分半内手速搞定暴力出来；

**277场周赛第1题** [2148.元素计数](https://leetcode-cn.com/problems/count-elements-with-strictly-smaller-and-greater-elements/)，能看出来是和最大最小比就很容易了，读题还是要细心呀；

**277场周赛第2题** [2149.按符号重排数组](https://leetcode-cn.com/problems/rearrange-array-elements-by-sign/)，甚至比很多第一题还简单，是相对比较简单的第二题了，直接拆成两个，不做原地操作就还是很简单的；

**279场周赛第1题** [2164.对奇偶下标分别排序](https://leetcode-cn.com/problems/sort-even-and-odd-indices-independently/)，这个题虽然是水题，但是可以学习python奇数偶数 **下标位置** 取值方法，`nums[::2]`取偶数，`nums[1:;2]`取奇数；

**281场周赛第1题** [2180.统计各位数字之和为偶数的整数个数](https://leetcode-cn.com/problems/count-integers-with-even-digit-sum/)，水题，写个judge函数搞过去就可以了；

**282场周赛第1题** [2185.统计包含给定前缀的字符串](https://leetcode-cn.com/problems/counting-words-with-a-given-prefix/)，python有 `str.startswith()` 函数，用这个就可以了；