---
title: Leetcode82.删除排序链表中的重复元素II
date: 2020-03-22 15:38:37
categories: Leetcode
tags:
  - 链表
---

## Leetcode82.删除排序链表中的重复元素II

给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 *没有重复出现* 的数字。

[题目链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/ )

<!--more-->

**示例 1:**

```
输入: 1->2->3->3->4->4->5
输出: 1->2->5
```

**示例 2:**

```
输入: 1->1->1->2->3
输出: 2->3
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
          ListNode dummyHead = new ListNode(-1);
          dummyHead.next = head;
          ListNode cur = dummyHead;
          while (cur.next != null && cur.next.next != null) {
              if (cur.next.val == cur.next.next.val) {
                  //temp为局部重复的第一个元素
                  ListNode temp = cur.next;
                  //通过while循环找到局部重复的最后一个元素
                  while (temp != null && temp.next != null && temp.val == temp.next.val) {
                      temp = temp.next;
                  }
                  cur.next = temp.next;
              } else {
                  cur = cur.next;
              }
          }
          return dummyHead.next;
      }
  }
  ```

  