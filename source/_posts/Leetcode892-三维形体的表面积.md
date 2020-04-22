---
title: Leetcode892.三维形体的表面积
date: 2020-03-25 11:23:45
categories: Leetcode
tags:
  - Math
---

## Leetcode892.三维形体的表面积

在 N * N 的网格上，我们放置一些 1 * 1 * 1  的立方体。

每个值 v = grid[i][j] 表示 v 个正方体叠放在对应单元格 (i, j) 上。

请你返回最终形体的表面积。

 [题目链接](https://leetcode-cn.com/problems/surface-area-of-3d-shapes)

<!--more-->

示例 1：

> 输入：[[2]]
>
> 输出：10

示例 2：

> 输入：[[1,2],[3,4]]
>
> 输出：34

示例 3：

> 输入：[[1,0],[0,2]]
>
> 输出：16

示例 4：

> 输入：[[1,1,1],[1,0,1],[1,1,1]]
>
> 输出：32

示例 5：

> 输入：[[2,2,2],[2,1,2],[2,2,2]]
>
> 输出：46


提示：

> 1 <= N <= 50
>
> 0 <= grid[i][j] <= 50

#### 思路

将网格上的正方体看成一个个柱体，每个柱体都有上下两个底面，每个小正方体贡献`4`个侧面。

然后，将柱体贴合在一起后，就需要把贴合部分的面积减去，**两个柱体贴合部分的表面积就是这两个柱体高度的最小值*2。**

#### 代码实现

```java
class Solution {
    public int surfaceArea(int[][] grid) {
        int n = grid.length;
        int area = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                int height = grid[i][j];
                if (height > 0) {
                    //每个小正方体贡献四个侧面积
                    area += 2 + (height << 2);
                    //减去贴合部分的面积
                    area -= i > 0 ? Math.min(height, grid[i - 1][j]) << 1 : 0;
                    area -= j > 0 ? Math.min(height, grid[i][j - 1]) << 1 : 0;
                }
            }
        }
        return area;
    }
}
```

