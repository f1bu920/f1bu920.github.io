---
title: Leetcode20.有效的括号
date: 2020-03-11 15:21:57
categories: Leetcode
tags:
  - Stack
  - Java
---

## Leetcode20.有效的括号

[题目链接](https://leetcode-cn.com/problems/valid-parentheses/ )

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

<!--more-->

示例 1:

输入: 

> "()"

输出: 

> true

示例 2:

输入: 

> "()[]{}"

输出:

> true

示例 3:

输入:

> "(]"

输出:

> false

示例 4:

输入:

> "([)]"

输出:

> false

示例 5:

输入: 

> "{[]}"

输出:

> true

```java
import java.util.Stack;
public class Solution {
    public boolean isValid(String s){
        if (s.equals("")||s == null){
            return true;
        }
        //构造栈
        Stack stack = new Stack();
        for (int i =0;i<s.length();i++){
            char c = s.charAt(i);
            //左括号入栈
            if (c == '('||c=='['||c=='{'){
                stack.push(c);
            }
            //右括号
            else {
                //排除右括号入栈时栈空情况，例："]"
                 if (stack.isEmpty()){
                    return false;
                }
                if (c==')'&&((Character)stack.pop())!='('){
                    return false;
                }
                if (c==']'&&((Character)stack.pop())!='['){
                    return false;
                }
                if (c=='}'&&((Character)stack.pop())!='{'){
                    return false;
                }
            }
        }
        //最后栈空则匹配
        if (stack.isEmpty()){
            return true;
        }
        return false;
    }
}
```

