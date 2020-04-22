---
title: Leetcode56.合并区间
date: 2020-04-16 09:02:02
categories: Leetcode
tags:
  - Array
  - sort
---

## Leetcode56.合并区间

给出一个区间的集合，请合并所有重叠的区间。

[题目链接](https://leetcode-cn.com/problems/merge-intervals)

<!--more-->

示例 1:

>输入: [[1,3],[2,6],[8,10],[15,18]]
>输出: [[1,6],[8,10],[15,18]]
>解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].

示例 2:

>输入: [[1,4],[4,5]]
>输出: [[1,5]]
>解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。

#### 思路

根据区间的额左端点升序排序，遍历整个数组。

- 如果下一个区间的左端点在当前区间右端点的左边，即`intervals[i+1][0] <= intervals[i][1]`，则这两个区间可以合并。将下一个区间的左端点赋为当前区间的左端点，即`intervals[i+1][0] = intervals[i][0]`，将下一个区间的右端点赋为这两个区间右端点的最大值，即`intervals[i+1][1] = Math.max(intervals[i][1], intervals[i+1][1])`。
- 否则将当前区间添加到集合中



#### 代码实现

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        if (intervals == null || intervals.length <= 1) {
            return intervals;
        }
        Arrays.sort(intervals, Comparator.comparingInt(interval -> interval[0]));
        List<int[]> list = new ArrayList<>();
        for (int i = 0; i < intervals.length - 1; i++) {
            if (intervals[i + 1][0] <= intervals[i][1]) {
                intervals[i + 1][0] = intervals[i][0];
                intervals[i + 1][1] = Math.max(intervals[i + 1][1], intervals[i][1]);
            } else {
                list.add(intervals[i]);
            }
        }
        list.add(intervals[intervals.length - 1]);
        return list.toArray(new int[list.size()][2]);
    }
}
```

