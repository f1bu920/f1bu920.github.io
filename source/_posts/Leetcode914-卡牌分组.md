---
title: Leetcode914.卡牌分组
date: 2020-03-27 13:06:53
categories: Leetcode
tags:
  - 递归
  - 最大公约数
---

## Leetcode914.卡牌分组

给定一副牌，每张牌上都写着一个整数。

此时，你需要选定一个数字 X，使我们可以将整副牌按下述规则分成 1 组或更多组：

每组都有 X 张牌。
组内所有的牌上都写着相同的整数。
仅当你可选的 X >= 2 时返回 true。

 [题目链接](https://leetcode-cn.com/problems/x-of-a-kind-in-a-deck-of-cards)

<!--more-->

示例 1：

> 输入：[1,2,3,4,4,3,2,1]
>
> 输出：true
> 解释：可行的分组是 [1,1]，[2,2]，[3,3]，[4,4]

示例 2：

> 输入：[1,1,1,2,2,2,3,3]
> 输出：false
> 解释：没有满足要求的分组。

示例 3：

> 输入：[1]
>
> 输出：false

示例 4：

> 输入：[1,1]
> 输出：true
> 解释：可行的分组是 [1,1]

示例 5：

> 输入：[1,1,2,2,2,2]
> 输出：true
> 解释：可行的分组是 [1,1]，[2,2]，[2,2]

提示：

> 1 <= deck.length <= 10000
> 0 <= deck[i] < 10000

#### 思路及代码实现

统计每个元素出现次数，使用`HashMap`存储。将数组分成1组或更多组，每组有2张以上的牌，所以我们可知只需求出现次数的最大公约数即可，若最大公约数大于等于2，则`return true`，否则`return false`。

#### 代码实现

```java
class Solution {
    public boolean hasGroupsSizeX(int[] deck) {
        if (deck == null || deck.length == 0 || deck.length == 1) {
            return false;
        }
        int x = 0;
        Map<Integer, Integer> map = new HashMap<>();
        for (int d : deck) {
            if (map.containsKey(d)) {
                map.put(d, map.get(d) + 1);
            } else {
                map.put(d, 1);
            }
        }
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            if (x == 0) {
                x = entry.getValue();
            } else {
                x = gcd(x, entry.getValue());
            }
        }
        return x >= 2;
    }

    private int gcd(int p, int q) {
        if (q == 0) {
            return p;
        }
        int r = p % q;
        return gcd(q, r);
    }
}
```

