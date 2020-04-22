---
title: Leetcode1111.有效括号的嵌套深度
date: 2020-04-01 11:22:32
categories: Leetcode
tags:
  - Stack
---

## Leetcode1111.有效括号的嵌套深度

有效括号字符串 仅由 "(" 和 ")" 构成，并符合下述几个条件之一：

空字符串
连接，可以记作 AB（A 与 B 连接），其中 A 和 B 都是有效括号字符串
嵌套，可以记作 (A)，其中 A 是有效括号字符串
类似地，我们可以定义任意有效括号字符串 s 的 嵌套深度 depth(S)：

s 为空时，depth("") = 0
s 为 A 与 B 连接时，depth(A + B) = max(depth(A), depth(B))，其中 A 和 B 都是有效括号字符串
s 为嵌套情况，depth("(" + A + ")") = 1 + depth(A)，其中 A 是有效括号字符串
例如：""，"()()"，和 "()(()())" 都是有效括号字符串，嵌套深度分别为 0，1，2，而 ")(" 和 "(()" 都不是有效括号字符串。

给你一个有效括号字符串 seq，将其分成两个不相交的子序列 A 和 B，且 A 和 B 满足有效括号字符串的定义（注意：A.length + B.length = seq.length）。

现在，你需要从中选出 任意 一组有效括号字符串 A 和 B，使 max(depth(A), depth(B)) 的可能取值最小。

返回长度为 seq.length 答案数组 answer ，选择 A 还是 B 的编码规则是：如果 seq[i] 是 A 的一部分，那么 answer[i] = 0。否则，answer[i] = 1。即便有多个满足要求的答案存在，你也只需返回 一个。

 [题目链接](https://leetcode-cn.com/problems/maximum-nesting-depth-of-two-valid-parentheses-strings)

<!--more-->

示例 1：

> 输入：seq = "(()())"
> 输出：[0,1,1,1,1,0]

示例 2：

> 输入：seq = "()(())()"
> 输出：[0,0,0,1,1,0,1,1]


提示：

> 1 <= text.size <= 10000

#### 思路

- 计算嵌套深度

  维护一个栈`stack`，从左到右遍历字符串中每一个字符。

  - 如果当前字符为`(`，压入栈中，此时这个`(`的嵌套深度为栈的高度；

  - 如果当前字符为`)`，此时这个`)`的嵌套深度为栈的高度，随后弹出一个`(`。

  - 例：

    ```java
    括号序列   ( ( ) ( ( ) ) ( ) )
    下标编号   0 1 2 3 4 5 6 7 8 9
    嵌套深度   1 2 2 2 3 3 2 2 2 1 
    ```

- 我们要使得`max(depth(A), depth(B))`值最小，只需要保证栈内一半括号属于`A`，一半括号属于`B`，就能保证拆分后最大嵌套深度最小。要实现这样的对半分配，只需要将奇数层的`( )`分给`A`，偶数层的`( )`分给`B`即可。

- 最终答案不止1种。



#### 代码实现

```java
class Solution {
    public int[] maxDepthAfterSplit(String seq) {
        int N = seq.length();
        int[] answers = new int[N];
        if (seq.length() == 0) {
            return answers;
        }
        int depth = 0;
        int i = 0;
        for (char c : seq.toCharArray()) {
            if (c == '(') {
                depth++;
                answers[i++] = depth % 2;
            } else {
                answers[i++] = depth % 2;
                depth--;
            }
        }
        return answers;
    }
}
```

