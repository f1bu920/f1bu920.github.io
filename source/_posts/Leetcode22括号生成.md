---
title: Leetcode22括号生成
date: 2020-04-09 09:47:50
categories: Leetcode
tags: 
  - 深度优先遍历
  - 递归
---

## Leetcode22.括号生成

给出 n 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且有效的括号组合。

例如，给出 n = 3，生成结果为：

[题目链接](https://leetcode-cn.com/problems/generate-parentheses)

<!--more-->

[参考链接](https://leetcode-cn.com/problems/generate-parentheses/solution/hui-su-suan-fa-by-liweiwei1419/ )

> [
>   "((()))",
>   "(()())",
>   "(())()",
>   "()(())",
>   "()()()"
> ]

#### 思路

深度优先遍历，采用递归的形式，所以可以使用系统栈，不需要自己定义栈。

![题解配图](https://f1bu920.github.io/images/LeetCode22括号生出题解配图.png)

如上图所示：

1. 左括号数大于右括号数时，需要剪枝，此情况一定不合法
2. 左括号数和右括号数都为0时，添加到结果集中
3. `left>0`时，递归调用；`right>0`时递归。



#### 代码实现

```java
class Solution {
    List<String> list = new ArrayList<>();

    public List<String> generateParenthesis(int n) {
        StringBuilder sb = new StringBuilder();
        String str = new String();
        dfs(str, n, n);
        return list;
    }

    private void dfs(String sb, int left, int right) {
        //左括号数大于右括号数时减枝
        if (left > right) {
            return;
        }
        //加入结果集中
        if (left == 0 && right == 0) {
            list.add(sb);
            return;
        }
        if (left > 0) {
            dfs(sb + '(', left - 1, right);
        }
        if (right > 0) {
            dfs(sb + ')', left, right - 1);
        }
    }
}
```











