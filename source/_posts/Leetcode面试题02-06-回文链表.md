---
title: Leetcode面试题02.06.回文链表
date: 2020-03-21 12:59:25
categories: Leetcode
tags:
  - 链表
---

## Leetcode面试题02.06.回文链表

编写一个函数，检查输入的链表是否是回文的。

 [题目链接](https://leetcode-cn.com/problems/palindrome-linked-list-lcci/ )

<!--more-->

**示例 1：**

```
输入： 1->2
输出： false 
```

**示例 2：**

```
输入： 1->2->2->1
输出： true 
```

**进阶：**
你能否用 O(n) 时间复杂度和 O(1) 空间复杂度解决此题？

- 思路

  使用快慢指针找到链表的中间节点，将后半部分链表反转，再逐个比较，因为回文链表必定以中间节点为中心对称。此时时间复杂度为O(n)，空间复杂度为O(1)。

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
      public boolean isPalindrome(ListNode head) {
          if (head == null || head.next == null) {
              return true;
          }
          //快慢指针
          ListNode slow = head.next;
          ListNode fast = slow.next;
          ListNode pre;
          while (fast != null && fast.next != null && fast.next.next != null) {
              slow = slow.next;
              fast = fast.next.next;
          }
          pre = null;
          //反转后半部分链表  
          while (slow != null) {
              ListNode temp = slow.next;
              slow.next = pre;
              pre = slow;
              slow = temp;
          }
          //判断是否回文
          while (pre != null && head != null) {
              if (pre.val != head.val) {
                  return false;
              }
              pre = pre.next;
              head = head.next;
          }
          return true;
      }
  }
  ```

  