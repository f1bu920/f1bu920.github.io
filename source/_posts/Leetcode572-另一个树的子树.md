---
title: Leetcode572.另一个树的子树
date: 2020-05-07 09:39:15
categories: Leetcode
tags:
  - 树
  - 递归
  - DFS
---

## Leetcode572.另一个树的子树

给定两个非空二叉树 s 和 t，检验 s 中是否包含和 t 具有相同结构和节点值的子树。s 的一个子树包括 s 的一个节点和这个节点的所有子孙。s 也可以看做它自身的一棵子树。

[题目链接](https://leetcode-cn.com/problems/subtree-of-another-tree)

<!--more-->

示例 1:
给定的树 s:

         3
        / \
       4   5
    
      / \
    
     1   2
    
    给定的树 t：
    
       4 
    
      / \
    
     1   2
    
    返回 true，因为 t 与 s 的一个子树拥有相同的结构和节点值。
    
示例 2:
给定的树 s：

         3
        / \
       4   5
    
      / \
    
     1   2
    
        /
    
       0
    
    给定的树 t：
    
       4
    
      / \
    
     1   2
    
    返回 false。
     
### 思路

创建一个辅助函数`isSame(TreeNode s, TreeNode t)`检查以`s`和`t`为根节点的二叉树是否相同：

- 根节点相同，再去判断左右子树是否相同，这里含有递归地性质。
- 递归结束条件：当两个树都为`null`时，相等；当一个为空一个不为空时，不相等。

整体结构也有递归地关系：

- 先判断根节点为`s`的树与`t`树是否相等，`isSame(s,t)`；
- 否则判断`s`的左右子树是否与`t`树相等，`isSubTree(s.left,t)||isSubTree(s.right,t)`。
- 递归结束条件：当`s`为空时，直接返回`false`。



### 代码实现

```java
class Solution {
    public boolean isSubtree(TreeNode s, TreeNode t) {
        if (s == null) {
            return false;
        }
        if (isSame(s, t)) {
            return true;
        } else {
            return isSubtree(s.left, t) || isSubtree(s.right, t);
        }
    }

    private boolean isSame(TreeNode s, TreeNode t) {
        if (s == null && t == null) {
            return true;
        }
        //注意这里经过上面的判断，s、t一定有一个不为空
        if (s == null || t == null) {
            return false;
        }
        if (s.val != t.val) {
            return false;
        } else
            return isSame(s.left, t.left) && isSame(s.right, t.right);
    }
}
```

