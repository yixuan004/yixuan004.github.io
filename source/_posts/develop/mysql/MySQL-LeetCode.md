---
title: MySQL-LeetCode
date: 2022-12-08 17:50:07
tags:
	- mysql
categories:
	- develop
	- mysql
---

# MySQL-LeetCode

## 1. EASY

### EASY-175. 组合两个表

#### 问题

https://leetcode.cn/problems/combine-two-tables/

#### 解答

主要考察`LEFT  JOIN ... ON`的使用

```sql
SELECT Person.FirstName, Person.LastName, Address.City, Address.State
FROM Person LEFT JOIN Address
ON Person.PersonId = Address.PersonId
```

### EASY-181. 超过经理收入的员工

#### 问题

https://leetcode.cn/problems/employees-earning-more-than-their-managers/

#### 解答

主要考察

```sql
# Write your MySQL query statement below
SELECT A.name as Employee
FROM Employee A
WHERE A.managerId IS NOT null and A.salary > (
    SELECT max(B.salary)
    FROM Employee B
    WHERE A.managerId = B.id
)
```

### EASY-182. 查找重复的电子邮箱

#### 问题

https://leetcode.cn/problems/duplicate-emails/

#### 解答

先获取到一张表，然后再在这个表基础上查询

```sql
SELECT Email 
FROM (
    SELECT Person.Email, COUNT(Person.Email) as num
    FROM Person
    GROUP BY Person.Email
) as tmp
WHERE tmp.num > 1
```

### EASY-183. 从不订购的客户

#### 问题

https://leetcode.cn/problems/customers-who-never-order/

#### 解答

`NOT IN`的使用

```sql
# Write your MySQL query statement below
SELECT Customers.Name as Customers
FROM Customers
WHERE Customers.id NOT IN (
    SELECT Orders.customerId
    FROM Orders
)
```

###  EASY-196. 删除重复的电子邮箱

#### 问题

https://leetcode.cn/problems/delete-duplicate-emails/

#### 解答

复制两份，然后DELETE

```sql
# Please write a DELETE statement and DO NOT write a SELECT statement.
# Write your MySQL query statement below
DELETE A
FROM Person A, Person B
WHERE A.email = B.email and A.id > B.id
```

### EASY-197. 上升的温度

#### 问题

https://leetcode.cn/problems/rising-temperature/

#### 解答

复制两份，然后搞

```sql
# Write your MySQL query statement below
SELECT B.id
FROM Weather A, Weather B
WHERE DATEDIFF(B.recordDate, A.recordDate) = 1 and B.temperature > A.temperature
```

## 2. MEDIUM

### MEDIUM-176. 第二高的薪水

#### 问题

https://leetcode.cn/problems/second-highest-salary/

#### 解答

```sql
# Write your MySQL query statement below
SELECT max(Salary) as SecondHighestSalary
FROM Employee
WHERE Salary not in (
    SELECT max(Salary)
    FROM Employee
)
```

