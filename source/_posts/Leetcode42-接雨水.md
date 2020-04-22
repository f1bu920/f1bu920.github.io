---
title: Leetcode42.接雨水
date: 2020-04-12 13:41:18
categories: Leetcode
tags:
  - 动态规划
  - 双指针
  - Stack
---

## Leetcode42.接雨水

给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

![rainwatertrap](https://f1bu920.github.io/images/rainwatertrap.png)

上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 

[题目链接](https://leetcode-cn.com/problems/trapping-rain-water)

<!--more-->

示例:

>输入: [0,1,0,2,1,0,1,3,2,1,2,1]
>输出: 6



#### 思路

- 暴力法

  对于数组中的每个元素，我们找到下雨水后能达到的最高位置等于两边最大高度的较小值减去其本身的高度。

- 动态规划

  我们使用`max_left[i]`存储`i`左边的最大高度，用`max_right[i]`存储`i`右边的最大高度，**注意不包括`i`本身**。最后，将`min(max_left,max_right)-height[i]`累加到结果上。

- 双指针与栈

  [参考链接](https://leetcode-cn.com/problems/trapping-rain-water/solution/jie-yu-shui-by-leetcode/ )

#### 代码实现

- 暴力法

  ```java
  class Solution {
      public int trap(int[] height) {
          int res = 0;
          int len = height.length;
          for (int i = 1; i < len - 1; i++) {
              int max_left = 0;
              int max_right = 0;
              //往左搜索，找到左边的最大高度
              for (int j = i; j >= 0; j--) {
                  max_left = Math.max(max_left, height[j]);
              }
              //往右搜索，找到右边的最大高度
              for (int j = i; j < len; j++) {
                  max_right = Math.max(max_right, height[j]);
              }
              res += Math.min(max_left, max_right) - height[i];
          }
          return res;
      }
  }
  ```

- 动态规划

  ```java
  class Solution {
      public int trap(int[] height) {
          int res = 0;
          int len = height.length;
          int[] max_left = new int[len];
          int[] max_right = new int[len];
          //存储左边的最大高度，不包括本身
          for (int i = 1; i < len - 1; i++) {
              max_left[i] = Math.max(max_left[i - 1], height[i - 1]);
          }
          //存储右边的最大高度，不包括本身
          for (int i = len - 2; i > 0; i--) {
              max_right[i] = Math.max(max_right[i + 1], height[i + 1]);
          }
          for (int i = 1; i < len - 1; i++) {
              int min = Math.min(max_left[i], max_right[i]);
              //因为不包括本身，所以要判断
              if (min > height[i]) {
                  res += min - height[i];
              }
          }
          return res;
      }
  }
  ```

