---
title: Leetcode面试题02.01.移除重复节点
date: 2020-03-20 16:30:37
categories: Leetcode
tags:
  - 链表
---

## Leetcode面试题02.01.移除重复节点

编写代码，移除未排序链表中的重复节点。保留最开始出现的节点。

[题目链接](https://leetcode-cn.com/problems/remove-duplicate-node-lcci/ )

<!--more-->

**示例1:**

```
 输入：[1, 2, 3, 3, 2, 1]
 输出：[1, 2, 3]
```

**示例2:**

```
 输入：[1, 1, 1, 1, 2]
 输出：[1, 2]
```

**提示：**

1. 链表长度在[0, 20000]范围内。
2. 链表元素在[0, 20000]范围内。

- 思路

  使用`HashMap`作为缓冲区存储出现过的值，若遇到的值已出现过，则删除。使用了虚拟头结点

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
      public ListNode removeDuplicateNodes(ListNode head) {
          if (head == null){
              return null;
          }
          //使用HashSet更好
          Map map = new HashMap();
          ListNode dummyHead = new ListNode(-1);
          dummyHead.next=head;
          ListNode prev = dummyHead;
          while (prev.next!=null){
              if (!map.containsKey(prev.next.val)){
                  map.put(prev.next.val,1);
                  prev = prev.next;
              }else {
                  prev.next = prev.next.next;
              }
          }
          return dummyHead.next;
  
      }
  }
  ```

  