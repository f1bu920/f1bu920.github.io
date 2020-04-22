---
title: Leetcode445.两数相加II
date: 2020-04-14 11:19:08
categories: Leetcode
tags:
  - 链表
  - Stack
---

## Leetcode445.两数相加II

给你两个 非空 链表来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。

进阶：

如果输入链表不能修改该如何处理？换句话说，你不能对列表中的节点进行翻转。

 [题目链接](https://leetcode-cn.com/problems/add-two-numbers-ii)

<!--more-->

示例：

>输入：(7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)
>输出：7 -> 8 -> 0 -> 7



#### 思路

- 将链表反转再相加

- 栈

  因为题中数位的顺序是逆序的，为了逆序处理所有数位，可以利用栈。

  将所有数字压入栈中，再依次取出相加，注意进位。

  代码略。

#### 代码实现

- 反转链表

  ```java
  class Solution {
      public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
          ListNode headA = reverseLinkedList(l1);
          ListNode headB = reverseLinkedList(l2);
          int carry = 0;
          ListNode dummyHead = new ListNode(-1);
          ListNode pre = dummyHead;
          while (headA != null && headB != null) {
              int result = headA.val + headB.val + carry;
              if (result >= 10) {
                  result %= 10;
                  carry = 1;
              } else {
                  carry = 0;
              }
              headA = headA.next;
              headB = headB.next;
              ListNode cur = new ListNode(result);
              pre.next = cur;
              pre = cur;
          }
          //1->null, 9->null的情况
          if (carry == 1 && headA == null && headB == null) {
              pre.next = new ListNode(1);
              pre.next.next = null;
          }
          if (headA != null) {
              pre.next = headA;
              while (headA != null) {
                  int result = headA.val + carry;
                  if (result >= 10) {
                      result %= 10;
                      carry = 1;
                  } else {
                      carry = 0;
                     
                  }
                  headA.val = result;
                  pre = headA;
                  headA = headA.next;
              }
              if (carry == 1) {
                  ListNode temp = new ListNode(1);
                  temp.next = null;
                  pre.next = temp;
              }
          }
          if (headB != null) {
              pre.next = headB;
              while (headB != null) {
                  int result = headB.val + carry;
                  if (result >= 10) {
                      result %= 10;
                      carry = 1;
                  } else {
                      carry = 0;
                  }
                  headB.val = result;
                  pre = headB;
                  headB = headB.next;
              }
              if (carry == 1) {
                  ListNode temp = new ListNode(1);
                  temp.next = null;
                  pre.next = temp;
              }
          }
          return reverseLinkedList(dummyHead.next);
      }
  
      //反转链表
      private ListNode reverseLinkedList(ListNode head) {
          if (head == null || head.next == null) {
              return head;
          }
          ListNode cur = head;
          ListNode pre = null;
          while (cur != null) {
              ListNode temp = cur.next;
              cur.next = pre;
              pre = cur;
              cur = temp;
          }
          return pre;
      }
  }
  ```

  