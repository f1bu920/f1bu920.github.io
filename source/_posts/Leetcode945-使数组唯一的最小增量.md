---
title: Leetcode945.使数组唯一的最小增量
date: 2020-03-22 10:43:07
categories: Leetcode
tags:
  - 排序
---

## Leetcode945.使数组唯一的最小增量

给定整数数组 A，每次 *move* 操作将会选择任意 `A[i]`，并将其递增 `1`。

返回使 `A` 中的每个值都是唯一的最少操作次数。

[题目链接](https://leetcode-cn.com/problems/minimum-increment-to-make-array-unique/ )

<!--more-->

**示例 1:**

```
输入：[1,2,2]
输出：1
解释：经过一次 move 操作，数组将变为 [1, 2, 3]。
```

**示例 2:**

```
输入：[3,2,1,2,1,7]
输出：6
解释：经过 6 次 move 操作，数组将变为 [3, 4, 1, 2, 5, 7]。
可以看出 5 次或 5 次以下的 move 操作是不能让数组的每个值唯一的。
```

**提示：**

1. `0 <= A.length <= 40000`
2. `0 <= A[i] < 40000`

- 思路及代码实现

  1. 使用集合`HashSet`存储，若重复出现，则暴力+1。（超出时间限制

     ```java
     class Solution {
         public int minIncrementForUnique(int[] A) {
             if (A == null) {
                 return 0;
             }
             Set<Integer> set = new HashSet<>();
             int count = 0;
             for (int i = 0; i < A.length; i++) {
                 if (!set.contains(A[i])) {
                     set.add(A[i]);
                 } else {
                     A[i]++;
                     i--;
                     count++;
                 }
             }
             return count;
         }
     }
     ```

  2. 计数法

     由题意`0<=A[i]<=40000`，所以开辟空间大小为80000的数组`count`，(为了防止A中为40000个40000的特殊情况)，`count`记录A中每个数字出现次数，其下标为A中元素。维护两个变量，`res`代表增加次数，`token`记录重复数字出现次数。若`count[x]>=2`，即A中`x`元素出现两次以上，`token`加上`count[x]-1`，`res`减去`x*(count[x]-1)`，否则如果还有重复元素即`token>0`，则在没有元素处加上即`res+=x`。

     ```java
         public int minIncrementForUnique(int[] A) {
             if (A == null || A.length == 0) {
                 return 0;
             }
             int[] count = new int[8000];
             for (int a : A) {
                 count[a]++;
             }
             int res = 0, token = 0;
             for (int x = 0; x < 8000; x++) {
                 if (count[x] >= 2) {
                     token += count[x] - 1;
                     res -= x * (count[x] - 1);
                 } else {
                     if (count[x] == 0 && token > 0) {
                         res += x;
                         token--;
                     }
                 }
             }
             return res;
     ```

  3. 排序

     先将数组排序。然后对数组进行扫描。

     - 如果`A[i-1]==A[i]`，将操作次数减去`A[i-1]`，并且重复数字+1；
     - 如果`A[i-1]<A[i]`，那么两者之间有`A[i]-A[i-1]-1`个数，我们有`token`个重复的数可以变成这之间的数。所以我们最多可以改变`give = Math.min(token,A[i]-A[i-1]-1)`个数。这`give`个数对结果的影响为`A[i-1]*give+(give+1)*give/2`。
     - 最后遍历完成后，如果还有重复的数，例如`[2,2,3]`，上步`give`始终为0，就需要把这些数变成`A[A.length-1]`后面区间上的数了。

     ```java
     class Solution {
         public int minIncrementForUnique(int[] A) {
             int res = 0, token = 0;
             if (A == null || A.length == 0) {
                 return 0;
             }
             Arrays.sort(A);
             for (int i = 1; i < A.length; i++) {
                 if (A[i] == A[i - 1]) {
                     token++;
                     res -= A[i - 1];
                 } else {
                     int give = Math.min(token, A[i] - A[i - 1] - 1);
                     token -= give;
                     res += give * A[i - 1] + (give + 1) * give / 2;
                 }
             }
             if (token > 0) {
                 res += token * A[A.length - 1] + (token + 1) * token / 2;
             }
             return res;
         }
     }
     ```

     