---
title: Leetcode面试题62.圆圈中最后剩下的数字
date: 2020-03-30 10:26:08
categories: Leetcode
tags:
  - Math
  - 递归
---

## Leetcode面试题62.圆圈中最后剩下的数字

0,1,,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

[题目链接](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof)

<!--more-->

示例 1：

> 输入: n = 5, m = 3
> 输出: 3

示例 2：

> 输入: n = 10, m = 17
> 输出: 2

限制：

> 1 <= n <= 10^5
> 1 <= m <= 10^6

#### 思路

- 利用循环链表

- 数学+递归

  定义一个辅助函数`f(n,m)`，返回值为最终留下来的元素的序号。长度为`n`的序列会删除第`m%n`个元素，然后留下一个`n-1`的序列，所以可以递归调用`f(n-1,m)`求解。我们令`x=f(n-1,m)`，设下一轮的最后结点编号为 p，那么当前一轮的最后结点为从被删除结点向后偏移 p+1 处的结点 。换一个更好用代码实现的描述方式：从被删除结点的下一个结点偏移 p 处的结点，编号为 ((m%n) + p)%n。

  ![图示案例](https://f1bu920.github.io/images/捕获.PNG)

#### 代码实现

- 循环链表

  ```java
  class Solution {
      public int lastRemaining(int n, int m) {
          List<Integer> list = new ArrayList<>(n);
          for (int i = 0; i < n; i++) {
              list.add(i);
          }
          int x = 0;
          while (n > 1) {
              x = (m + x - 1) % n;
              list.remove(x);
              n--;
          }
          return list.get(0);
      }
  }
  ```

- 数学+递归

  ```java
      public int lastRemaining(int n, int m) {
          return f(n, m);
      }
  
      private int f(int n, int m) {
          if (n == 1) {
              return 0;
          }
          int x = f(n - 1, m);
          return (m % n + x) % n;
      }
  ```

  




