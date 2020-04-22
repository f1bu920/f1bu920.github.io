---
title: Leetcode148.排序链表
date: 2020-04-03 15:29:45
categories: Leetcode
tags:
  - 链表
  - 归并排序
---

## Leetcode148.排序链表

在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序。

[题目链接](https://leetcode-cn.com/problems/sort-list)

<!--more-->

示例 1:

>输入: 4->2->1->3
>输出: 1->2->3->4

示例 2:

>输入: -1->5->3->4->0
>输出: -1->0->3->4->5

#### 思路

在`O(n logn)`时间复杂度和常数级空间复杂度下，所以我们可以使用归并排序。

归并排序有两种形式：递归与非递归，这里我使用的是递归写法。

1. 先用快慢指针找到链表的中点的前一个元素，在此基础上就相当于找到了两个子序列的头结点。
2. 递归调用
3. 归并



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
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode slow = head;
        //这里找的的是链表中间结点的前一个节点
        ListNode fast = head.next.next;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode rightHead = slow.next;
        slow.next = null;
        ListNode left = sortList(head);
        ListNode right = sortList(rightHead);
        return mergeListNode(left, right);
    }

    private ListNode mergeListNode(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode(-1);
        ListNode cur = dummyHead;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        cur.next = (l1 != null) ? l1 : l2;
        return dummyHead.next;
    }
}
```

