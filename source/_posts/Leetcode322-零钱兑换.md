---
title: Leetcode322.零钱兑换
date: 2020-03-18 14:32:31
categories: Leetcode
tags:
  - 递归
  - 动态规划
  - 记忆化搜索
---

## Leetcode322.零钱兑换

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 `-1`。

**示例 1:**

```
输入: coins = [1, 2, 5], amount = 11
输出: 3 
解释: 11 = 5 + 5 + 1
```

**示例 2:**

```
输入: coins = [2], amount = 3
输出: -1
```

**说明**:

> 你可以认为每种硬币的数量是无限的。

<!--more-->

[题目链接](https://leetcode-cn.com/problems/coin-change/ )

#### 思路

- 动态规划与记忆化搜索

  定义memory[i]为凑成i所需的最小硬币数，则`memory[i] = memory[i-coins[j]] + 1  `，其中`coins[j]`代表一个硬币面值，使用`memory`数组存储结果。

- 递归

  `findWay`方法中`amount`不断减去`coins[i]`，并用`count`记录次数，最终与结果`res`比较.

#### 代码实现

- 递归

  ```java
  class Solution {
      int res = Integer.MAX_VALUE;
      public int coinChange(int[] coins, int amount) {
          if(coins.length == 0){
              return -1;
          }
  
          findWay(coins,amount,0);
  
          // 如果没有任何一种硬币组合能组成总金额，返回 -1。
          if(res == Integer.MAX_VALUE){
              return -1;
          }
          return res;
      }
  
      public void findWay(int[] coins,int amount,int count){
          if(amount < 0){
              return;
          }
          if(amount == 0){
              res = Math.min(res,count);
          }
  
          for(int i = 0;i < coins.length;i++){
              findWay(coins,amount-coins[i],count+1);
          }
      }
  }
  ```

  

- 记忆化搜索(自上而下)

```java
public class Solution {
    //memory[n]表示n可被换取的最少硬币数，不能被换则为-1
    int[] memory;

    public int coinChange(int[] coins, int amount) {
        if (coins.length == 0) {
            return -1;
        }
        memory = new int[amount];
        return findWay(coins, amount);
    }
    //findWay返回可凑成amount所需的最小硬币数
    public int findWay(int[] coins, int amount) {
        if (amount<0){
            return -1;
        }
        if (amount==0){
            return 0;
        }
        //记忆化搜索，如果memory[amount]被赋过值，直接返回结果
        if (memory[amount-1]!=0){
            return memory[amount-1];
        }
        int min = Integer.MAX_VALUE;
        for (int i = 0;i<coins.length;i++){
            int res = findWay(coins,amount-coins[i]);
            if (res!=-1&&res<min){
                min = res+1;
            }
        }
        memory[amount-1] = (min==Integer.MAX_VALUE)?-1:min;
        return memory[amount-1];
    }
}
```

- 动态规划(自下而上)

  ```java
  public class Solution {
  
      public int coinChange(int[] coins, int amount) {
          int[] memory;
          
          if (coins.length == 0) {
              return -1;
          }
          //memory[n]表示凑成总金额为n需要的最少硬币数
          memory = new int[amount + 1];
          for (int i = 1; i <= amount; i++) {
              int min = Integer.MAX_VALUE;
              for (int j = 0; j < coins.length; j++) {
                  if (i - coins[j] >= 0 && memory[i - coins[j]] < min) {
                      min = memory[i - coins[j]] + 1;
                  }
              }
              //凑不成时memory[i]为Integer.MAX_VALUE
              memory[i] = min;
          }
          return memory[amount] == Integer.MAX_VALUE ? -1 : memory[amount];
      }
  }
  ```

  