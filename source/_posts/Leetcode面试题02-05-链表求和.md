---
title: Leetcode面试题02.05.链表求和
date: 2020-03-22 17:43:59
categories: Leetcode
tags:
  - 链表
  - 队列
---

## Leetcode面试题02.05.链表求和

给定两个用链表表示的整数，每个节点包含一个数位。

这些数位是反向存放的，也就是个位排在链表首部。

编写函数对这两个整数求和，并用链表形式返回结果。

 [题目链接](https://leetcode-cn.com/problems/sum-lists-lcci/ )

<!--more-->

**示例：**

```
输入：(7 -> 1 -> 6) + (5 -> 9 -> 2)，即617 + 295
输出：2 -> 1 -> 9，即912
```

**进阶：**假设这些数位是正向存放的，请再做一遍。

**示例：**

```
输入：(6 -> 1 -> 7) + (2 -> 9 -> 5)，即617 + 295
输出：9 -> 1 -> 2，即912
```

#### 思路

- 将链表转化为整数，但会超出`int`类型范围，发生溢出。
- 因为两个链表中数位是反向存放的，可利用队列进行缓存。链表中先读的元素是低位，要先运算即先出，符合先进先出的特点，所以使用队列。
- 维护两个变量`num`和`carray`，分别表示当前的和与进位，若`num>=10`，则进1位。
- 使用尾插法构造节点
- 判断最后是否还有进位，若有则构造一个`val=1`的节点。

#### 代码实现

```java
 public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        Queue<ListNode> queue1 = new ArrayDeque<>();
        Queue<ListNode> queue2 = new ArrayDeque<>();
        ListNode head = new ListNode(-1);
        head.next = null;
        ListNode cur = head;
        while (l1 != null) {
            queue1.add(l1);
            l1 = l1.next;
        }
        while (l2 != null) {
            queue2.add(l2);
            l2 = l2.next;
        }
        int carry = 0;
        while (!queue1.isEmpty() || !queue2.isEmpty()) {
            int num = carry;
            if (!queue1.isEmpty()) {
                num += queue1.poll().val;
            }
            if (!queue2.isEmpty()) {
                num += queue2.poll().val;
            }
            if (num >= 10) {
                carry = 1;
                num %= 10;
            } else {
                carry = 0;
            }
            ListNode temp = new ListNode(num);
            cur.next = temp;
            cur = temp;
        }
        if (carry != 0) {
            ListNode temp = new ListNode(1);
            cur.next = temp;
            cur = temp;
        }
        return head.next;
    }
```

