---
title: Leetcode912排序数组
date: 2020-03-31 10:17:41
categories: Leetcode
tags:
  - Array
  - 排序
---

## Leetcode912排序数组

给你一个整数数组 nums，将该数组升序排列。

 [题目链接](https://leetcode-cn.com/problems/sort-an-array)

<!--more-->

示例 1：

> 输入：nums = [5,2,3,1]
> 输出：[1,2,3,5]

示例 2：

> 输入：nums = [5,1,1,2,0,0]
> 输出：[0,0,1,1,2,5]


提示：

> 1 <= nums.length <= 50000
> -50000 <= nums[i] <= 50000

### 思路：八大排序

1. **冒泡排序**

   ```java
   class Solution {
       public int[] sortArray(int[] nums) {
           for (int i = 0; i < nums.length; i++) {
               for (int j = i + 1; j < nums.length; j++) {
                   if (nums[i] > nums[j]) {
                       int t = nums[i];
                       nums[i] = nums[j];
                       nums[j] = t;
                   }
               }
           }
           return nums;
       }
   }
   ```

   **时间复杂度为`O(n^2)`**，超出时间限制。

2. **选择排序**

   ```java
   class Solution {
       public int[] sortArray(int[] nums) {
           for (int i = 0; i < nums.length; i++) {
               //k记录当前下标
               int k = i;
               for (int j = i + 1; j < nums.length; j++) {
                   if (nums[k] > nums[j]) {
                       //k记录最小值下标
                       k = j;
                   }
               }
               //swap
               int temp = nums[i];
               nums[i] = nums[k];
               nums[k] = temp;
           }
           return nums;
       }
   }
   ```

   **时间复杂度为`O(n^2)`**，能通过但效率极低。

   ![通过截图](https://f1bu920.github.io/images/选择排序.png)

3. **插入排序**

   ```java
   class Solution {
       public int[] sortArray(int[] nums) {
           int N = nums.length;
           for (int i = 1; i < N; i++) {
               //交换次数较多
               for (int j = i; j > 0 && nums[j] < nums[j - 1]; j--) {
                   int temp = nums[j - 1];
                   nums[j - 1] = nums[j];
                   nums[j] = temp;
               }
           }
           return nums;
       }
   }
   ```

   **时间复杂度`O(n^2)`**，插入排序的性能取决于原始数组的排序程度，若原始数组只有少部分乱序，插入排序的性能很好。若原数组是大规模的乱序，插入排序会很慢，因为它只会交换相邻的元素。例如，在升序排序中，若最小值在数组最后面，则要将其移动`N-1`次。

4. **希尔排序**

   ```java
   class Solution {
       public int[] sortArray(int[] nums) {
           int N = nums.length;
           int h = 1;
           while (h < N / 3) {
               h = h * 3 + 1;
           }
           while (h >= 1) {
               //将数组变为h有序
               for (int i = h; i < N; i++) {
                   //将nums[i]插入nums[i-2*h]、nums[i-3*h]、nums[i-4*h]中
                   for (int j = i; j >= h && nums[j] < nums[j - h]; j -= h) {
                       int temp = nums[j - h];
                       nums[j - h] = nums[j];
                       nums[j] = temp;
                   }
               }
               h /= 3;
           }
           return nums;
       }
   }
   ```

   希尔排序是基于插入排序的快速排序算法，也称**递减增量排序算法**，它会优先比较距离较远的元素。

   对于每个h，用插入排序对h个子数组进行独立的排序。但因为子数组是相互独立的，只要在h-子数组中将每个元素交换到比它大的元素之前去，我们只需要在插入排序中将移动元素的距离由1改为h即可。这样，希尔排序就转变成了一个类似于插入排序但是有不同增量的过程。

5. **归并排序**

   - 自顶向下的递归

   ```java
   class Solution {
       int[] temp;
   
       public int[] sortArray(int[] nums) {
           int N = nums.length;
           temp = new int[N];
           mergeSort(nums, 0, N - 1);
           return nums;
       }
   
       private void mergeSort(int[] nums, int l, int r) {
           if (l >= r) return;
           int mid = (l + r) >> 1;
           mergeSort(nums, l, mid);
           mergeSort(nums, mid + 1, r);
           int i = l, j = mid + 1;
           int k = 0;
           while (i <= mid && j <= r) {
               if (nums[i] < nums[j]) {
                   temp[k++] = nums[i++];
               } else {
                   temp[k++] = nums[j++];
               }
           }
           while (i <= mid) {
               temp[k++] = nums[i++];
           }
           while (j <= r) {
               temp[k++] = nums[j++];
           }
           for (int m = 0; m < r - l + 1; m++) {
               nums[m + l] = temp[m];
           }
       }
   }
   ```

   递归实现的归并排序是分治思想的典型应用，我们将一个大问题分割成一个个小问题解决，然后用所有小问题的答案来解决整个大问题。

   - 自底向上的归并排序

     ```java
     class Solution {
         int[] temp;
     
         public int[] sortArray(int[] nums) {
             int N = nums.length;
             temp = new int[N];
             //sz为子数组大小
             for (int sz = 1; sz < N; sz = sz * 2) {
                 //lo为子数组索引
                 for (int lo = 0; lo < N - sz; lo += sz * 2) {
                     merge(nums, lo, lo + sz - 1, Math.min(N - 1, lo + sz + sz - 1));
                 }
             }
             return nums;
         }
     
         private void merge(int[] nums, int lo, int mid, int hi) {
             int i = lo, j = mid + 1;
             for (int k = lo; k <= hi; k++) {
                 temp[k] = nums[k];
             }
             for (int k = lo; k <= hi; k++) {
                 if (i > mid) {
                     nums[k] = temp[j++];
                 } else {
                     if (j > hi) {
                         nums[k] = temp[i++];
                     } else {
                         if (temp[j] < temp[i]) {
                             nums[k] = temp[j++];
                         } else {
                             nums[k] = temp[i++];
                         }
                     }
                 }
             }
         }
     }
     ```

     ![归并排序的非递归实现](https://f1bu920.github.io/images/归并排序.png)

     

6. **快速排序**

   ```java
   class Solution {
       private int partition(int[] nums, int lo, int hi) {
           int i = lo, j = hi + 1;
           int t = nums[lo];
           while (true) {
               while (nums[++i] < t) {
                   if (i == hi)
                       break;
               }
               while (nums[--j] > t) {
                   if (j == lo)
                       break;
               }
               if (i >= j) break;
               int temp = nums[j];
               nums[j] = nums[i];
               nums[i] = temp;
           }
           int temp = nums[lo];
           nums[lo] = nums[j];
           nums[j] = temp;
           return j;
       }
   
       private void quickSort(int[] nums, int lo, int hi) {
           if (lo >= hi) return;
           //切分
           int j = partition(nums, lo, hi);
           //将左半部分排序
           quickSort(nums, lo, j - 1);
           //将右半部分排序
           quickSort(nums, j + 1, hi);
       }
   
       public int[] sortArray(int[] nums) {
           quickSort(nums, 0, nums.length - 1);
           return nums;
       }
   }
   ```
   快速排序是一种基于分治的思想，它将数组分为前后两个子数组，并确定一个基准，使得第一个数组中元素全部不大于基准，第二个数组中元素全部不小于基准，再递归调用就可以完成整体排序。

   关于基准的选择有很多种方法，这里只是效率最差的固定切分法，还有随机切分法和三取样切分法。

   **快速排序的均摊时间复杂度为O(n logn).**

   

7. **堆排序**

   ```java
   class Solution {
       public void heapSort(int[] nums) {
           //构建大顶堆
           for (int i = nums.length / 2 - 1; i >= 0; --i) {
               //从第一个非叶子节点从下到上、从右到左调整结构
               adjustHeap(nums, i, nums.length);
           }
           //调整堆结构+交换堆顶元素与末尾元素
           for (int j = nums.length - 1; j > 0; j--) {
               //将堆顶元素与末尾元素进行交换
               int temp = nums[0];
               nums[0] = nums[j];
               nums[j] = temp;
               //重新对堆进行调整
               adjustHeap(nums, 0, j);
           }
       }
   
       public void adjustHeap(int[] nums, int i, int length) {
           //取出当前元素
           int temp = nums[i];
           //从i节点的左子节点开始，也就是从2*i+1处开始
           for (int k = 2 * i + 1; k < length; k = 2 * k + 1) {
               //如果左子节点小于右子节点，k指向右子节点
               if (k + 1 < length && nums[k] < nums[k + 1]) {
                   k++;
               }
               //如果子节点大于父节点，将子节点的值赋给父节点（不用进行交换）
               if (nums[k] > temp) {
                   nums[i] = nums[k];
                   i = k;
               } else {
                   break;
               }
           }
           //将temp的值放到最终的位置l
           nums[i] = temp;
       }
   
       public int[] sortArray(int[] nums) {
           heapSort(nums);
           return nums;
       }
   }
   ```

   堆排序的思想就是先将待排序的序列建成大根堆，使得每个父节点的元素大于等于它的子节点。此时整个序列最大值即为堆顶元素，我们将其与末尾元素交换，使末尾元素为最大值，然后再调整堆顶元素使得剩下的 n-1n−1 个元素仍为大根堆，再重复执行以上操作我们即能得到一个有序的序列。基本思路如下：

   1. **将无序序列构建成一个堆，根据升降需求选择大顶堆或小顶堆，升序选择大顶堆，降序选择小顶堆**。
   2. **将堆顶元素与末尾元素交换，将最大元素"沉"到数组末端** 。
   3. **重新调整结构，使其满足堆定义，然后继续交换堆顶元素与当前末尾元素，反复执行调整+交换步骤，直到整个序列有序** 。



