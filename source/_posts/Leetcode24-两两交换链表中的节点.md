---
title: Leetcode24.两两交换链表中的节点
date: 2020-03-28 16:05:12
categories: Leetcode
tags:
  - 链表
---

## Leetcode24.两两交换链表中的节点

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

 [题目链接](https://leetcode-cn.com/problems/swap-nodes-in-pairs)

<!--more-->

示例:

> 给定 1->2->3->4, 你应该返回 2->1->4->3.

#### 思路

1. `pre`和`cur`分别遍历奇节点和偶节点，`prevNode`指向前一个节点。

2. 交换节点:

   1. `prevNode.next = cur;`
   2. `pre.next = cur.next;`
   3. `cur.next = pre;`

3. 更新节点：`prevNode = pre;`，`head = pre.next; `

   

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
       public ListNode swapPairs(ListNode head) {
           ListNode dummyHead = new ListNode(-1);
           dummyHead.next = head;
           ListNode prevNode = dummyHead;
           while (head != null && head.next != null) {
               ListNode pre = head;
               ListNode cur = head.next;
               prevNode.next = cur;
               pre.next = cur.next;
               cur.next = pre;
   
               prevNode = pre;
               head = pre.next;
           }
           return dummyHead.next;
       }
   }
   ```

    