---
title: Java-Leetcode刷题模板
date: 2022-12-31 17:13:45
tags:
	- javase
categories:
	- develop
	- java
	- javase
---

# Java-Leetcode刷题模板

主要是要把所有的写在一个文件里，然后想象成和python那个刷题板子差不多的流程

以翻转链表这个题为例：https://leetcode.cn/problems/reverse-linked-list/

```java
package cn.leetcode.codetop.jan05;

class ListNode {
    int val;
    ListNode next;


    ListNode() {

    }


    ListNode(int val, ListNode next) {
        this.val = val;
        this.next = next;
    }
}

class Solution206 {
    public ListNode solution(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
}


public class Main206ReverseList {
    public static void main(String[] args) {
        ListNode b = new ListNode(3, null);
        ListNode a = new ListNode(2, b);
        ListNode head = new ListNode(1, a);

        Solution206 S = new Solution206();
        ListNode res = S.solution(head);

        while (res != null) {
            System.out.println(res.val);
            res = res.next;
        }
    }
}
```