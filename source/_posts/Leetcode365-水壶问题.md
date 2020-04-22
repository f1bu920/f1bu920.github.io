---
title: Leetcode365.水壶问题
date: 2020-03-21 14:59:30
categories: Leetcode
tags:
  - 广度优先搜索
---

## Leetcode365.水壶问题

有两个容量分别为 *x*升 和 *y*升 的水壶以及无限多的水。请判断能否通过使用这两个水壶，从而可以得到恰好 *z*升 的水？

如果可以，最后请用以上水壶中的一或两个来盛放取得的 *z升* 水。

你允许：

- 装满任意一个水壶
- 清空任意一个水壶
- 从一个水壶向另外一个水壶倒水，直到装满或者倒空

[题目链接](https://leetcode-cn.com/problems/water-and-jug-problem/ )

<!--more-->

**示例 1:** (From the famous [*"Die Hard"* example](https://www.youtube.com/watch?v=BVtQNK_ZUJg))

```
输入: x = 3, y = 5, z = 4
输出: True
```

**示例 2:**

```
输入: x = 2, y = 6, z = 5
输出: False
```

#### 思路

可以定义一个内部类`Pair`，`Pair`中的两个变量`x、y`分别表示当前两个水壶的水`curX、curY`。创建一个队列`queue`，来存储所有的状态，创建一个哈希集合`visited`来避免保存遍历过的状态。

题目说：

> 你允许：
>
> - 装满任意一个水壶
> - 清空任意一个水壶
> - 从一个水壶向另外一个水壶倒水，直到装满或者倒空

所以一共有以下几种情况

装满任意一个水壶，定义为「情况一」，分为：
（1）装满 `A`；
（2）装满 `B`。

清空任意一个水壶，定义为「情况二」，分为
（1）清空 `A`；
（2）清空 `B`。

从一个水壶向另外一个水壶倒水，直到装满或者倒空，定义为「情况三」，其实根据描述「装满」或者「倒空」就知道可以分为 4 种情况：

（1）从 `A` 到 `B`，使得 `B` 满，`A` 还有剩；
（2）从 `A` 到 `B`，此时 `A` 的水太少，`A` 倒尽，`B` 没有满；
（3）从 `B` 到 `A`，使得 `A` 满，`B` 还有剩余；
（4）从 `B` 到 `A`，此时 `B` 的水太少，`B` 倒尽，`A` 没有满。

因此，从当前「状态」最多可以进行 8 种操作，得到 8 个新「状态」，对这 8 个新「状态」，依然可以扩展，一直做下去，直到某一个状态满足题目要求。

#### 代码实现

```java
import java.util.*;
import java.util.Queue;

public class Solution {
    public boolean canMeasureWater(int x, int y, int z) {
        if (x + y < z) {
            return false;
        }
        if (z == 0) {
            return true;
        }
        Set<Pair> visited = new HashSet<>();
        Queue<Pair> queue = new ArrayDeque<>();
        Pair pair = new Pair(0, 0);
        queue.add(pair);
        visited.add(pair);
        while (!queue.isEmpty()) {
            Pair head = queue.poll();
            int curX = head.getX();
            int curY = head.getY();
            if (curX == z || curY == z || curX + curY == z) {
                return true;
            }
            List<Pair> nextPairs = getNextPairs(curX, curY, x, y);
//            System.out.println(head+"->" +nextPairs);
            for (Pair p : nextPairs) {
                if (!visited.contains(p)) {
                    visited.add(p);
                    queue.add(p);
                }
            }
            System.out.println(queue);
        }
        return false;
    }

    public List<Pair> getNextPairs(int curX, int curY, int x, int y) {
        List<Pair> list = new ArrayList<>(8);
        //装满A
        Pair pair1 = new Pair(x, curY);
        //装满B
        Pair pair2 = new Pair(curX, y);
        //A没满时装满
        if (curX < x) {
            list.add(pair1);
        }
        //B没满时装满
        if (curY < y) {
            list.add(pair2);
        }
        //清空
        Pair pair3 = new Pair(0, curY);
        Pair pair4 = new Pair(curX, 0);
        //有水时清空
        if (curX > 0) {
            list.add(pair3);
        }
        if (curY > 0) {
            list.add(pair4);
        }
        //A->B,B fill,A left
        Pair pair5 = new Pair(curX - (y - curY), y);
        //A->B,B not fill,A empty
        Pair pair6 = new Pair(0, curY + curX);
        //B->A,A fill,B left
        Pair pair7 = new Pair(x, curY - (x - curX));
        //B->A,A not fill,B empty
        Pair pair8 = new Pair(curX + curY, 0);
        //有剩余
        if (curX - (y - curY) > 0) {
            list.add(pair5);
        }
        if (curY - (x - curX) > 0) {
            list.add(pair7);
        }
        //装不满
        if (curX + curY < x) {
            list.add(pair8);
        }
        if (curX + curY < y) {
            list.add(pair6);
        }
        return list;
    }


    private class Pair {
        private int x;
        private int y;

        public Pair(int x, int y) {
            this.x = x;
            this.y = y;
        }

        @Override
        public String toString() {
            return "Pair{" +
                    "x=" + x +
                    ", y=" + y +
                    '}';
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Pair)) return false;
            Pair pair = (Pair) o;
            return getX() == pair.getX() &&
                    getY() == pair.getY();
        }

        @Override
        public int hashCode() {
            return Objects.hash(getX(), getY());
        }
        
        public int getX() {
            return x;
        }
        
        public int getY() {
            return y;
        }
    }
}
```

