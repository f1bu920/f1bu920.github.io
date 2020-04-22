---
title: Leetcode206.反转链表
date: 2020-03-27 17:29:53
categories: Leetcode
tags:
  - 链表
---

## Leetcode206.反转链表

反转一个单链表。

[题目链接](https://leetcode-cn.com/problems/reverse-linked-list)

<!--more-->

示例:

> 输入: 1->2->3->4->5->NULL
> 输出: 5->4->3->2->1->NULL

#### 代码实现

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode pre = null;
        ListNode cur = head;
        while (cur != null) {
            ListNode tempNode = cur.next;
            cur.next = pre;
            pre = cur;
            cur = tempNode;
        }
        return pre;
    }
}
```

