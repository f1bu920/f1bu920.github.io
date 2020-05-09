---
title: Leetcode33.搜索旋转排序数组
date: 2020-04-27 10:27:45
categories: Leetcode
tags:
  - 二分搜索
---

## Leetcode33.搜索旋转排序数组

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 O(log n) 级别。

[题目链接](https://leetcode-cn.com/problems/search-in-rotated-sorted-array)

<!--more-->

示例 1:

>输入: nums = [4,5,6,7,0,1,2], target = 0
>输出: 4

示例 2:

>输入: nums = [4,5,6,7,0,1,2], target = 3
>输出: -1

### 思路

看到时间复杂度要求为O(log n)级别并且数组局部有序，就可以确定使用二分搜索。

因为局部有序，所以可以分为两类：

- `nums[left]<=nums[mid]`，前半部分有序。因此如果`nums[left]<=target<nums[mid]`，就在前半部分找，否则在后半部分找；**注意这里`nums[left]<=nums[mid]`一定要加等于号，这是因为只剩两个数时mid会指向第一个数。version2代码中使用的是先判断后半部分有序，此时不需加=号。**
- `nums[left]>nums[mid]`，后半部分有序，因此如果`nums[mid]<target<=nums[right]`，就在后半部分找，否则在前半部分找。

### 代码实现

1. version 1

   ```java
   class Solution {
       public int search(int[] nums, int target) {
           if (nums == null || nums.length == 0 || (nums.length == 1 && nums[0] != target)) {
               return -1;
           }
           //数组长度为1的情况
           if (nums[0] == target) {
               return 0;
           }
           int index = -1;
           //这里数组长度最少为2
           int i = 0, j = 1;
           while (j < nums.length && nums[j] > nums[i]) {
               j++;
               i++;
           }
           //数组为全局有序的情况
           if (j >= nums.length) {
               index = Arrays.binarySearch(nums, 0, nums.length, target);
               return index < 0 ? -1 : index;
           }
           //数组发生了旋转，找到乱序的边界，分为两个子部分,局部有序
           if (nums[0] <= target && nums[i] >= target) {
               index = Arrays.binarySearch(nums, 0, i + 1, target);
           } else if (nums[j] <= target && nums[nums.length - 1] >= target) {
               index = Arrays.binarySearch(nums, j, nums.length, target);
           }
           return index < 0 ? -1 : index;
       }
   }
   ```

   可以看到用了较多的`if-else`判断，代码很乱，而且是偷懒用了`Arrays`类的库函数。

2. **version 2**

   ```java
   class Solution {
       public int search(int[] nums, int target) {
           if (nums == null || nums.length == 0) {
               return -1;
           }
           int left = 0;
           int right = nums.length - 1;
           while (left <= right) {
               int mid = left + ((right - left) >> 1);
               if (nums[mid] == target) {
                   return mid;
               }
               //后半部分有序
               //这里注意，如果是用前半部分有序的话，条件应为nums[left]<=nums[mid]
               //这是因为当只有两个数时，mid会指向第一个数
               if (nums[mid] < nums[right]) {
                   //target在后半部分
                   if (nums[mid] < target && nums[right] >= target) {
                       left = mid + 1;
                   } else {
                       right = mid - 1;
                   }
               } else {
                   //target在前半部分
                   if (nums[mid] > target && nums[left] <= target) {
                       right = mid - 1;
                   } else {
                       left = mid + 1;
                   }
               }
           }
           return -1;
       }
   }
   ```

   