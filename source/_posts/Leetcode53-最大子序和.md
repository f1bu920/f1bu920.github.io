---
title: Leetcode53.最大子序和
date: 2020-05-04 13:08:34
categories: Leetcode
tags:
  - 贪心
  - 分治
---

## Leetcode53.最大子序和

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

[题目链接](https://leetcode-cn.com/problems/maximum-subarray)

<!--more-->

示例:

>输入: [-2,1,-3,4,-1,2,1,-5,4],
>输出: 6
>解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

进阶:

>如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的分治法求解。

### 思路

- 贪心

  令`sum`表示当前的子序列和，令`result`表示结果最大的子序列和。

  当`sum<0`时，丢弃。当`sum>=0`时，就和当前元素相加。

- 分治

  [参考链接](https://leetcode-cn.com/problems/maximum-subarray/solution/zui-da-zi-xu-he-by-leetcode-solution/ )

### 代码实现

- 贪心

```java
public class Solution {
    public int maxSubArray(int[] nums) {
        int sum = nums[0];
        int result = nums[0];
        for (int i = 1; i < nums.length; i++) {
            if (sum < 0) {
                sum = nums[i];
            } else {
                sum += nums[i];
            }
            result = Math.max(result, sum);
        }
        return result;
    }
}
```