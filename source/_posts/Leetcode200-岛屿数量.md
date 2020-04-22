---
title: Leetcode200.岛屿数量
date: 2020-04-20 10:32:27
categories: Leetcode
tags:
  - 广度优先遍历
  - 深度优先遍历
---

## Leetcode200.岛屿数量

给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

[题目链接](https://leetcode-cn.com/problems/number-of-islands)

<!--more-->

示例 1:

>输入:
>11110
>11010
>11000
>00000
>输出: 1

示例 2:

>输入:
>11000
>11000
>00100
>00011
>输出: 3
>解释: 每座岛屿只能由水平和/或竖直方向上相邻的陆地连接而成。



#### 思路

- 广度优先遍历

  用一个布尔类型数组`isVisited[row][col]`标记当前元素是否被访问过，借助一个辅助队列。

- 深度优先遍历

  - 辅助栈

    将广度优先遍历中的辅助队列替换为辅助栈即可。

  - 利用系统栈

#### 代码实现

- 广度优先遍历

  ```java
  class Solution {
      public int numIslands(char[][] grid) {
          int res = 0;
          int row = grid.length;
          if (row == 0) {
              return 0;
          }
          int col = grid[0].length;
          //方向向量
          int[][] directions = new int[][]{{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
          //标记是否被访问过
          boolean[][] isVisited = new boolean[row][col];
          for (int i = 0; i < row; i++) {
              for (int j = 0; j < col; j++) {
                  if (!isVisited[i][j] && grid[i][j] == '1') {
                      res++;
                      Queue<Integer> queue = new LinkedList<>();
                      queue.add(i * col + j);
                      isVisited[i][j] = true;
                      while (!queue.isEmpty()) {
                          int head = queue.poll();
                          int x = head / col;
                          int y = head % col;
                          for (int k = 0; k < 4; k++) {
                              int newX = x + directions[k][0];
                              int newY = y + directions[k][1];
                              if (newX >= 0 && newX < row && newY >= 0 && newY < col && !isVisited[newX][newY] && grid[newX][newY] == '1') {
                                  queue.add(newX * col + newY);
                                  isVisited[newX][newY] = true;
                              }
                          }
                      }
                  }
              }
          }
          return res;
      }
  }
  ```

- 深度优先遍历

  - 利用辅助栈

    ```java
    class Solution {
        public int numIslands(char[][] grid) {
            int res = 0;
            int row = grid.length;
            if (row == 0) {
                return 0;
            }
            int col = grid[0].length;
            int[][] directions = new int[][]{{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
            boolean[][] isVisited = new boolean[row][col];
            for (int i = 0; i < row; i++) {
                for (int j = 0; j < col; j++) {
                    if (!isVisited[i][j] && grid[i][j] == '1') {
                        res++;
                        Stack<Integer> stack = new Stack<>();
                        stack.push(i*col+j);
                        isVisited[i][j] = true;
                        while (!stack.isEmpty()) {
                            int head = stack.pop();
                            int x = head / col;
                            int y = head % col;
                            for (int k = 0; k < 4; k++) {
                                int newX = x + directions[k][0];
                                int newY = y + directions[k][1];
                                if (newX >= 0 && newX < row && newY >= 0 && newY < col && !isVisited[newX][newY] && grid[newX][newY] == '1') {
                                    stack.push(newX * col + newY);
                                    isVisited[newX][newY] = true;
                                }
                            }
                        }
                    }
                }
            }
            return res;
        }
    }
    ```

  - 利用系统栈

    同理如辅助栈。

    

    