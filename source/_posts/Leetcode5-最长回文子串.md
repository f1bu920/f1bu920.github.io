---
title: Leetcode5.最长回文子串
date: 2020-04-02 15:20:57
categories: Leetcode
tags:
  - String
  - 动态规划
  - 扩展中心
---

## Leetcode5.最长回文子串

给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

[题目链接](https://leetcode-cn.com/problems/longest-palindromic-substring)

<!--more-->

示例 1：

> 输入: "babad"
> 输出: "bab"
> 注意: "aba" 也是一个有效答案。

示例 2：

> 输入: "cbbd"
> 输出: "bb"

#### 思路

- 暴力法

  两个指针遍历整个字符串找到全部子串中的最大回文串。超出时间限制。

- 动态规划

  ![动态规划问题的思考方向](https://f1bu920.github.io/images/动态规划问题的思考方向.png)

  [参考链接](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-dong-tai-gui-hua-by-liweiwei1419/ )

  定义状态：`dp[i][j]`表示字符串`s[i,j]`是否是回文子串。

  状态转移方程：`dp[i][j] = (s[i]==s[j]) and dp[i+1][j-1]`。

  此题中`i<j`，边界情况：表达式`[i+1,j-1]`不构成区间，即长度严格小于2，所以`(j-1)-(i+1)+1<1`，得`j-i<3`。

- 扩展中心算法

  因为回文串一定是对称的，所以我们可以每次选择一个中心进行左右扩展，判断字符是否相等。

  由于存在奇数个和偶数个的字符串，所以我们要从一个字符或者两个字符之间开始扩展，即共有`n+n-1`个中心。

#### 代码实现

- 暴力法

  ```java
      public String longestPalindrome(String s) {
          if (s == null || s.length() < 1) {
              return "";
          }
          int start = 0;
          int end = 0;
          int max = 0;
          for (int i = 0; i < s.length(); i++) {
              for (int j = i; j < s.length(); j++) {
                  String subStr = s.substring(i, j + 1);
                  if (isPalindrome(subStr)) {
                      if ((j - i) >= max) {
                          max = j - i;
                          start = i;
                          end = j;
                      }
                  }
              }
          }
          return s.substring(start, end + 1);
      }
  
      boolean isPalindrome(String s) {
          if (s.length() < 1 || s == null) {
              return false;
          }
          StringBuilder sb = new StringBuilder(s);
          StringBuilder reverseS = sb.reverse();
          return s.equals(reverseS.toString());
      }
  ```

  超出时间限制

-  动态规划法

  ```java
  class Solution {
      public String longestPalindrome(String s) {
          if (s == null || s.length() < 1) {
              return "";
          }
          int start = 0, end = 0;
          int max = 1;
          int N = s.length();
          //对角线上全为单个字符，都是回文串
          boolean[][] dp = new boolean[N][N];
          for (int i = 0; i < N; i++) {
              dp[i][i] = true;
          }
          for (int j = 1; j < N; j++) {
              for (int i = 0; i < j; i++) {
                  if (s.charAt(i) == s.charAt(j)) {
                      //当前子串元素个数小于3个时一定为回文串，例"bab"，j-i<3
                      if (j - i < 3) {
                          dp[i][j] = true;
                      } else {
                          dp[i][j] = dp[i + 1][j - 1];
                      }
                  } else {
                      dp[i][j] = false;
                  }
                  //当前子串为回文串，记录
                  if (dp[i][j]) {
                      int curlen = j - i + 1;
                      if (curlen > max) {
                          max = curlen;
                          start = i;
                          end = j;
                      }
                  }
              }
          }
          return s.substring(start, end + 1);
      }
  }
  ```

  

- 扩展中心法

  ```java
  class Solution {
      public String longestPalindrome(String s) {
          if (s == null || s.length() < 1) {
              return "";
          }
          int start = 0, end = 0;
          int N = s.length();
          for (int i = 0; i < N; i++) {
              int len1 = expandCenter(s, i, i);
              int len2 = expandCenter(s, i, i + 1);
              int len = Math.max(len1, len2);
              if (len > end - start) {
                  //注意这里要(len-1)/2,防止偶数情况下最长长度中心在两点之间的情况
                  //例"cbaabd",len/2时结果为abaab
                  start = i - (len - 1) / 2;
                  end = i + len / 2;
              }
          }
          return s.substring(start, end + 1);
      }
  
      private int expandCenter(String s, int left, int right) {
          int L = left;
          int R = right;
          while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
              L--;
              R++;
          }
          //这里是-1，因为本来是L-R+1，但因为最后L多减了一次，R多加了一次，所以要-2.
          return R - L - 1;
      }
  }
  ```

  