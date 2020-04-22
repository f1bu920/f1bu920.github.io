---
title: Leecode118.杨辉三角
date: 2020-3-10 12:29:30
categories: Leetcode
tags: Java
---

### Leetcode118. 杨辉三角

给定一个非负整数 numRows，生成杨辉三角的前 numRows 行。

在杨辉三角中，每个数是它左上方和右上方的数的和。

[杨辉三角](https://leetcode-cn.com/problems/pascals-triangle).

<!-- more -->

示例:

输入:

> 5

输出:

> [
>
> ​      [1],
>     [1,1],
>    [1,2,1],
>   [1,3,3,1],
>  [1,4,6,4,1]
> ]

```java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        //numRows
        if(numRows<=0){
            return result;
        }
        //第一行一定为1
        result.add(new ArrayList<>());
        result.get(0).add(1);
        //rowNum=1，即从第二行开始
        for(int rowNum = 1 ; rowNum < numRows ; rowNum++){
            //本行
            List<Integer> row = new ArrayList<>();
            //上一行
            List<Integer> preRow = result.get(rowNum-1);
            //一行中第一个为1
            row.add(1);
            for(int j = 1;j<rowNum;j++){
                row.add(preRow.get(j-1)+preRow.get(j));
            }
            //一行中最后一个为1
            row.add(1);
            result.add(row);
        }
        return result;
    }
}
```

