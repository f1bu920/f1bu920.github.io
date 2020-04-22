---
title: Leetcode145.二叉树的后序遍历
date: 2020-03-25 14:20:46
categories: Leetcode
tags:
  - 递归
  - Stack
  - 树
---

## Leetcode145.二叉树的后序遍历

给定一个二叉树，返回它的 后序 遍历。

[题目链接](https://leetcode-cn.com/problems/binary-tree-postorder-traversal)

类似题目：

1. [前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal)
2. [中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal)

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
> 输出: [3,2,1]

进阶: 递归算法很简单，你可以通过迭代算法完成吗？

#### 思路及代码实现

- 递归

  ```java
  class Solution {
      public List<Integer> postorderTraversal(TreeNode root) {
          List<Integer> list = new ArrayList<>();
          postOrder(root, list);
          return list;
      }
  
      private void postOrder(TreeNode node, List<Integer> list) {
          if (node == null) {
              return;
          }
          postOrder(node.left, list);
          postOrder(node.right, list);
          list.add(node.val);
      }
  }
  ```

- 迭代

  1. 用一个指针`cur`标记当前退出的节点是什么；
  2. 后序遍历中遍历完左子树和右子树`cur`都会回到根节点，所以当前不管是从左子树还是右子树回到根结点都不应该再操作了，应该退回上层。 
  3. 如果是从右边再返回根结点，应该回到上层。

  ```java
  class Solution {
      public List<Integer> postorderTraversal(TreeNode root) {
          List<Integer> list = new ArrayList<>();
          if(root == null){
              return list;
          }
          Stack<TreeNode> stack = new Stack<>();
          TreeNode cur = root;
          stack.push(root);
          while (!stack.isEmpty()) {
              TreeNode peek = stack.peek();
              //一直将左子树压栈
              if (peek.left != null && peek.left != cur && peek.right != cur) {
                  stack.push(peek.left);
                  //从左子树返回
              } else if (peek.right != null && peek.right != cur) {
                  stack.push(peek.right);
              } else {
                  //从右子树返回
                  list.add(stack.pop().val);
                  cur = peek;
              }
          }
          return list;
      }
  }
  ```

  