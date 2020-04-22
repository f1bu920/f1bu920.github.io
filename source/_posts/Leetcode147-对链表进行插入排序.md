---
title: Leetcode147.对链表进行插入排序
date: 2020-03-29 12:30:36
categories: Leetcode
tags:
  - 链表
  - 排序
---

## Leetcode147.对链表进行插入排序

对链表进行插入排序。


插入排序的动画演示如上。从第一个元素开始，该链表可以被认为已经部分排序（用黑色表示）。
每次迭代时，从输入数据中移除一个元素（用红色表示），并原地将其插入到已排好序的链表中。

插入排序算法：

插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。
每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。
重复直到所有输入数据插入完为止。

[题目链接](https://leetcode-cn.com/problems/insertion-sort-list)

<!--more-->

示例 1：

> 输入: 4->2->1->3
> 输出: 1->2->3->4

示例 2：

> 输入: -1->5->3->4->0
> 输出: -1->0->3->4->5

#### 思路

创建一个虚拟头结点`dummyHead`，令`pre`指向`head`，`cur`指向`head.next`，若遇到`pre.val>cur.val`时，从`dummyHead`开始遍历插入。



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
    public ListNode insertionSortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode dummyHead = new ListNode(-1);
        dummyHead.next = head;
        ListNode pre = head;
        ListNode cur = head.next;
        while (cur != null) {
            if (pre.val > cur.val) {
                //要从dummyHead开始才能遍历到head
                ListNode temp = dummyHead;
                while (temp.next.val < cur.val) {
                    temp = temp.next;
                }
                pre.next = cur.next;
                //插入
                cur.next = temp.next;
                temp.next = cur;
                
                cur = pre.next;
            } else {
                pre = pre.next;
                cur = cur.next;
            }
        }
        return dummyHead.next;
    }
}
```

