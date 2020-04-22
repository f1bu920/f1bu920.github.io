---
title: Leetcode199.二叉树的右视图
date: 2020-04-22 09:56:33
categories: Leetcode
tags:
  - 广度优先遍历
  - 深度优先遍历
---

## Leetcode199.二叉树的右视图

给定一棵二叉树，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

[题目链接](https://leetcode-cn.com/problems/binary-tree-right-side-view)

<!--more-->

示例:

>输入: [1,2,3,null,5,null,4]
>输出: [1, 3, 4]
>解释:
>
>   1            <---
> /   \
>2     3         <---
> \     \
>  5     4       <---

#### 思路

- 广度优先遍历

  要我们找到每一层的最后一个节点，可以使用层序遍历。使用辅助队列`nodeQueue `存储节点，使用辅助队列`depthQueue `存储深度，使用`map`存放结果，其中`k-v`分别对应深度和节点值。每一层从左到右遍历，利用哈希冲突不断覆盖找到每一层的最后一个节点。

- 深度优先遍历

  - 辅助栈形式

    只需将广度优先遍历中的辅助队列替换为辅助栈即可。

  - 递归形式

    采用前序遍历变形，注意要**先遍历右子树，再遍历左子树**。



#### 代码实现

- 广度优先遍历

  ```java
  class Solution {
      List<Integer> list = new ArrayList<>();
  
      public List<Integer> rightSideView(TreeNode root) {
          if (root == null) {
              return list;
          }
          //存储结果，通过不断覆盖找到每层最后一个节点
          Map<Integer, Integer> map = new HashMap<>();
          //存储节点和深度的辅助队列
          Queue<TreeNode> nodeQueue = new LinkedList<>();
          Queue<Integer> depthQueue = new LinkedList<>();
          int maxDepth = -1;
          nodeQueue.add(root);
          depthQueue.add(1);
          while (!nodeQueue.isEmpty()) {
              TreeNode node = nodeQueue.poll();
              int depth = depthQueue.remove();
              if (node != null) {
                  //最大深度
                  maxDepth = Math.max(maxDepth, depth);
                  //找到每一个深度的最后一个节点
                  map.put(depth, node.val);
                  nodeQueue.add(node.left);
                  nodeQueue.add(node.right);
                  depthQueue.add(depth + 1);
                  depthQueue.add(depth + 1);
              }
          }
          //将结果添加到list中
          for (int i = 1;i<=maxDepth;i++){
              list.add(map.get(i));
          }
          return list;
      }
  }
  ```

- 深度优先遍历

  - 辅助栈

    只需将广度优先遍历中辅助队列替换为辅助栈即可。

  - 递归

    ```java
    class Solution {
         List<Integer> list = new ArrayList<>();
    
        public List<Integer> rightSideView(TreeNode root) {
            if (root == null) {
                return list;
            }
            dfs(list, root, 0);
            return list;
        }
    
        private void dfs(List<Integer> list, TreeNode node, int height) {
            if (node != null) {
                if (list.size() == height) {
                    list.add(node.val);
                }
                dfs(list, node.right, height + 1);
                dfs(list, node.left, height + 1);
            }
        }
    }
    ```

    