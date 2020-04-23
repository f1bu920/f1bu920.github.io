---
title: Leetcode面试题08.11.硬币
date: 2020-04-23 09:40:29
categories: Leetcode
tags:
  - 动态规划
---

## Leetcode面试题08.11.硬币

---
硬币。给定数量不限的硬币，币值为25分、10分、5分和1分，编写代码计算n分有几种表示法。(结果可能会很大，你需要将结果模上1000000007)

示例1:

 输入: n = 5
 输出：2
 解释: 有两种方式可以凑成总金额:
5=5
5=1+1+1+1+1
示例2:

 输入: n = 10
 输出：4
 解释: 有四种方式可以凑成总金额:
10=10
10=5+5
10=5+1+1+1+1+1
10=1+1+1+1+1+1+1+1+1+1
说明：

注意:

你可以假设：

0 <= n (总金额) <= 1000000
---

[题目链接](https://leetcode-cn.com/problems/coin-lcci)

<!--more-->

#### 思路

采用动态规划。令`dp[i][j]`表示遍历到当前硬币`i`时组成金额`j`的方法数目。有两个可能：

1. 金额`j<coins[i]`，当前这个硬币没有取，`dp[i][j]= dp[i-1][j]`；
2. 金额`j>=coins[i]`，当前这个硬币取了，`dp[i][j]=dp[i][j-coins[i]]`。

初始化时，当`i=0`时，取硬币面值1，`dp[0][i]=1`；

当金额为0时，`dp[i][0]=1`，全为1.

#### 代码实现

```java
class Solution {
    public int waysToChange(int n) {
        //dp表示遍历到当前硬币i时，组成金额j的方法数目
        int[][] dp = new int[4][n + 1];
        int[] coins = new int[]{1, 5, 10, 25};
        //初始化
        for (int i = 0; i <= n; i++) {
            dp[0][i] = 1;
        }
        for (int i = 0; i < 4; i++) {
            dp[i][0] = 1;
        }
        for (int i = 1; i < 4; i++) {
            for (int j = 1; j <= n; j++) {
                if (j < coins[i]) {
                    dp[i][j] = dp[i - 1][j];
                } else {
                    dp[i][j] = (dp[i - 1][j] + dp[i][j - coins[i]]) % 1000000007;
                }
            }
        }
        return dp[3][n];
    }
}
```

