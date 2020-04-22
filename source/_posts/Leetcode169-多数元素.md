---
title: LeetCode169.多数元素
date: 2020-03-15 18:09:55
categories: Leetcode
tags:
  - Array
  - Java
---

## LeetCode169.多数元素

给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

[题目链接](https://leetcode-cn.com/problems/majority-element)

<!--more-->

示例 1:

> 输入: [3,2,3]
>
> 输出: 3

示例 2:

> 输入: [2,2,1,1,1,2,2]
>
> 输出: 2

#### 解法一：哈希表

```java
 public class Solution {
    public int majorityElement(int[] nums) {
        HashMap<Integer,Integer> map = new HashMap<>();
        for (int num:nums){
            if (!map.containsKey(num)){
                map.put(num,1);
            }
            else {
                map.put(num,map.get(num)+1);
            }
        }
        Map.Entry<Integer,Integer> majorityEntry = null;
        for (Map.Entry<Integer,Integer> entry : map.entrySet()){
            if (majorityEntry == null||entry.getValue()>majorityEntry.getValue()){
                majorityEntry = entry;
            }
       }
        return majorityEntry.getKey();
    }
 }
```

#### 解法二：排序

因为题中说明了众数数目多余n/2，所以排序后n/2处一定为众数。

```java
Arrays.sort(nums);
return nums[nums.length/2];
```

### 解法三：投票算法

- 思路

  如果我们把众数记为1，其他数记为-1，全部加起来后，显然和大于0.

- 算法

  - 我们先维护一个候选众数`candidate`和它出现的次数`count`，初始时`candidate`为任意值，`count`为0.
  - 遍历数组元素，对于每个出现的元素`num`，如果判断`num`之前，`count`为0，将`num`的值赋给`candidate`。然后进行如下判断：
    - 若`num`与`candidate`相等，则`count++`
    - 如果`num`与`candidate`不等，则`count--`
  - 遍历完成后，`candidate`即为众数

- 代码实现：

  ```java
  class Solution {
      public int majorityElement(int[] nums) {
         //投票算法
          int candidate = 0;
          int count = 0;
          for (int num:nums){
              if (count == 0 ){
                  candidate = num;
              }
              count+=num==candidate? 1:-1;
          }
          return candidate;
      }
  }
  ```

  

