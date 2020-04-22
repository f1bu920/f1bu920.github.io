---
title: Leetcode409.最长回文串
date: 2020-03-19 11:06:39
categories: Leetcode
tags:
  - String
---

## Leetcode409.最长回文串

给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。

在构造过程中，请注意区分大小写。比如 `"Aa"` 不能当做一个回文字符串。

**注意:**
假设字符串的长度不会超过 1010。

[题目链接](https://leetcode-cn.com/problems/longest-palindrome/ )

<!--more-->

**示例 1:**

```
输入:
"abccccdd"

输出:
7

解释:
我们可以构造的最长的回文串是"dccaccd", 它的长度是 7。
```

- 思路

  回文串正着和反着相同，所以有一个回文中心作为分界线，左右两边对称分布。从`abbcbba`可知一个回文串最多有一个字符出现奇数次，其他全为偶数次。所以我们构建最长回文串时，可以将每个字符使用偶数次，如果有奇数个字符，我们取出一个且只能取出一个作为回文中心。

- 代码实现

  ```java
  class Solution {
      public int longestPalindrome(String s) {
          int res = 0;
          Map<Character, Integer> map = new HashMap<>();
          for (int i = 0; i < s.length(); i++) {
              char c = s.charAt(i);
              if (map.containsKey(c)) {
                  map.put(c, map.get(c) + 1);
              } else {
                  map.put(c, 1);
              }
          }
          for (Map.Entry<Character, Integer> entry : map.entrySet()) {
              //将每个字符使用偶数次，res为偶数
              res += entry.getValue() / 2 * 2;
              //遇到奇数个字符时res++，此时res变为奇数，不会再++了
              if (res % 2 == 0 && entry.getValue() % 2 == 1) {
                  res++;
              }
          }
          return res;
      }
  }
  ```

  

