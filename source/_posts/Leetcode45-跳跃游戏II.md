---
title: Leetcode45.跳跃游戏II
date: 2020-05-04 09:38:21
categories: Leetcode
tags:
  - 贪心
---

## Leetcode45.跳跃游戏II

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

你的目标是使用最少的跳跃次数到达数组的最后一个位置。

[题目链接](https://leetcode-cn.com/problems/jump-game-ii)

<!--more-->

示例:

>输入: [2,3,1,1,4]
>输出: 2
>解释: 跳到最后一个位置的最小跳跃数是 2。
>     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。

说明:

>假设你总是可以到达数组的最后一个位置。

### 思路

贪心的进行正向查找，每次找到可到达的最远位置，就可以在线性时间内找到最少的跳跃次数。

设置一个变量`maxPosition`记录下一次跳跃可以到达的最远位置，用`end`表示当前能到达的最大下标位置，记为边界。

到达边界时，跳跃次数加一。

从左到右遍历整个数组，我们不访问最后一个元素，因为访问最后一个元素之前，我们的`end`边界一定大于等于最后一个位置。因此，我们的循环结束条件为`i<nums.length-1`。

![Leetcode45-跳跃游戏II.png](https://f1bu920.github.io/images/Leetcode45-跳跃游戏II.png)



### 代码实现

```java
class Solution {
    public int jump(int[] nums) {
        int steps = 0;
        int len = nums.length;
        int maxPosition = 0;
        int end = 0;
        //注意我们不访问最后一个元素
        for (int i = 0; i < len - 1; i++) {
            //记录能到达的最远位置
            maxPosition = Math.max(maxPosition, i + nums[i]);
            if (i == end) {
                //维护下一次跳跃的边界
                end = maxPosition;
                steps++;
            }
        }
        return steps;
    }
}
```