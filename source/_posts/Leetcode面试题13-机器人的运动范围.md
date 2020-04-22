---
title: Leetcode面试题13.机器人的运动范围
date: 2020-04-08 14:28:09
categories: Leetcode
tags:
  - 广度优先遍历
---

## Leetcode面试题13.机器人的运动范围

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

 [题目链接](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof)

<!--more-->

示例 1：

> 输入：m = 2, n = 3, k = 1
> 输出：3

示例 1：

>输入：m = 3, n = 1, k = 0
>输出：1

提示：

> 1 <= n,m <= 100
> 0 <= k <= 20

#### 思路

看完题目很明显要用广度优先搜索，题目中要用到行坐标和列坐标之和，所以我们利用一个辅助函数`help`求行坐标和列坐标，注意边界条件和防止死循环即可。

**注意：这题也是表格类题，可能会将二维坐标转化为一维，但此题不可。因为将二维坐标转化为一维的前提是列数小于等于行数，即`temp = x * m + y; x = temp / m; y = temp % m;`,当`y>m`时，`temp / m`就会大于`x`了。这也是我写的时候出错的地方！**所以，我们有一对二维坐标需要存储，但因为Java中没有提供`Pair`类的数据结构，我们可以自己定义，但简单起见，我这里定义了两个队列，分别存放横坐标与纵坐标。



#### 代码实现

```java
public class Solution {
    public int movingCount(int m, int n, int k) {
        int res = 1;
        int[][] table = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                table[i][j] = 0;
            }
        }
        //上、下、左、右四个方向向量
        int[][] directions = new int[][]{{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        Queue<Integer> queue1 = new LinkedList<>();
        Queue<Integer> queue2 = new LinkedList<>();
        queue1.add(0);
        queue2.add(0);
        table[0][0] = 2;
        while (!queue1.isEmpty() && !queue2.isEmpty()) {
            int x = queue1.poll();
            int y = queue2.poll();
            for (int[] direction : directions) {
                int newX = x + direction[0];
                int newY = y + direction[1];
                if (newX >= 0 && newX < m && newY >= 0 && newY < n && help(newX) + help(newY) <= k && table[newX][newY] != 2) {
                    queue1.add(newX);
                    queue2.add(newY);
                    table[newX][newY] = 2;
                    res++;
                }
            }
        }
        return res;
    }
    //自己定义的求横、纵坐标的辅助函数
    private int help(int n) {
        int x = n;
        int res = 0;
        while (x != 0) {
            res += x % 10;
            x /= 10;
        }
        return res;
    }
}
```



