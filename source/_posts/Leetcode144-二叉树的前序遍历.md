---
title: Leetcode144.二叉树的前序遍历
date: 2020-03-25 13:23:30
categories: Leetcode
tags:
  - 递归
  - Stack
  - 树
---

## Leetcode144.二叉树的前序遍历

给定一个二叉树，返回它的 前序 遍历。

[题目链接](https://leetcode-cn.com/problems/binary-tree-preorder-traversal)

类似题目：

1. [中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal)
2. [后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal)

<!--more-->

 示例:

输入: [1,null,2,3]  

> 1
>
> ​    \
>      2
>     /
>    3 
>
> 输出: [1,2,3]

进阶: 递归算法很简单，你可以通过迭代算法完成吗？

#### 思路

- 递归

  借助辅助函数传入当前根结点与集合。

- 迭代

  借助栈模拟递归



#### 代码实现

- 递归

  ```java
  class Solution {
      public List<Integer> preorderTraversal(TreeNode root) {
          List<Integer> list = new ArrayList<>();
          preOrder(root, list);
          return list;
      }
  
      private void preOrder(TreeNode node, List<Integer> list) {
          if (node == null) {
              return;
          }
          list.add(node.val);
          preOrder(node.left, list);
          preOrder(node.right, list);
      }
  }
  ```

- 迭代

  ```java
  class Solution {
      public List<Integer> preorderTraversal(TreeNode root) {
          List<Integer> list = new ArrayList<>();
          Stack<TreeNode> stack = new Stack<>();
          if (root == null) {
              return list;
          }
          stack.push(root);
          while (!stack.isEmpty()) {
              TreeNode node = stack.pop();
              list.add(node.val);
              if (node.right != null) {
                  stack.push(node.right);
              }
              if (node.left != null) {
                  stack.push(node.left);
              }
          }
          return list;
      }
  }
  ```

  