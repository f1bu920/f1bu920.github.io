---
title: Leetcode23.合并K个排序链表
date: 2020-04-26 08:39:58
categories: Leetcode
tags:
  - 链表
  - 优先级队列
  - 分治法
---

## Leetcode23.合并K个排序链表

合并 k 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。

[题目链接](https://leetcode-cn.com/problems/merge-k-sorted-lists)

<!--more-->

示例:

>输入:
>[
>  1->4->5,
>  1->3->4,
>  2->6
>]
>输出: 1->1->2->3->4->4->5->6

### 思路

- 顺序合并

  创建一个辅助函数，两两合并，第`i`次循环将第`i`个链表合并到`res`中。

- 优先级队列

  我们每次需要找到链表数组中未添加部分的头结点的最小值，可以使用优先级队列来实现。

### 代码实现

- 顺序合并

  ```java
  class Solution {
      public ListNode mergeKLists(ListNode[] lists) {
          ListNode res = null;
          for (int i = 0; i < lists.length; i++) {
              res = mergeTwoList(res, lists[i]);
          }
          return res;
      }
      //两两合并
      private ListNode mergeTwoList(ListNode headA, ListNode headB) {
          if (headA == null || headB == null) {
              return headA == null ? headB : headA;
          }
          ListNode curA = headA;
          ListNode curB = headB;
          ListNode dummyHead = new ListNode(-1);
          ListNode cur = dummyHead;
          while (curA != null && curB != null) {
              if (curA.val < curB.val) {
                  cur.next = curA;
                  curA = curA.next;
              } else {
                  cur.next = curB;
                  curB = curB.next;
              }
              cur = cur.next;
          }
          cur.next = curA == null ? curB : curA;
          return dummyHead.next;
      }
  }
  ```

- 优先级队列

  ```java
  class Solution {
      public ListNode mergeKLists(ListNode[] lists) {
          if (lists==null||lists.length==0){
              return null;
          }
          if(lists.length==1){
              return lists[0];
          }
          //注意这里使用了lambda表达式，也可以new一个Comparator接口对象，指定优先级队列出队优先级
          Queue<ListNode> queue = new PriorityQueue<>(lists.length,
                  (ListNode o1, ListNode o2) -> {
                      return o1.val - o2.val;
                  });
          for (ListNode node : lists) {
              //注意这里空节点不能入队
              if(node!=null){
                  queue.add(node);
              }
          }
          ListNode dummyHead = new ListNode(-1);
          ListNode res = dummyHead;
          while (!queue.isEmpty()) {
              res.next = queue.poll();
              res = res.next;
              if (res.next != null) {
                  queue.add(res.next);
              }
          }
          return dummyHead.next;
      }
  }
  ```