---
title: Leetcode46.全排列
date: 2020-04-25 10:27:48
categories: Leetcode
tags:
  - 回溯
  - 深度优先遍历
---

## Leetcode46.全排列

给定一个 没有重复 数字的序列，返回其所有可能的全排列。

[题目链接](https://leetcode-cn.com/problems/permutations)

<!--more-->

示例:

>输入: [1,2,3]
>输出:
>[
>  [1,2,3],
>  [1,3,2],
>  [2,1,3],
>  [2,3,1],
>  [3,1,2],
>  [3,2,1]
>]

### 思路

全排列问题可以采用枚举的思路，可以用树形结构来表示。具体来说，就是**执行一次深度优先遍历，从树的根节点到叶子节点就是一次全排列。**以数组`[1,2,3]`的全排列为例：

![](https://f1bu920.github.io/images/Leetcode46.全排列.png)

使用一个布尔数组`used[len]`标记当前元素是否已经被添加。递归过程中，建立一个变量`depth`表示当前`list`中的数组元素，即递归深度，当`depth==len`时，表示已经全部添加，递归结束。

**注意状态重置，回溯要设置当前元素访问状态为`used[i]=false`，并且将`list`中最后一个元素删去**。

### 代码实现

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> lists = new ArrayList<>();
        int len = nums.length;
        if (len < 1) {
            return lists;
        }
        List<Integer> list = new ArrayList<>(len);
        boolean[] used = new boolean[len];
        dfs(nums, used, len, 0, list, lists);
        return lists;
    }

    private void dfs(int[] nums, boolean[] used, int len, int depth, List<Integer> list, List<List<Integer>> lists) {
        if (depth == len) {
            lists.add(new ArrayList<>(list));
            return;
        }
        for (int i = 0; i < len; i++) {
            if (!used[i]) {
                //访问后要立即将访问状态设为true
                used[i] = true;
                list.add(nums[i]);
                //递归调用
                dfs(nums, used, len, depth + 1, list, lists);
                //回溯，从深层结点回到浅层结点的过程，代码在形式上和递归之前是对称的
                used[i] = false;
                list.remove(list.size() - 1);
            }
        }
    }
}
```





