---
title: Leetcode面试题57-II.和为s的连续正数序列
date: 2020-03-14 15:27:59
categories: Leetcode
tags: 
  - Java
  - Sliding Window
---

## Leetcode面试题57-II.和为s的连续正数序列

输入一个正整数 `target `，输出所有和为 `target` 的连续正整数序列（至少含有两个数）。

序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

[题目链接](https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof)

<!--more-->

示例 1：

> 输入：target = 9
>
> 输出：[[2,3,4],[4,5]]

示例 2：

> 输入：target = 15
>
> 输出：[[1,2,3,4,5],[4,5,6],[7,8]]


限制：

> 1 <= target <= 10^5

### 滑动窗口

滑动窗口可以看成数组中框起来的一部分，我们可以用滑动窗口来观察可能的结果，当滑动窗口从数组的左边滑倒了右边，就可以得到所有可能的结果。

设窗口左边界为i，右边界为j，则滑动窗口是`[i,j)`，设窗口的和为`sum`。

- 当`sum>target`时，窗口的和需要减小，所以要缩小窗口，左边界右移，即`i++`
- 当`sum<target`时，窗口的和需要增大，所以要增大窗口，右边界右移，即`j++`
- 当`sum==target`时，记录结果，接下来找`i+1`开头的序列，所以·`sum-=i,i++`

代码实现：

```java
class Solution {
    public int[][] findContinuousSequence(int target) {
         List<int[]> res = new ArrayList<>();
        int i = 1;//左窗口
        int j = 1;//右窗口
        int sum = 0;
        while (i<(target-1)/2){
            if (sum>target){
                sum-=i;
                i ++;
            }if (sum<target){
                sum+=j;
                j++;
            }
            if (sum==target){
                int arr[] = new int[j-i];
                for (int k =i;k<j;k++){
                    arr[k-i] = k;
                }
                res.add(arr);
                sum-=i;
                i++;
            }
        }
        return res.toArray(new int[res.size()][]);
    }
}
```





