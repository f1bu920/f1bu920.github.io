---
title: Leetcode11.盛最多水的容器
date: 2020-04-18 09:56:45
categories: Leetcode
tags:
  - 双指针
  - Array
---

## Leetcode11.盛最多水的容器

给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。

 

![Leetcode11.盛最多水的容器.jpg](https://f1bu920.github.io/images/Leetcode11.盛最多水的容器.jpg)

图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

 [题目链接](https://leetcode-cn.com/problems/container-with-most-water)

<!--more-->

示例：

>输入：[1,8,6,2,5,4,8,3,7]
>输出：49



#### 思路

- 暴力法

  两次遍历整个数组，找到最大盛水容量

- 双指针法

  开始时令两个指针分别指向数组的开头和末尾，记录当前的容量。

  移动对于数组中数字较小的那个指针，这是因为**容纳的水量为两个指针指向数字的较小值*指针间的距离。**

  如果我们移动数字较大的那个指针，那么前者「两个指针指向的数字中较小值」不会增加，后者「指针之间的距离」会减小，那么这个乘积会减小。因此，我们移动数字较大的那个指针是不合理的。因此，我们移动 数字较小的那个指针。

  

#### 代码实现

1. 暴力法

   ```java
   class Solution {
       public int maxArea(int[] height) {
           int maxCapacity = 0;
           int capacity = 0;
           int n = height.length;
           for (int i = 0; i < n - 1; i++) {
               for (int j = i + 1; j < n; j++) {
                   capacity = Math.min(height[i], height[j]) * (j - i);
                   maxCapacity = Math.max(capacity, maxCapacity);
               }
           }
           return maxCapacity;
       }
   }
   ```

2. 双指针法

   ```java
   class Solution {
       public int maxArea(int[] height) {
           int maxCapacity = 0;
           int capacity = 0;
           int l = 0, r = height.length - 1;
           while (l < r) {
               capacity = Math.min(height[l], height[r]) * (r - l);
               maxCapacity = Math.max(capacity, maxCapacity);
               if (height[l] > height[r]) {
                   r--;
               } else {
                   l++;
               }
           }
           return maxCapacity;
       }
   }
   ```

   