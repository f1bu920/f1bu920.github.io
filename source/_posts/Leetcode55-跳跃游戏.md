---
title: Leetcode55.跳跃游戏
date: 2020-04-17 08:44:56
categoties: Leetcode
tags:
  - 贪心
  - Array
---

## Leetcode55.跳跃游戏

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。

[题目链接](https://leetcode-cn.com/problems/jump-game)

<!--more-->

示例 1:

>输入: [2,3,1,1,4]
>输出: true
>解释: 我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。

示例 2:

>输入: [3,2,1,0,4]
>输出: false
>解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。



#### 思路

对于数组中的任意一个位置，如果它本身可以到达，那么它跳跃的最大长度就一定可以到达，`i + nums[i]>=y`，那么位置y也可以到达。

遍历整个数组中的每一个位置，记录最远可以到达的位置`farmost`，如果`farmost>=n-1`，则一定可以到达最后一个位置，返回`true`；遍历结束后返回`false`。



#### 代码实现

```java
class Solution {
    public boolean canJump(int[] nums) {
        int n = nums.length;
        int farmost = 0;
        for (int i = 0; i < n; i++) {
            if (i <= farmost) {
                farmost = Math.max(farmost, i + nums[i]);
                if (farmost >= n - 1) {
                    return true;
                }
            }
        }
        return false;
    }
}
```



