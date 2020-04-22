---
title: Leetcode1162.地图分析
date: 2020-03-29 10:49:21
categories: Leetcode
tags:
  - 广度优先搜索
  - Queue
---

## Leetcode1162.地图分析

你现在手里有一份大小为 N x N 的『地图』（网格） grid，上面的每个『区域』（单元格）都用 0 和 1 标记好了。其中 0 代表海洋，1 代表陆地，你知道距离陆地区域最远的海洋区域是是哪一个吗？请返回该海洋区域到离它最近的陆地区域的距离。

我们这里说的距离是『曼哈顿距离』（ Manhattan Distance）：(x0, y0) 和 (x1, y1) 这两个区域之间的距离是 |x0 - x1| + |y0 - y1| 。

如果我们的地图上只有陆地或者海洋，请返回 -1。

 [题目链接](https://leetcode-cn.com/problems/as-far-from-land-as-possible)

<!--more-->

示例 1：

> 输入：[[1,0,1],[0,0,0],[1,0,1]]
> 输出：2
> 解释： 
> 海洋区域 (1, 1) 和所有陆地区域之间的距离都达到最大，最大距离为 2。

示例 2：

> 输入：[[1,0,0],[0,0,0],[0,0,0]]
> 输出：4
> 解释： 
> 海洋区域 (2, 2) 和所有陆地区域之间的距离都达到最大，最大距离为 4。

提示：

> 1 <= grid.length == grid[0].length <= 100
>
> grid[i][j] 不是 0 就是 1

### 思路

题目中问二维表格上的距离问题，很明显使用广度优先遍历。

1. 先遍历整个数组，将陆地元素存到队列中，这里将二维坐标转化为一维数字；
2. 这个多源情况需要在`while`循环内部一次性把队列中所有元素全部取出；
3. 一个元素添加到队列中后，需要将它设为已访问状态，防止出现死循环；

二维表格问题常用技巧：

- 设置方向向量，使得向「四面八方」扩散的代码更加紧凑； 
- 判断数组是否越界
- 将二维坐标与一维坐标相互转换。

### 代码实现

```java
class Solution {
    public int maxDistance(int[][] grid) {
        int step = 0;
        //方向向量
        int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        Queue<Integer> queue = new LinkedList<>();
        int N = grid.length;
        //将全部陆地存入队列中
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                if (grid[i][j] == 1) {
                    queue.add(i * N + j);
                }
            }
        }
        int size = queue.size();
        //若表格中全为海洋或者全为陆地则返回-1
        if (size == 0 || size == N*N) {
            return -1;
        }
        while (!queue.isEmpty()) {
            int currentSize = queue.size();
            //一次性依次将队列中元素全部出队
            for (int i = 0; i < currentSize; i++) {
                int node = queue.poll();
                int x = node / N;
                int y = node % N;
                //原始坐标加上方向向量
                for (int[] direction : directions) {
                    int newX = x + direction[0];
                    int newY = y + direction[1];
                    if (newX >= 0 && newX < N && newY >= 0 && newY < N && grid[newX][newY] == 0) {
                        //访问后立刻设为已访问状态，可设为非0的任意数字
                        grid[newX][newY] = 2;
                        //刚访问的海洋入队
                        queue.add(newX * N + newY);
                    }
                }
            }
            //距离加1
            step++;
        }
        //因为最后一次访问后表格中海洋已全部访问完，队列中只有刚入队的海洋，step还要再加一次，所以返回step-1；
        return step - 1;
    }
}
```



