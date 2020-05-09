---
title: Leetcode98.验证二叉搜索树
date: 2020-05-05 09:26:59
categories: Leetcode
tags:

  - 树
  - 模拟栈
  - 递归
---

## Leetcode98.验证二叉搜索树

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

- 节点的左子树只包含小于当前节点的数。
- 节点的右子树只包含大于当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

[题目链接](https://leetcode-cn.com/problems/validate-binary-search-tree)

<!--more-->

示例 1:

```
输入:

    2

   / \

  1   3

输出: true

```

示例 2:

```
输入:

    5

   / \

  1   4

     / \

    3   6

输出: false

解释: 输入为: [5,1,4,null,null,3,6]。

     根节点的值为 5 ，但是其右子节点值为 4 。

```

#### 思路

- 中序遍历

  因为二叉搜索树的中序遍历一定是升序的，我们可以借助辅助栈来遍历节点，检查当前节点的值是否大于上一个节点的值，我们用`preNodeVal`来记录上一个节点的值，其初始化为`-Long.MAX_VALUE`。

- 递归

  [参考链接](https://leetcode-cn.com/problems/validate-binary-search-tree/solution/zhong-xu-bian-li-qing-song-na-xia-bi-xu-miao-dong-/ )

  
#### 代码实现

中序遍历

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        long preNodeVal = -Long.MAX_VALUE;
        while (!stack.isEmpty() || cur != null) {
            while (cur != null) {
                stack.push(cur);
                cur = cur.left;
            }
            cur = stack.pop();
            if (cur.val <= preNodeVal) {
                return false;
            }
            preNodeVal = (long) cur.val;
            cur = cur.right;
        }
        return true;
    }
}
```