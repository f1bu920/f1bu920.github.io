---
title: Leetcode面试题06.从尾到头打印链表
date: 2020-04-30 09:54:50
categories: Leetcode
tags:
  - 链表
  - 栈
---

## Leetcode面试题06.从尾到头打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

 [题目链接](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof)

<!--more-->

示例 1：

>输入：head = [1,3,2]
>输出：[2,3,1]


限制：

>0 <= 链表长度 <= 10000

### 思路

利用栈存储链表中的每个节点就能实现逆序返回每个节点。或者直接反转链表。



### 代码实现

```java
class Solution {
    public int[] reversePrint(ListNode head) {
        Stack<Integer> stack = new Stack<>();
        while (head != null) {
            stack.add(head.val);
            head = head.next;
        }
        int[] res = new int[stack.size()];
        int i = 0;
        while (!stack.isEmpty()) {
            res[i++] = stack.pop();
        }
        return res;
    }
}
```

