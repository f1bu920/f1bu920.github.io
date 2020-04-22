---
title: Leetcode543.二叉树的直径
date: 2020-03-26 10:34:59
categories: Leetcode
tags:
  - 递归
  - 树
  - 深度优先搜索
---

## Leetcode543.二叉树的直径

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

 [题目链接](https://leetcode-cn.com/problems/diameter-of-binary-tree)

<!--more-->

示例 :

> 给定二叉树
>
> 返回 3, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。

          1
         / \
        2   3
       / \     
      4   5    
> 注意：两结点之间的路径长度是以它们之间边的数目表示。

#### 思路

一条路径的长度是其经过的结点数减一，所以我们只需要求得最长路径即可。而任意一条路径都可看成由某个结点为起点，拼接其由左右儿子向下遍历的路径。

令该结点的左儿子向下遍历经过的最多结点数为`L`，右儿子向下遍历经过的最多结点数位`R`，所以以该结点为起始的最多结点数为`L+R+1`，即最长路径为`L+R`。

定义一个递归函数，计算以传入结点`node`为根的子树深度。先递归调用`node`的左儿子和右儿子，得到左右子树的最大深度`L、R`，则最大深度为`max(L,R)+1`。

维护一个全局变量`res`代表最长路径。



#### 代码实现

```java
class Solution {
    //最长路径
    int res = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        depth(root);
        return res;
    }

    private int depth(TreeNode node) {
        if (node == null) {
            return 0;
        }
        //左儿子为根的子树的深度
        int L = depth(node.left);
        //右儿子为根的子树的深度
        int R = depth(node.right);
        //最大节点数为L+R+1，所以最长路径为L+R
        res = Math.max(res, L + R);
        //返回以该结点为根的子树深度
        return Math.max(L, R) + 1;
    }
}
```

