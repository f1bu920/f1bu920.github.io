---
title: Leetcode1013.将数组分成相等的三个部分
date: 2020-03-16 17:05:38
categories: Leetcode
tags:
  - Array
---

## Leetcode1013.将数组分成相等的三个部分

给你一个整数数组 A，只有可以将其划分为三个和相等的非空部分时才返回 true，否则返回 false。

形式上，如果可以找出索引 i+1 < j 且满足 (A[0] + A[1] + ... + A[i] == A[i+1] + A[i+2] + ... + A[j-1] == A[j] + A[j-1] + ... + A[A.length - 1]) 就可以将数组三等分。

 [题目链接](https://leetcode-cn.com/problems/partition-array-into-three-parts-with-equal-sum)

<!--more-->

示例 1：

> 输出：[0,2,1,-6,6,-7,9,1,2,0,1]
>
> 输出：true
> 解释：0 + 2 + 1 = -6 + 6 - 7 + 9 + 1 = 2 + 0 + 1

示例 2：

> 输入：[0,2,1,-6,6,7,9,-1,2,0,1]
>
> 输出：false

示例 3：

> 输入：[3,3,6,5,-2,2,5,1,-9,4]
>
> 输出：true
> 解释：3 + 3 = 6 = 5 - 2 + 2 + 5 + 1 - 9 + 4


提示：

> 3 <= A.length <= 50000
>
> -10^4 <= A[i] <= 10^4

```java
class Solution {
    public boolean canThreePartsEqualSum(int[] A) {
        int sum = 0;
        for (int a : A) {
            sum += a;
        }
        int target = sum / 3;
        if (sum%3 != 0) {
            return false;
        }
        int temp = 0;
        int time = 0;
            for (int i = 0;i<A.length;i++) {
                temp += A[i];
                if(temp==target){
                    temp = 0;
                    time++;
                }
            }
        //防止[1,-1,1,-1,1,-1,1,-1]的情况下time>3
        return time>=3;
    }
}
```

