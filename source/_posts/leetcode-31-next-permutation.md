---
title: LeetCode 31. Next Permutation
date: 2017-04-15 22:31:34
categories: LEARNING
tags: [LeetCode, Algorithm]
---
[题目链接](https://leetcode.com/problems/next-permutation/#/description)

<!--more-->

乍一看没啥头绪 - - ，于是参考了 C++ 的 `std::next_permutation()` 的实现和[这个问题下排名第一的仁兄的答案](http://stackoverflow.com/questions/11483060/stdnext-permutation-implementation-explanation)。

1 2 3 4 **5**
1 2 3 **5 4**
1 2 4 3 **5**
1 2 4 **5 3**
1 2 5 3 **4**
1 2 **5 4 3**
…

观察发现，假设某个元素的 index 为 k，当 nums[k, …, nums.length - 1] 不递增时，nums[k - 1] 需要改变为一个更大的数，即改变为 nums[k, …, nums.length - 1] 中恰大于 nums[k - 1] 的数。交换这两个数，并 reverse nums[k, …, nums.length - 1]。注意判重就好。

```java
public class Solution {
    public void nextPermutation(int[] nums) {
        if(nums.length == 0) return; // no such cases
        int len = nums.length;
        int k = len - 1;
        // everything to the right of nums[k - 1] is in nonincreasing order
        while(k > 0 && nums[k] <= nums[k - 1]) // avoid duplication
            k--;
        if(k == 0)
            reverse(nums, 0, len - 1);
        else {
            int i = 0;
            // find the number just larger than nums[k - 1]
            for(i = len - 1; ; i--) {
                if(nums[i] > nums[k - 1])
                    break;
            }
            // then swap them
            swap(nums, k - 1, i);
            reverse(nums, k, len - 1);
        }
    }
    
    private void reverse(int[] nums, int l, int r) {
        while(l < r)
            swap(nums, l++, r--);
    }
    
    private void swap(int[] nums, int i, int j) {
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```