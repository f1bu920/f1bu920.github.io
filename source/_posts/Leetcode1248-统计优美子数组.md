---
title: Leetcode1248.统计'优美子数组'
date: 2020-04-21 09:37:20
categories: Leetcode
tags:
  - Slide Windows
  - 双指针
---

## Leetcode1248.统计'优美子数组'

给你一个整数数组 nums 和一个整数 k。

如果某个 连续 子数组中恰好有 k 个奇数数字，我们就认为这个子数组是「优美子数组」。

请返回这个数组中「优美子数组」的数目。

 [题目链接](https://leetcode-cn.com/problems/count-number-of-nice-subarrays)

<!--more-->

示例 1：

>输入：nums = [1,1,2,1,1], k = 3
>输出：2
>解释：包含 3 个奇数的子数组是 [1,1,2,1] 和 [1,2,1,1] 。

示例 2：

>输入：nums = [2,4,6], k = 1
>输出：0
>解释：数列中不包含任何奇数，所以不存在优美子数组。

示例 3：

>输入：nums = [2,2,2,1,2,2,1,2,2,2], k = 2
>输出：16


提示：

>1 <= nums.length <= 50000
>1 <= nums[i] <= 10^5
>1 <= k <= nums.length



#### 思路

采用滑动窗口，只需要关注奇数的个数，找到每种情况下最短的符合要求的子数组，此时子数组的左右边界一定都是奇数。向左向右拓展，直到遇到奇数为止。

创建一个数组`array`记录原始数组中奇数下标，从`array`中下标为1处开始存储，左边界为-1，右边界为`nums.length`。

- 向左扩展，有`array[i]-array[i-1]`种可能；向右扩展，有`array[i+k]-array[i+k-1]`中可能。
- 所有一共有`(array[i]-array[i-1])*(array[i+k]-array[i+k-1])`种可能。
- 遍历整个下标数组。



#### 代码实现

```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        int res = 0;
        int n = nums.length;
        int index = 0;
        //存储奇数下标
        int[] array = new int[n + 2];
        for (int i = 0; i < n; i++) {
            if (nums[i] % 2 == 1) {
                array[++index] = i;
            }
        }
        //左边界
        array[0] = -1;
        //右边界
        array[index + 1] = n;
        //array数组的右边界下标为index+1，一共有index+2个元素
        for (int i = 1; i + k <= index + 1; i++) {
            //这里i+k是子数组的第k+1个元素
            res += (array[i] - array[i - 1]) * (array[i + k] - array[i + k - 1]);
        }
        return res;
    }
}
```



