---
title: Leetcode1047.删除字符串中的所有相邻重复项
date: 2020-03-11 16:58:54
categories: Leetcode
tags: 
  - Stack
  - Java
---

## Leetcode1047.删除字符串中的所有相邻重复项

给出由小写字母组成的字符串 S，重复项删除操作会选择两个相邻且相同的字母，并删除它们。

在 S 上反复执行重复项删除操作，直到无法继续删除。

在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

 [题目链接](https://leetcode-cn.com/problems/remove-all-adjacent-duplicates-in-string)

<!--more-->

示例：

输入："abbaca"
输出："ca"
解释：
例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。


提示：

> 1 <= S.length <= 20000
>
> S 仅由小写英文字母组成。

```java
import java.util.Stack;
class Solution {
    public String removeDuplicates(String S) {
        //S为空时
        if (S.equals("")||S ==null){
            return S;
        }
        Stack<Character> stack = new Stack<>();
        //将第一个元素入栈，防止出现StackEmpty错误
        stack.push(S.charAt(0));
        for (int i = 1;i<S.length();i++){
            char c = S.charAt(i);
            //逐个遍历，与栈顶元素相同则出栈，不同就入栈
            if (!stack.isEmpty() &&c==((char)stack.peek())){
                stack.pop();
            }
            else {
                stack.push(c);
            }
        }
        //转化为String格式
        StringBuilder stringBuilder = new StringBuilder();
        for (char c:stack){
            stringBuilder.append(c);
        }
        return stringBuilder.toString();
    }
 }
```

