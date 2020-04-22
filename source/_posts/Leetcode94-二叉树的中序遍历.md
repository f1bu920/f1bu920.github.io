---
title: Leetcode94.二叉树的中序遍历
date: 2020-03-25 12:58:25
categories: Leetcode
tags:
  - 递归
  - Stack
  - 树
---

## Leetcode94.二叉树的中序遍历

给定一个二叉树，返回它的中序 遍历。

[题目链接](https://leetcode-cn.com/problems/binary-tree-inorder-traversal)

类似题目：

1. [前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal)
2. [后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal)

<!--more-->

示例:

> 输入: [1,null,2,3]

> 1 
>
>    \
>      2
>     /
>    3
>
> 输出: [1,3,2]

进阶: 递归算法很简单，你可以通过迭代算法完成吗？

#### 思路

- 递归

  利用一个辅助函数，传入结点和集合，很简单。

- 迭代

  利用栈模拟迭代

#### 代码实现

- 递归

  ```java
      public List<Integer> inorderTraversal(TreeNode root) {
          List<Integer> list = new ArrayList<>();
          inOrder(root, list);
          return list;
      }
  
      private void inOrder(TreeNode node, List<Integer> list) {
          if (node == null) {
              return;
          }
          if (node.left != null) {
              inOrder(node.left, list);
          }
          list.add(node.val);
          if (node.right != null) {
              inOrder(node.right, list);
          }
      }
  ```

- 迭代

  ```java
  class Solution {
      public List<Integer> inorderTraversal(TreeNode root) {
          List<Integer> list = new ArrayList<>();
          Stack<TreeNode> stack = new Stack<>();
          TreeNode cur = root;
          while(cur!=null||!stack.isEmpty()){
              //结点不为空一直压栈
               while (cur!=null){
                  stack.push(cur);
                   //考虑左子树
                  cur = cur.left;
              }
              //结点为空就出栈
              cur = stack.pop();
              //当前值加入
              list.add(cur.val);
              //考虑右子树
              cur = cur.right;
          }
          return list;
      }
  }
  ```

  