---
title: Leeetcode542.01矩阵
date: 2020-04-28 10:28:12
categories: Leetcode
tags:
  - 广度优先遍历
---

## Leeetcode542.01矩阵

给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。

两个相邻元素间的距离为 1 。

[题目链接](https://leetcode-cn.com/problems/01-matrix)

<!--more-->

示例 1:
输入:

>0 0 0
>0 1 0
>0 0 0

输出:

>0 0 0
>0 1 0
>0 0 0

示例 2:
输入:

>0 0 0
>0 1 0
>1 1 1

输出:

>0 0 0
>0 1 0
>1 2 1

注意:

>给定矩阵的元素个数不超过 10000。
>给定矩阵中至少有一个元素是 0。
>矩阵中的元素只在四个方向上相邻: 上、下、左、右。

### 思路

这种最短距离问题很明显是用到了广度优先遍历。

遍历整个数组，将0入队，并将1置为-1，代表当前元素未被访问过。遍历当前元素的四周，如果周围点的值为-1，代表这个点是未被访问过的1，更新值为`matrix[x][y]+1`，并且将这个点入队。

### 代码实现

- 将数组中的0入队

```java
class Solution {
    public int[][] updateMatrix(int[][] matrix) {
        int row = matrix.length;
        int col = matrix[0].length;
        //方向向量
        int[][] directions = new int[][]{{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        Queue<int[]> queue = new LinkedList<>();
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (matrix[i][j] == 0) {
                    queue.add(new int[]{i, j});
                } else {
                    //-1表示当前元素未被访问
                    matrix[i][j] = -1;
                }
            }
        }
        while (!queue.isEmpty()) {
            int[] poll = queue.poll();
            int x = poll[0], y = poll[1];
            for (int i = 0; i < 4; i++) {
                int newX = x + directions[i][0];
                int newY = y + directions[i][1];
                //如果周围元素为-1，代表这里是未被访问的1，更新距离为matrix[x][y]+1
                if (newX >= 0 && newX < row && newY >= 0 && newY < col && matrix[newX][newY] == -1) {
                    matrix[newX][newY] = matrix[x][y] + 1;
                    queue.add(new int[]{newX, newY});
                }
            }
        }
        return matrix;
    }
}
```