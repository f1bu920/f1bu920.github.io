---
title: Leetcode402.移掉K位数字
date: 2020-03-12 14:26:44
categories: Leetcode
tags: 
  - Stack
  - Java
---

## Leetcode402.移掉K位数字

给定一个以字符串表示的非负整数 num，移除这个数中的 k 位数字，使得剩下的数字最小。

注意:

num 的长度小于 10002 且 ≥ k。

num 不会包含任何前导零。

[题目链接](https://leetcode-cn.com/problems/remove-k-digits)

<!--more-->

示例 1 :

> 输入: num = "1432219", k = 3
>
> 输出: "1219"
> 解释: 移除掉三个数字 4, 3, 和 2 形成一个新的最小的数字 1219。

示例 2 :

> 输入: num = "10200", k = 1
>
> 输出: "200"
> 解释: 移掉首位的 1 剩下的数字为 200. 注意输出不能有任何前导零。

示例 3 :

> 输入: num = "10", k = 2
>
> 输出: "0"
> 解释: 从原数字移除所有的数字，剩余为空就是0。

```java
class Solution {
    public String removeKdigits(String num, int k) {
         LinkedList<Character> stack = new LinkedList<>();
        for (char c:num.toCharArray()){
            //利用了&&的截断，避免了栈中首个元素peek时为空
            while (k>0  && stack.size()>0 && stack.peekLast()>c){
                k--;
                stack.removeLast();
            }
            stack.addLast(c);
        }
        //防止递增数列情况，除去末尾的大数字
        for (int i = 0 ;i<k;i++){
            stack.removeLast();
        }
        StringBuilder ret = new StringBuilder();
        //除去前导零
        boolean leadingZero = true;
        for (char c : stack){
            if (leadingZero && c=='0'){
                continue;
            }
            leadingZero = false;
            ret.append(c);
        }
        //栈为空时返回0
        if (ret.length()==0) return "0";
        return ret.toString();
    }
}
```

- 思路

  **1. 给定一个数字序列[D1 D2 D3...Dn]，如果数字D2小于其左邻居D1，则我们应该删除左邻居(D1)，以获得最小结果。对于每个数字，如果该数字小于栈顶元素，即该数字的左邻居，则弹出堆栈，即删除左邻居，否则推入堆栈。重复上述步骤，直至堆栈为空或者已经删除K位数字。**

  **2.但对于某些情况，规则对任意数字都不适用，即单调递增数列，对此我们只需要删除末尾数字即可。**

  

