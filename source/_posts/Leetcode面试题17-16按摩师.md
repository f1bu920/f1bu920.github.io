---
title: Leetcode面试题17.16按摩师
date: 2020-03-24 15:08:59
categories: Leetcode
tags:
  - 动态规划
---

## Leetcode面试题17.16按摩师

一个有名的按摩师会收到源源不断的预约请求，每个预约都可以选择接或不接。在每次预约服务之间要有休息时间，因此她不能接受相邻的预约。给定一个预约请求序列，替按摩师找到最优的预约集合（总预约时间最长），返回总的分钟数。

注意：本题相对原题稍作改动

 [题目链接](https://leetcode-cn.com/problems/the-masseuse-lcci/ )

<!--more-->

示例 1：

> 输入： [1,2,3,1]
>
> 输出： 4
> 解释： 选择 1 号预约和 3 号预约，总时长 = 1 + 3 = 4。

示例 2：

> 输入： [2,7,9,3,1]
>
> 输出： 12
> 解释： 选择 1 号预约、 3 号预约和 5 号预约，总时长 = 2 + 9 + 1 = 12。

示例 3：

> 输入： [2,1,4,5,3,1,1,3]
>
> 输出： 12
> 解释： 选择 1 号预约、 3 号预约、 5 号预约和 8 号预约，总时长 = 2 + 4 + 3 + 3 = 12。

#### 思路

此类动态规划题都可先找出所有情况的转移状态。此题中令`dp[i][0]`表示前`i`个预约，第`i`个预约不接的最长时间；令`dp[i][1]`表示前`i`个预约，第`i`个预约接的最长时间。

因为不能接受相邻的预约，所以可以得出状态转移方程如下：

1. `dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1])`
2. `dp[i][1]=nums[i]+dp[i-1][0]`

最终，`dp[nums.length-1][1]`或者`dp[nums.length-1][0]`中的最大者就是最长时间。

#### 代码实现

```java
class Solution {
    public int massage(int[] nums) {
        if(nums.length==0||nums==null){
            return 0;
        }
        int[][] dp = new int[nums.length][2];
        dp[0][0] = 0;
        dp[0][1] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1]);
            dp[i][1] = nums[i] + dp[i - 1][0];
        }
        return Math.max(dp[nums.length - 1][0], dp[nums.length - 1][1]);
    }
}
```



