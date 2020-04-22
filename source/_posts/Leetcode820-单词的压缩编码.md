---
title: Leetcode820.单词的压缩编码
date: 2020-03-28 15:20:19
categories: Leetcode
tags:
  - 字典树
  - 字符串
---

## Leetcode820.单词的压缩编码

给定一个单词列表，我们将这个列表编码成一个索引字符串 S 与一个索引列表 A。

例如，如果这个列表是 ["time", "me", "bell"]，我们就可以将其表示为 S = "time#bell#" 和 indexes = [0, 2, 5]。

对于每一个索引，我们可以通过从字符串 S 中索引的位置开始读取字符串，直到 "#" 结束，来恢复我们之前的单词列表。

那么成功对给定单词列表进行编码的最小字符串长度是多少呢？

 [题目链接](https://leetcode-cn.com/problems/short-encoding-of-words/ )

<!--more-->

示例：

> 输入: words = ["time", "me", "bell"]
> 输出: 10
> 说明: S = "time#bell#" ， indexes = [0, 2, 5] 。

提示：

> 1 <= words.length <= 2000
> 1 <= words[i].length <= 7
> 每个单词都是小写字母 。



#### 思路

如果单词`x`是单词`Y`的后缀，那么编码字符串中就不必考虑`X`了，因为`Y`中一定包含`X`，在编码`Y`时就自动把`X`编码了。例如：`"time"`就自动编码了`"me"`。

如果单词`Y`不在任何别的单词`X`中出现，那么`Y`一定是编码字符的一部分。

单词`Y`的部分出现在其他单词的情况，无法满足，因为从索引开始读字符串，直到`#`结束。例如：`time`和`mell`，只可能为`time#mell`。

所以，保留所有不是其他单词后缀的字符，结果就是这些单词长度+1的总和。

#### 代码实现

```java
class Solution {
    public int minimumLengthEncoding(String[] words) {
        Set<String> set = new HashSet<>(Arrays.asList(words));
        for (String word : words) {
            //要从1开始，否则会把set中所有的单词都删除
            for (int i = 1; i < word.length(); i++) {
                set.remove(word.substring(i));
            }
        }
        int res = 0;
        for (String word : set) {
            res += word.length() + 1;
        }
        return res;
    }
}
```

