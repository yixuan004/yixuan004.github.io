---
title: LeetCode2022春季战队赛
date: 2022-04-23 19:47:54
tags:
    - 
    - EASY
    - MEDIUM
categories:
	- LeetCode-python
	- LeetCode周赛
---

比赛综述&总结：

这个春季战队赛还是挺难的，第一题上来就是读题困难；第二题只能想到很暴力的思路了，感觉第二题和笔试题还是挺像的，这种时间超出的没想到有一些dp操作优化感觉还是太久没做类似的题目了；第三题hard的dp，现在自己的实力差距还是有些大了，有时间还是想补一下这个题的；后面几个题貌似难度有点过大了，

另外赛制的话这个看起来就像函数版的ACM赛制一样，不能面向错误编程了，只告诉一个超出时间限制还是有难度的

<!--more-->

# 第一题：LCP 55.采集果实

[题目链接](https://leetcode-cn.com/problems/PTXy4P/)

## 题目大意

欢迎各位勇者来到力扣新手村，本次训练内容为「采集果实」。

在新手村中，各位勇者需要采集一些果实来制作药剂。`time[i]` 表示勇者每次采集 `1～limit` 颗第 `i` 种类型的果实需要的时间（即每次最多可以采集 `limit` 颗果实）。

当前勇者需要完成「采集若干批果实」的任务， `fruits[j] = [type, num]` 表示第 `j` 批需要采集 `num` 颗 `type` 类型的果实。采集规则如下：
- 按 `fruits` 给定的顺序**依次**采集每一批次
- 采集完当前批次的果实才能开始采集下一批次
- 勇者完成当前批次的采集后将**清空背包**（即多余的果实将清空）

请计算并返回勇者完成采集任务最少需要的时间。

示例1：
```
输入：time = [2,3,2], fruits = [[0,2],[1,4],[2,1]], limit = 3

输出：10

解释：
由于单次最多采集 3 颗
第 0 批需要采集 2 颗第 0 类型果实，需要采集 1 次，耗时为 2*1=2
第 1 批需要采集 4 颗第 1 类型果实，需要采集 2 次，耗时为 3*2=6
第 2 批需要采集 1 颗第 2 类型果实，需要采集 1 次，耗时为 2*1=2
返回总耗时 2+6+2=10
```

示例2：
```
输入：time = [1], fruits = [[0,3],[0,5]], limit = 2

输出：5

解释：
由于单次最多采集 2 颗
第 0 批需要采集 3 颗第 0 类型果实，需要采集 2 次，耗时为 1*2=2
第 1 批需要采集 5 颗第 0 类型果实，需要采集 3 次，耗时为 1*3=3
需按照顺序依次采集，返回 2+3=5
```

提示：
- 1 <= time.length <= 100
- 1 <= time[i] <= 100
- 1 <= fruits.length <= 10^3
- 0 <= fruits[i][0] < time.length
- 1 <= fruits[i][1] < 10^3
- 1 <= limit <= 100

## 分析和解答

这个题一上来就让人读题困难了，简单概括来说就是一个套角标，然后看看是否能整除，除完了向上取整吧

```python
class Solution(object):
    def getMinimumTime(self, time, fruits, limit):
        """
        :type time: List[int]
        :type fruits: List[List[int]]
        :type limit: int
        :rtype: int
        """
        res = 0
        for i in range(len(fruits)):
            if fruits[i][1] % limit != 0:
                res += ((fruits[i][1]//limit) + 1) * time[fruits[i][0]]
            else:
                 res += (fruits[i][1]//limit) * time[fruits[i][0]]
        return res
```


# 第二题：LCP 56.信物传送

[题目链接](https://leetcode-cn.com/problems/6UEx57/)

## 题目大意

欢迎各位勇者来到力扣城，本次试炼主题为「信物传送」。

本次试炼场地设有若干传送带，`matrix[i][j]` 表示第 `i` 行 `j` 列的传送带运作方向，`"^","v","<",">"` 这四种符号分别表示 **上**、**下**、**左**、**右** 四个方向。信物会随传送带的方向移动。勇者**每一次**施法操作，可**临时**变更一处传送带的方向，在物品经过后传送带恢复原方向。

![](/images/2022-04-23-20-10-49.png)

通关信物初始位于坐标 `start` 处，勇者需要将其移动到坐标 `end` 处，请返回勇者施法操作的最少次数。

注意：
- `start` 和 `end` 的格式均为 `[i,j]`

示例1：
```
输入：matrix = [">>v","v^<","<><"], start = [0,1], end = [2,0]

输出：1

解释：
如上图所示
当信物移动到 [1,1] 时，勇者施法一次将 [1,1] 的传送方向 ^ 从变更为 <
从而信物移动到 [1,0]，后续到达 end 位置
因此勇者最少需要施法操作 1 次
```

示例2：
```
输入：matrix = [">>v",">>v","^<<"], start = [0,0], end = [1,1]

输出：0

解释：勇者无需施法，信物将自动传送至 end 位置
```

示例3：
```
输入：matrix = [">^^>","<^v>","^v^<"], start = [0,0], end = [1,3]

输出：3
```

提示：
- `matrix` 中仅包含 `'^'、'v'、'<'、'>'`
- `0 < matrix.length <= 100`
- `0 < matrix[i].length <= 100`
- `0 <= start[0],end[0] < matrix.length`
- `0 <= start[1],end[1] < matrix[i].length`

## 分析和解答

这个题感觉有平常周赛里相对难一点第三题的感觉，比赛的时候没有想到太好的做法就直接超级暴力了，赛后看只过了2/32个测试用例，这也是这个比赛没法面向答案编程的难度吧hhhh

实际上要用bfs+dp的做法来做，当时能想到bfs，但是还是忽略和dp结合到一起了

这个题不需要记录vis了，就是说只把更小的入队，也好像不能纯优先队列，因为有些地方有好几个方向能走到，另外不用优先队列也可以做，但是用优先队列好像耗时会快一些

优先队列方法：
```python
def conveyorBelt(self, matrix, start, end):
		"""
		:type matrix: List[str]
		:type start: List[int]
		:type end: List[int]
		:rtype: int
		"""
		dx = [1, 0, -1, 0]
		dy = [0, 1, 0, -1]

		# 二维dp，代表从每个点到x y位置处的最小消耗
		dp = [[1e9 for _ in range(len(matrix[0]))] for _ in range(len(matrix))]

		# 状态初始化
		sx, sy = start
		import heapq
		pq = []
		heapq.heappush(pq, (0, [sx, sy]))  # 优先队列
		dp[sx][sy] = 0
		
		while len(pq) != 0:
			tmp = heapq.heappop(pq)
			x, y = tmp[1]
			for i in range(4):
				nx = x + dx[i]
				ny = y + dy[i]
				if nx >= 0 and nx < len(matrix) and ny >= 0 and ny < len(matrix[0]):
					# 这里不用设置visited数组是关键
					if (matrix[x][y] == '^' and i == 2) or (matrix[x][y] == 'v' and i == 0) or (matrix[x][y] == '<' and i == 3) or (matrix[x][y] == '>' and i == 1):
						if dp[x][y] < dp[nx][ny]:
							# 只有更小的才入队，貌似不是优先队列也行
							dp[nx][ny] = dp[x][y]
							heapq.heappush(pq, (tmp[0], [nx, ny]))
					else:
						if dp[x][y] + 1 < dp[nx][ny]:
							# 只有更小的才入队，貌似不是优先队列也行
							dp[nx][ny] = dp[x][y] + 1
							heapq.heappush(pq, (tmp[0]+1, [nx, ny]))
					
		return dp[end[0]][end[1]]
```

非优先队列方法：
```python
def conveyorBelt(self, matrix, start, end):
			"""
			:type matrix: List[str]
			:type start: List[int]
			:type end: List[int]
			:rtype: int
			"""
			dx = [1, 0, -1, 0]
			dy = [0, 1, 0, -1]

			# 二维dp，代表从每个点到x y位置处的最小消耗
			dp = [[1e9 for _ in range(len(matrix[0]))] for _ in range(len(matrix))]

			# 状态初始化
			sx, sy = start
			queue = [[sx, sy]]
			dp[sx][sy] = 0
			
			while len(queue) != 0:
				x, y = queue[0]
				queue = queue[1:]
				for i in range(4):
					nx = x + dx[i]
					ny = y + dy[i]
					if nx >= 0 and nx < len(matrix) and ny >= 0 and ny < len(matrix[0]):
						# 这里不用设置visited数组是关键
						if (matrix[x][y] == '^' and i == 2) or (matrix[x][y] == 'v' and i == 0) or (matrix[x][y] == '<' and i == 3) or (matrix[x][y] == '>' and i == 1):
							if dp[x][y] < dp[nx][ny]:
								# 只有更小的才入队，貌似不是优先队列也行
								dp[nx][ny] = dp[x][y]
								queue.append([nx, ny])
						else:
							if dp[x][y] + 1 < dp[nx][ny]:
								# 只有更小的才入队，貌似不是优先队列也行
								dp[nx][ny] = dp[x][y] + 1
								queue.append([nx, ny])
						
			return dp[end[0]][end[1]]
```

比赛时候的超时做法：
```python
class Solution(object):
	def conveyorBelt(self, matrix, start, end):
		"""
		:type matrix: List[str]
		:type start: List[int]
		:type end: List[int]
		:rtype: int
		"""
		dx = [1, 0, -1, 0]
		dy = [0, 1, 0, -1]

		record = [[[-1, 0] for _ in range(len(matrix[0]))] for _ in range(len(matrix))]
		visited = [[0 for _ in range(len(matrix[0]))] for _ in range(len(matrix))]
		total_vis = 0
		target_vis = len(matrix[0]) * len(matrix)

		# 第一次从start开始走，把所有能走到的位置都标记上，如果走到了end则跳出并且输出0
		# x纵向，y横向
		sx, sy = start
		nx, ny = sx, sy
		
		while visited[nx][ny] == 0 and nx >= 0 and nx < len(matrix) and ny >= 0 and ny < len(matrix[0]):
			# 如果走到了end则跳出，并且输出0
			if nx == end[0] and ny == end[1]:
				return 0

			# 标记访问过的
			visited[nx][ny] = 1
			total_vis += 1

			# 修改状态
			record[nx][ny][0] = 0
			record[nx][ny][1] = 1
			
			# 按照当前的格子走
			if matrix[nx][ny] == '>':
				ny = ny + 1
			elif matrix[nx][ny] == '<':
				ny = ny - 1
			elif matrix[nx][ny] == '^':
				nx = nx - 1
			elif matrix[nx][ny] == 'v':
				nx = nx + 1

		# print("total_vis: ", total_vis)
		# print("target_vis: ", target_vis)
		# print("record: ", record)

		# 暴力走
		now_status = 0
		jilu_i = 0
		jilu_j = 0
		while total_vis != target_vis:
			# 找到一个与now_status相邻的位置
			break_flag = False
			for i in range(jilu_i, len(matrix)):
				for j in range(jilu_j, len(matrix[0])):
					can_flag = False
					if visited[i][j] == 0:  # 如果还没有访问过
						# 如果临近的可达状态有上一个now_status的
						for k in range(4):
							nnx = i + dx[k]
							nny = j + dy[k]
							if nnx >= 0 and nnx < len(matrix) and nny >= 0 and nny < len(matrix[0]) and record[nnx][nny][0] == now_status:  # 
								can_flag = True
								break

					if can_flag:
						break_flag = True
						break
				if break_flag:
					break

			if break_flag:  # 还能找到
				
				jilu_i = i
				jilu_j = j

				nx, ny = i, j
				while nx >= 0 and nx < len(matrix) and ny >= 0 and ny < len(matrix[0]) and visited[nx][ny] == 0:
					# 修改状态
					record[nx][ny][0] = now_status + 1
					record[nx][ny][1] = 1
					
					# 增加
					visited[i][j] = 1
					total_vis += 1

					# 按照当前的格子走
					if matrix[nx][ny] == '>':
						ny = ny + 1
					elif matrix[nx][ny] == '<':
						ny = ny - 1
					elif matrix[nx][ny] == '^':
						nx = nx - 1
					elif matrix[nx][ny] == 'v':
						nx = nx + 1

				# print(record)
				# print("=============")



			else:
				now_status += 1  # 再多一跳才能跳到的

		return record[end[0]][end[1]][0]
```