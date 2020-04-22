---
title: Leetcode面试题40.最小的k个数
date: 2020-03-20 11:30:50
categories: Leetcode
tags:
  - 排序
---

## Leetcode面试题40.最小的k个数

输入整数数组 `arr` ，找出其中最小的 `k` 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

 [题目链接](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/ )

<!--more-->

**示例 1：**

```
输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
```

**示例 2：**

```
输入：arr = [0,1,2,1], k = 1
输出：[0]
```

**限制：**

> `0 <= k <= arr.length <= 10000`

> `0 <= arr[i] <= 10000`

- 思路

  排序后取数组的前k个元素

- 代码实现

  ```java
  class Solution {
      public int[] getLeastNumbers(int[] arr, int k) {
          if (arr == null || k <= 0) {
              return new int[]{};
          }
          if (k >= arr.length) {
              return arr;
          }
          Arrays.sort(arr);
          return Arrays.copyOfRange(arr, 0, k);
      }
  }
  ```

  