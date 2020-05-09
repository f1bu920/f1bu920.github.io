---
title: Leetcode21.合并两个有序链表
date: 2020-05-04 10:30:15
categories: Leetcode
tags:
  - 链表
---

## Leetcode21.合并两个有序链表

将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

[题目链接](https://leetcode-cn.com/problems/merge-two-sorted-lists)

<!--more-->

示例：

输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4

### 思路

设置两个指针`headA`和`headB`分别指向两个有序链表的头结点，创建一个虚拟头结点`dummyHead`，使遍历节点`cur`指向虚拟头结点。

若`headA.val < headB.val`，则令`cur`指向`headA`，否则指向`headB`。若某条链表遍历结束，则令`cur`指向另一条链表剩余节点。

### 代码实现

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode headA = l1;
        ListNode headB = l2;
        ListNode dummyHead = new ListNode(-1);
        ListNode cur = dummyHead;
        while (headA != null && headB != null) {
            if (headA.val < headB.val) {
                cur.next = headA;
                headA = headA.next;
            } else {
                cur.next = headB;
                headB = headB.next;
            }
            cur = cur.next;
        }
        if (headA == null) {
            cur.next = headB;
        } else {
            cur.next = headA;
        }
        return dummyHead.next;
    }
}
```

