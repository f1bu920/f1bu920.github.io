---
title: Leetcode151.翻转字符串里的单词
date: 2020-04-10 10:40:22
categories: Leetcode
tags:
  - String
  - 双指针
---

## Leetcode151.翻转字符串里的单词

给定一个字符串，逐个翻转字符串中的每个单词。

 [题目链接](https://leetcode-cn.com/problems/reverse-words-in-a-string)

<!--more-->

示例 1：

>输入: "the sky is blue"
>输出: "blue is sky the"

示例 2：

>输入: "  hello world!  "
>输出: "world! hello"
>解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。

示例 3：

>输入: "a good   example"
>输出: "example good a"
>解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。


说明：

>无空格字符构成一个单词。
>输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
>如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。



#### 思路

利用双指针除去空格、找到单词，再将单词添加到`list`集合中。



#### 代码实现

```java
class Solution {
    public String reverseWords(String s) {
        if (s == null || s.length() == 0) {
            return s;
        }
        String str = s.trim();
        StringBuilder sb = new StringBuilder();
        List<String> list = new ArrayList<>();
        char[] chars = str.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            if (chars[i] == ' ')
                continue;
            int j = i;
            while (j < chars.length && chars[j] != ' ') {
                j++;
            }
            list.add(str.substring(i, j));
            i = j;
        }
        for (int i = list.size() - 1; i >= 0; i--) {
            sb.append(list.get(i));
            if (i != 0) {
                sb.append(' ');
            }
        }
        return sb.toString();
    }
}
```

