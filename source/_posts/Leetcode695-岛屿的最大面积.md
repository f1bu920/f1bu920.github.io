---
title: Leetcode695.岛屿的最大面积
date: 2020-03-19 14:04:54
categories: Leetcode
tags:
  - 递归
  - 深度优先搜索
---

## Leetcode695.岛屿的最大面积

给定一个包含了一些 `0` 和 `1` 的非空二维数组 `grid` 。

一个 **岛屿** 是由一些相邻的 `1` (代表土地) 构成的组合，这里的「相邻」要求两个 `1` 必须在水平或者竖直方向上相邻。你可以假设 `grid` 的四个边缘都被 `0`（代表水）包围着。

找到给定的二维数组中最大的岛屿面积。(如果没有岛屿，则返回面积为 `0` 。)

 [题目链接](https://leetcode-cn.com/problems/max-area-of-island/ )

<!--more-->

**示例 1:**

```
[[0,0,1,0,0,0,0,1,0,0,0,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 [0,1,0,0,1,1,0,0,1,0,1,0,0],
 [0,1,0,0,1,1,0,0,1,1,1,0,0],
 [0,0,0,0,0,0,0,0,0,0,1,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]
```

> 对于上面这个给定矩阵应返回 `6`。注意答案不应该是 `11` ，因为岛屿只能包含水平或垂直的四个方向的 `1` 

**示例 2:**

```
[[0,0,0,0,0,0,0,0]]
```

> 对于上面这个给定的矩阵, 返回 `0`。
>
> **注意:** 给定的矩阵`grid` 的长度和宽度都不超过 50。

#### 思路

- 递归

  当登录某个岛屿时，以此岛屿为中心，向上下左右扩散。当步入新地点时，则继续以新地点为中心朝四周扩散。很明显这符合递归的特性。

#### 代码实现

- 递归

  ```java
  public int maxAreaOfIsland(int[][] grid) {
          int area = 0;
          int maxArea = 0;
          for (int i = 0; i < grid.length; i++) {
              for (int j = 0; j < grid[0].length; j++) {
                  if (grid[i][j] == 1) {
                      //以此为中心朝四周扩散
                      area = getArea(grid, i, j);
                      maxArea = maxArea > area ? maxArea : area;
                  }
              }
          }
          return maxArea;
      }
  
     public int getArea(int[][] grid, int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == 0) {
           return 0;
         } else {
            //保证每块土地最多被访问一次，将登陆后的土地置为0
            grid[i][j] = 0;
            return 1 + getArea(grid, i - 1, j) + getArea(grid, i + 1, j) + getArea(grid, i, j - 1) + getArea(grid, i, j + 1);
          }
      }
  ```

  