---
title: LeetCode 34. Search for a Range
date: 2017-04-21 23:46:26
categories: Learning
tags: [Algorithm, LeetCode]
---
**Keywords: Binary Search**
[题目链接](https://leetcode.com/problems/search-for-a-range/#/description)
> Given an array of integers sorted in ascending order, find the starting and ending position of a given target value.
>
> Your algorithm's runtime complexity must be in the order of *O*(log *n*).
>
> If the target is not found in the array, return `[-1, -1]`.
>
> For example,
> Given `[5, 7, 7, 8, 8, 10]` and target value 8,
> return `[3, 4]`.

<!--more-->
题意没啥可说的，先上代码：
```java
public class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] ans = new int[2];
        ans[0] = lowerBound(nums, 0, nums.length, target);
        ans[1] = upperBound(nums, 0, nums.length, target) - 1;
        if(ans[0] > ans[1]) ans[0] = ans[1] = -1;
        return ans;
    }
    private int upperBound(int[] nums, int l, int r, int target) {
        while(l < r) {
            int m = l + (r - l) / 2;
            if(target >= nums[m]) l = m + 1;
            else r = m;
        }
        return l;
    }
    
    private int lowerBound(int[] nums, int l, int r, int target) {
        while(l < r) {
            int m = l + (r - l) / 2;
            if(target > nums[m]) l = m + 1;
            else r = m;
        }
        return l;
    }
}
```
`upperBound()` 返回 > target 的第一个位置。因为 == 的时候加入右边了。最后一次满足 == 之后加了1，故返回的是第一个 > 的位置。
同理`lowerBound()` 返回 >= target 的第一个位置。
两函数均为左闭右开。

显然当 target 存在于 nums 中时， 答案是 `[lowerBound(), upperBound() - 1]`。否则 `lowerBound() = upperBound()`，故只要判断 `ans[0] > ans[1]` 就说明 target 不存在了。

刚写完就 T 了我的内心很崩溃……最后发现是 `int m = l + (r - l) >> 1;`写蠢了，真的被自己蠢哭……括号应该打成 `(r - l >> 1）` 啊。既然是写 LC，而且用的还是 Java，以后还是少写这种风格的代码好了。

感谢韬韬的教学，虽然我A了之后他还WA着……

trick1: `int m = l + (r - l) / 2`
`int m = l + r >> 1` 有溢出风险

trick2: `nums[m] <= target`，target 写在右边
为了 C++ 的范型？向 STL 看齐？but 我好像是在写 Java…以及以 target 为基准判断明明更自然啊，所以我还是选择不这么写 [冷漠.jpg]