---
title: Leetcode1160.拼写单词
date: 2020-03-17 15:08:55
categories: Leetcode
tags:
  - 哈希表
---

## Leetcode1160.拼写单词

给你一份『词汇表』（字符串数组） words 和一张『字母表』（字符串） chars。

假如你可以用 chars 中的『字母』（字符）拼写出 words 中的某个『单词』（字符串），那么我们就认为你掌握了这个单词。

注意：每次拼写时，chars 中的每个字母都只能用一次。

返回词汇表 words 中你掌握的所有单词的 长度之和。

 [题目链接](https://leetcode-cn.com/problems/find-words-that-can-be-formed-by-characters)

<!--more-->

示例 1：

> 输入：words = ["cat","bt","hat","tree"], chars = "atach"
>
> 输出：6
> 解释： 
> 可以形成字符串 "cat" 和 "hat"，所以答案是 3 + 3 = 6。

示例 2：

> 输入：words = ["hello","world","leetcode"], chars = "welldonehoneyr"
>
> 输出：10
> 解释：
> 可以形成字符串 "hello" 和 "world"，所以答案是 5 + 5 = 10。


提示：

> 1 <= words.length <= 1000
>
> 1 <= words[i].length, chars.length <= 100
> 所有字符串中都仅包含小写英文字母

- 思路

  很明显可以利用哈希表来做，统计mapChar是否覆盖mapWord。

  也可以统计26个字母出现次数来判断。

- 代码实现

  ```java
  public class Solution {
      public int countCharacters(String[] words, String chars) {
          //结果
          int lenSum = 0;
          Map<Character, Integer> mapWord = new HashMap<>();
          Map<Character, Integer> mapChar = new HashMap<>();
          for (int i = 0; i < chars.length(); i++) {
              char c = chars.charAt(i);
              if (mapChar.containsKey(c)) {
                  int t = mapChar.get(c);
                  mapChar.put(c, t + 1);
              } else {
                  mapChar.put(c, 1);
              }
          }
          for (String word : words) {
              for (int i = 0; i < word.length(); i++) {
                  char c = word.charAt(i);
                  if (mapWord.containsKey(c)) {
                      int t = mapWord.get(c);
                      mapWord.put(c, t + 1);
                  } else {
                      mapWord.put(c, 1);
                  }
              }
              boolean flag = true;
              for (Map.Entry<Character, Integer> entry : mapWord.entrySet()) {
                  char ck = entry.getKey();
                  if (!(mapChar.containsKey(ck) && entry.getValue() <= mapChar.get(ck))) {
                      flag = false;
                  }
              }
              lenSum += (flag == true) ? word.length() : 0;
              //记得每次遍历完一次word后要把mapWord清空
              mapWord.clear();
          }
          return lenSum;
      }
  }
  ```

  