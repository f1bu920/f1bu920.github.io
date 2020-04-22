---
title: Leecode66.加一
date: 2019-11-27 19:36:30
categories: Leetcode
tags: 
  - Math
  - Java
---

### Leecode题解66.加一

[Leecode 66.加一](https://leetcode-cn.com/problems/plus-one/ )

给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

<!--more-->

示例 1:

> 输入: [1,2,3]
>
> 输出: [1,2,4]
>
> 解释: 输入数组表示数字 123。


示例 2:

> 输入: [4,3,2,1]
>
> 输出: [4,3,2,2]
> 解释: 输入数组表示数字 4321.

代码：

~~~java
class Solution {
    public int[] plusOne(int[] digits) {
        int len = digits.length;
        for(int i=len-1;i>=0;i--){
            digits[i]++;
            digits[i]%=10;
            if(digits[i]!=0) return digits;
        }
        digits = new int[len+1];
        digits[0]=1;
        return digits;
    }
}
~~~

思路:

**加一的所以有可能的情况就只有两种：**

**除 99 之外的数字加一；**
**数字 99。**
**加一得十进一位个位数为 00 加法运算如不出现进位就运算结束了且进位只会是一。**

**所以只需要判断有没有进位并模拟出它的进位方式，如十位数加 11 个位数置为 00，如此循环直到判断没有再进位就退出循环返回结果。**

**然后还有一些特殊情况就是当出现 9999、999999 之类的数字时，循环到最后也需要进位，出现这种情况时需要手动将它进一位。**

