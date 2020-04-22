---
title: Leetcode876.链表的中间结点
date: 2020-03-23 11:30:52
categories: Leetcode
tags:
  - 链表
---

## Leetcode876.链表的中间结点

给定一个带有头结点 `head` 的非空单链表，返回链表的中间结点。

如果有两个中间结点，则返回第二个中间结点。

 [题目链接](https://leetcode-cn.com/problems/middle-of-the-linked-list/ )

<!--more-->

**示例 1：**

```
输入：[1,2,3,4,5]
输出：此列表中的结点 3 (序列化形式：[3,4,5])
返回的结点值为 3 。 (测评系统对该结点序列化表述是 [3,4,5])。
注意，我们返回了一个 ListNode 类型的对象 ans，这样：
ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.
```

**示例 2：**

```
输入：[1,2,3,4,5,6]
输出：此列表中的结点 4 (序列化形式：[4,5,6])
由于该列表有两个中间结点，值分别为 3 和 4，我们返回第二个结点。
```

- 思路

  使用快慢指针，初始时快慢指针皆指向虚拟头结点，慢指针一次走一步，快指针一次走两步，当快指针走到链表尾时，慢指针正好走到中间结点或两个中间结点的前者。

  最后，如果链表元素个数为偶数个时，中间结点有两个，题目要求指向后一个，所以需要加上判断：若快指针不为空时，此时为偶数个元素的链表，慢指针移动一位即可。

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
      public ListNode middleNode(ListNode head) {
          if (head == null || head.next == null) {
              return head;
          }
          ListNode slow = new ListNode(-1);
          slow.next = head;
          ListNode fast = slow;
          while (fast != null && fast.next != null) {
              slow = slow.next;
              fast = fast.next.next;
          }
          if (fast != null) {
              slow = slow.next;
          }
          return slow;
      }
  }
  ```

  