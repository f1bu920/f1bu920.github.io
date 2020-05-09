---
title: Leetcode202.快乐数
date: 2020-04-30 09:38:20
categories: Leetcode
tags:
  - Math
  - 哈希表
---

## Leetcode202.快乐数

编写一个算法来判断一个数 n 是不是快乐数。

「快乐数」定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。如果 可以变为  1，那么这个数就是快乐数。

如果 n 是快乐数就返回 True ；不是，则返回 False 。

 [题目链接](https://leetcode-cn.com/problems/happy-number)

<!--more-->

示例：

>输入：19
>输出：true

解释：

>12 + 92 = 82
>82 + 22 = 68
>62 + 82 = 100

### 思路

编写一个辅助函数`powerSum`求每个数字的平方和，利用哈希表存储结果。

一旦出现哈希表中存在过的数字，代表出现死循环，直接`return false`。

哈希表可以使用`HashSet`，这里我使用的是`HashMap`，性能较差。

### 代码实现

```java
class Solution {
    public boolean isHappy(int n) {
        if (n <= 0) {
            return false;
        }
        Map<Integer, Integer> map = new HashMap<>();
        int temp = powerSum(n);
        if (temp == 1) {
            return true;
        }
        while (temp != 1) {
            if (map.containsKey(n)) {
                break;
            } else {
                map.put(n, temp);
                n = temp;
                temp = powerSum(n);
                if (temp == 1) {
                    return true;
                }
            }
        }
        return false;
    }
    //求平方和的辅助函数
    private int powerSum(int n) {
        int x = n;
        int sum = 0;
        int t;
        while (x != 0) {
            t = x % 10;
            sum += t * t;
            x /= 10;
        }
        return sum;
    }
}
```