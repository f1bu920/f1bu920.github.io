---
title: Leetcode1019.链表中的下一个更大节点
date: 2020-04-28 17:18:09
categories: Leetcode
tags:
  - 链表
  - 栈
---

## Leetcode1019.链表中的下一个更大节点

给出一个以头节点 head 作为第一个节点的链表。链表中的节点分别编号为：node_1, node_2, node_3, ... 。

每个节点都可能有下一个更大值（next larger value）：对于 node_i，如果其 next_larger(node_i) 是 node_j.val，那么就有 j > i 且  node_j.val > node_i.val，而 j 是可能的选项中最小的那个。如果不存在这样的 j，那么下一个更大值为 0 。

返回整数答案数组 answer，其中 answer[i] = next_larger(node_{i+1}) 。

注意：在下面的示例中，诸如 [2,1,5] 这样的输入（不是输出）是链表的序列化表示，其头节点的值为 2，第二个节点值为 1，第三个节点值为 5 。

 [题目链接](https://leetcode-cn.com/problems/next-greater-node-in-linked-list)

<!--more-->

示例 1：

>输入：[2,1,5]
>输出：[5,5,0]

示例 2：

>输入：[2,7,4,3,5]
>输出：[7,0,5,5,0]

示例 3：

>输入：[1,7,5,1,9,2,5,1]
>输出：[7,9,9,9,0,5,0,0]


提示：

>对于链表中的每个节点，1 <= node.val <= 10^9
>给定列表的长度在 [0, 10000] 范围内

### 思路

- 暴力法

  对于每个当前节点，设一个`nextLargeNode`，找到下一个更大的值

- 栈

  利用栈`stack`存储下标，数组`arr`存储结果(下一个更大的值)，数组`valueArray`存储链表中的值。设置一个变量`index`存储下标。

  每次讲数组下标入栈，当找到了比栈顶元素更大的值时，出栈。

  一次遍历，时间复杂度为O(n)。

### 代码实现

- 暴力法

```java
class Solution {
    public int[] nextLargerNodes(ListNode head) {
        ListNode cur = head;
        int count = 0;
        //找到链表长度
        while (cur != null) {
            count++;
            cur = cur.next;
        }
        int[] answer = new int[count];
        int i = 0;
        cur = head;
        ListNode nextLargeNode;
        while (cur != null) {
            int maxValue = 0;
            nextLargeNode = cur.next;
            while (nextLargeNode != null) {
                if (nextLargeNode.val > cur.val) {
                    maxValue = Math.max(maxValue, nextLargeNode.val);
                    break;
                }
                nextLargeNode = nextLargeNode.next;
            }
            cur = cur.next;
            answer[i++] = maxValue;
        }
        //最后一个元素一定没有下一个比它更大的值，一定是0
        answer[--i] = 0;
        return answer;
    }
}
```

- 栈

```java
class Solution {
    public int[] nextLargerNodes(ListNode head) {
        ListNode cur = head;
        //存放结果
        int[] arr = new int[10000];
        //链表数据
        int[] valueArray = new int[10000];
        //栈存放下标
        Stack<Integer> stack = new Stack<>();
        int value;
        //下标
        int index = 0;
        while (cur != null) {
            value = cur.val;
            valueArray[index] = value;
            //找到了比栈顶元素更大的值，出栈
            while (!stack.isEmpty() && value > valueArray[stack.peek()]) {
                arr[stack.pop()] = value;
            }
            stack.add(index);
            index++;
            cur = cur.next;
        }
        int[] answer = new int[index];
        for (int i = 0; i < index; i++) {
            answer[i] = arr[i];
        }
        return answer;
    }
}
```