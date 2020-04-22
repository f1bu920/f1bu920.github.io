---
title: Leetcode83.删除排序链表中的重复元素
date: 2020-03-22 13:57:36
categories: Leetcode
tags:
  - 链表
---

## Leetcode83.删除排序链表中的重复元素

给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

[题目链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/ )

<!--more-->

**示例 1:**

```
输入: 1->1->2
输出: 1->2
```

**示例 2:**

```
输入: 1->1->2->3->3
输出: 1->2->3
```

- 代码实现

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
      public ListNode deleteDuplicates(ListNode head) {
          if (head == null || head.next == null) {
              return head;
          }
          ListNode cur = head;
          while (cur != null && cur.next != null) {
              if (cur.val != cur.next.val) {
                  cur = cur.next;
              } else {
                  cur.next = cur.next.next;
              }
          }
          return head;
      }
  }
  ```

  