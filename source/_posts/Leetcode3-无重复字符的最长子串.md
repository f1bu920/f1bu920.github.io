---
title: Leetcode3.无重复字符的最长子串
date: 2020-05-02 09:51:37
categories: Leetcode
tags:
  - 哈希表
  - String
  - Slide Window
  - 双指针
---

## Leetcode3.无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

[题目链接](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters)

<!--more-->

示例 1:

>输入: "abcabcbb"
>输出: 3 
>解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

示例 2:

>输入: "bbbbb"
>输出: 1
>解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

示例 3:

>输入: "pwwkew"
>输出: 3
>解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
>     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

### 思路

设置两个指针`left`和`right`，代表滑动窗口的左右临界。递增的枚举子串的起始位置，在每次枚举中，将左指针向右移动一格，表示我们枚举字符的起始位置，然后我们不断地向右移动右指针，直到出现重复字符。移动结束后，这个最长无重复字符的子串就是左、右指针对应的子串。

我们可以利用`HashSet`这种数据结构来判断是否出现重复字符。在左指针向右移动时，从哈希表中移除一个字符；在右指针向右移动时，向哈希表中添加一个字符。

### 代码实现

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s == null) {
            return 0;
        }
        int len = s.length();
        if (len < 2) {
            return len;
        }
        int left = 0, right = 0;
        int maxlen = 0;
        Set<Character> set = new HashSet<>(len);
        while (left < len) {
            if (left != 0) {
                set.remove(s.charAt(left - 1));
            }
            while (right < len && !set.contains(s.charAt(right))) {
                set.add(s.charAt(right));
                right++;
            }
            maxlen = Math.max(maxlen, right - left);
            left++;
        }
        return maxlen;
    }
}
```

