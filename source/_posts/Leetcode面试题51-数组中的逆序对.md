---
title: Leetcode面试题51.数组中的逆序对
date: 2020-04-24 10:37:56
categories: Leetcode
tags:
  - 归并排序
  - Array
  - 分治法
---

## Leetcode面试题51.数组中的逆序对

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

 [题目链接](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof)

<!--more-->

示例 1:

>输入: [7,5,6,4]
>输出: 5


限制：

>0 <= 数组长度 <= 50000

#### 思路

- 暴力法

  两层for循环遍历数组，设置一个计数器。时间复杂度为O(n^2)，超出时间限制。

- 归并排序

  - 分解：将数组分为两个子数组，直到两个子数组长度为1，长度为1的数组可以认为是排好序的。
  - 合并：将两个已经排好序的子序列合并，**合并过程中，分别使用两个指针`i`和`j`分别指向序列1和序列2，当`i`指向的值小于等于`j`指向的值时，不构成逆序对，此时对逆序对数量贡献为0；当`i`指向的值大于`j`指向的值时，贡献了`mid-i+1`个逆序对。**

  [参考链接](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/solution/shu-zu-zhong-de-ni-xu-dui-by-leetcode-solution/ )



#### 代码实现

- 暴力法

  ```java
  class Solution {
      public int reversePairs(int[] nums) {
          int res = 0;
          if (nums == null || nums.length == 0) {
              return 0;
          }
          for (int i = 0; i < nums.length-1; i++) {
              for (int j = i+1; j < nums.length; j++) {
                  if (nums[i]>nums[j]){
                      res++;
                  }
              }
          }
          return res;
      }
  }
  ```

  超出时间限制

- 归并排序

  ```java
  class Solution {
      public int reversePairs(int[] nums) {
          int len = nums.length;
          if (len < 2) {
              return 0;
          }
          int[] copy = new int[len];
          System.arraycopy(nums, 0, copy, 0, len);
          int[] temp = new int[len];
          return reversePairs(copy, 0, len - 1, temp);
      }
  	//nums[left...right]为左闭右闭的区间
      private int reversePairs(int[] nums, int left, int right, int[] temp) {
          //left==right，只有一个元素
          if (left == right) {
              return 0;
          }
          //这样写防止溢出
          int mid = left + (right - left) / 2;
          int leftPairs = reversePairs(nums, left, mid, temp);
          int rightPairs = reversePairs(nums, mid + 1, right, temp);
          int crossPairs = mergeAndCount(nums, left, mid, right, temp);
          return leftPairs + rightPairs + crossPairs;
      }
  	//归并、计数
      private int mergeAndCount(int[] nums, int left, int mid, int right, int[] temp) {
          for (int i = left; i <= right; i++) {
              temp[i] = nums[i];
          }
          int i = left;
          int j = mid + 1;
          int count = 0;
          for (int k = left; k <= right; k++) {
              if (i == mid + 1) {
                  nums[k] = temp[j];
                  j++;
              } else if (j == right + 1) {
                  nums[k] = temp[i];
                  i++;
              } else if (temp[i] <= temp[j]) {
                  nums[k] = temp[i];
                  i++;
              } else {
                  nums[k] = temp[j];
                  j++;
                  count += (mid - i + 1);
              }
          }
          return count;
      }
  }
  ```

  