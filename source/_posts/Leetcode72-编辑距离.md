---
title: Leetcode72.编辑距离
date: 2020-04-10 13:43:54
categories: Leetcode
tags:
  - 动态规划
---

## Leetcode72.编辑距离

给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符

[题目链接](https://leetcode-cn.com/problems/edit-distance)

<!--more-->


示例 1：

>输入：word1 = "horse", word2 = "ros"
>输出：3
>解释：
>horse -> rorse (将 'h' 替换为 'r')
>rorse -> rose (删除 'r')
>rose -> ros (删除 'e')

示例 2：

>输入：word1 = "intention", word2 = "execution"
>输出：5
>解释：
>intention -> inention (删除 't')
>inention -> enention (将 'i' 替换为 'e')
>enention -> exention (将 'n' 替换为 'x')
>exention -> exection (将 'n' 替换为 'c')
>exection -> execution (插入 'u')



#### 思路

运用动态规划的思想，令`dp[i][j]`代表输入单词长度为`i`和`j`的编辑距离。当我们获得`dp[i-1][j]、dp[i][j-1]、dp[i-1][j-1]`的值后就可以计算出`dp[i][j]`。

`dp[i][j]`代表`word1`中`[0,i)`位置转换成`word2`中`[0,j)`位置所需的最小编辑距离。所以当`word1[i-1]=word2[j-1]`时，`dp[i][j] = dp[i-1][j-1]`。

如果不等`word1[i-1] =word2[j-1]`，我们要从三种变换方法中找到最小的一种。

- 插入

  对于插入操作，我们是在原有的基础上`dp[i][j-1]`增加一个操作，因为我们插入时是在`word1`的`i`的后面插入`word2[j-1]`，所以状态转移方程为`dp[i][j] = dp[i][j-1]+1`。

- 删除

  对于删除操作，我们是在原有基础上`dp[i-1][j]`上面加一，所以状态转移方程为`dp[i][j] = dp[i-1][j]+1`.

- 替换

  对于替换操作，我们是在`dp[i-1][j-1]`上加一，所以状态转移方程为`dp[i][j] = dp[i-1][j-1]+1`.

初始化：很明显`dp[0][j] = j`、`dp[i][0] = 1`。

#### 代码实现

  ```java
class Solution {
    public int minDistance(String word1, String word2) {
        int row = word1.length();
        int col = word2.length();
        //有一个为空的情况
        if (row * col == 0) {
            return row + col;
        }
        //dp[i][j]的意义是word1[0,i)变为word2[0,j)所需的最小编辑距离
        int[][] dp = new int[row + 1][col + 1];
        //初始化
        for (int i = 0; i < row + 1; i++) {
            dp[i][0] = i;
        }
        for (int i = 1; i < col + 1; i++) {
            dp[0][i] = i;
        }
        for (int i = 1; i < row + 1; i++) {
            for (int j = 1; j < col + 1; j++) {
                //替换
                int replace = dp[i - 1][j - 1];
                //删除
                int delete = dp[i - 1][j] + 1;
                //插入
                int insert = dp[i][j - 1] + 1;
                if (word1.charAt(i - 1) != word2.charAt(j - 1)) {
                    replace += 1;
                }
                dp[i][j] = Math.min(replace, Math.min(delete, insert));
            }
        }
        return dp[row][col];
    }
}
  ```

