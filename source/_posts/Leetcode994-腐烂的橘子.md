---
title: Leetcode994.腐烂的橘子
date: 2020-03-16 15:57:37
categories: Leetcode
tags:
  - Queue
  - 广度优先搜索
---

## Leetcode994.腐烂的橘子

在给定的网格中，每个单元格可以有以下三个值之一：

值 0 代表空单元格；
值 1 代表新鲜橘子；
值 2 代表腐烂的橘子。
每分钟，任何与腐烂的橘子（在 4 个正方向上）相邻的新鲜橘子都会腐烂。

返回直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1。

 [题目链接](https://leetcode-cn.com/problems/rotting-oranges)

<!--more-->

示例 1：

> 输入：[[2,1,1],[1,1,0],[0,1,1]]
>
> 输出：4

示例 2：

> 输入：[[2,1,1],[0,1,1],[1,0,1]]
>
> 输出：-1
> 解释：左下角的橘子（第 2 行， 第 0 列）永远不会腐烂，因为腐烂只会发生在 4 个正向上。

示例 3：

> 输入：[[0,2]]
>
> 输出：0
> 解释：因为 0 分钟时已经没有新鲜橘子了，所以答案就是 0 。


提示：

> 1 <= grid.length <= 10
>
> 1 <= grid[0].length <= 10
> grid[i][j] 仅为 0、1 或 2

- 思路

  第一天将所有的烂橘子入队列，然后按天数依次腐烂，新腐烂的橘子入队列，记录天数。然后判断是否还有没腐烂的橘子。

- 代码实现

  ```java
  public class Solution {
      //按照上左下右的顺序腐烂
      int[] tr = new int[]{-1, 1, 0, 0};
      int[] tc = new int[]{0, 0, -1, 1};
      public int orangesRotting(int[][] grid) {
          int R = grid.length;
          //队列
          Queue<Integer> arrayDeque = new ArrayDeque<>();
          int C = grid[0].length;
          //存储天数
          Map<Integer, Integer> depth = new HashMap<>();
          //将第0天所有腐烂的橘子入队列
          for (int r = 0; r < R; r++) {
              for (int c = 0; c < C; c++) {
                  if (grid[r][c] == 2) {
                      //转换为单数组
                      int code = r * C + c;
                      arrayDeque.add(code);
                      depth.put(code, 0);
                  }
              }
          }
          //存储结果
          int ret = 0;
          while (!arrayDeque.isEmpty()) {
              int code = arrayDeque.remove();
              int r = code / C, c = code % C;
              //按上左下右顺序
              for (int k = 0; k < 4; k++) {
                  int nr = r + tr[k];
                  int nc = c + tc[k];
                  if (0 <= nr && nr < R && 0 <= nc && nc < C && grid[nr][nc] == 1) {
                      grid[nr][nc] = 2;
                      int ncode = nr * C + nc;
                      arrayDeque.add(ncode);
                      depth.put(ncode, depth.get(code) + 1);
                      ret = depth.get(ncode);
                  }
              }
          }
          //最后判断是否还有新鲜的橘子
          for (int[] row : grid) {
              for (int col : row) {
                  if (col == 1) {
                      return -1;
                  }
              }
          }
          return ret;
      }
  }
  ```

  