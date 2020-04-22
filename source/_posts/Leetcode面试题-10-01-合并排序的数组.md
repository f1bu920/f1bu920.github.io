---
title: Leetcode面试题 10.01.合并排序的数组
date: 2020-03-14 13:00:04
categories: Leetcode
tags: 
  - Array
  - Java
---

## Leetcode面试题 10.01.合并排序的数组

给定两个排序后的数组 A 和 B，其中 A 的末端有足够的缓冲空间容纳 B。 编写一个方法，将 B 合并入 A 并排序。

初始化 A 和 B 的元素数量分别为 m 和 n。

[题目链接](https://leetcode-cn.com/problems/sorted-merge-lcci)

<!--more-->

示例:

> 输入:
>
> A = [1,2,3,0,0,0], m = 3
> B = [2,5,6],       n = 3
>
> 输出: [1,2,2,3,5,6]

说明:

> A.length == n + m

**思路：双指针法，利用数组已经排序的性质，将两个数组看成队列，每次从两个数组头部取出较小的数字放入结果中。代码实现如下：**

```java
class Solution {
    public void merge(int[] A, int m, int[] B, int n) {
        //x,y为数组A，B的指针
        int x=0;
        int y = 0;
        int i =0;
        //将A复制到t中
        int[] t = new int[m];
        for (int j = 0;j<m;j++){
            t[j]=A[j];
        }
        //每次从B和t头部取出较小的数字放入A中，直至有一个数组全部取出后将另一个数组接到A的尾部
        while (x<m||y<n){
            if (x==m){
               A[i++] = B[y++];
            }else {
               if (y==n){
                  A[i++] = t[x++];
               }
               else{
                 if (t[x]<B[y]){
                    A[i++] = t[x++];
                 }
                 else {
                   A[i++] = B[y++];
                 }
               }
            }
       }
}
}
```

