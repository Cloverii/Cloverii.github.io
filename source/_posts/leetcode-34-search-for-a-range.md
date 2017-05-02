---
title: 两道二分题 LeetCode 33 & 34
date: 2017-04-21 23:46:26
categories: LEARNING
tags: [Algorithm, LeetCode]
---
**Keywords: Binary Search**

### 基本姿势 lower_bound && upper_bound
[34. 题目链接](https://leetcode.com/problems/search-for-a-range/#/description)

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
        while(l < r) { // [l, r)
            int m = l + (r - l) / 2;
            if(target >= nums[m]) l = m + 1;
            else r = m;
        }
        return l;
    }
    
    private int lowerBound(int[] nums, int l, int r, int target) {
        while(l < r) { // [l, r)
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
两函数均为左闭右开，借鉴 C++ STL 的风格。

显然当 target 存在于 nums 中时， 答案是 `[lowerBound(), upperBound() - 1]`。否则 `lowerBound() = upperBound()`，故只要判断 `ans[0] > ans[1]` 就说明 target 不存在了。

刚写完就 T 了我的内心很崩溃……最后发现是 `int m = l + (r - l) >> 1;`写蠢了，真的被自己蠢哭……括号应该打成 `(r - l >> 1）` 啊。既然是写 LC，而且用的还是 Java，以后还是少写这种风格的代码好了。

感谢韬韬的教学，虽然我A了之后他还WA着……

trick1: 
`int m = l + (r - l) / 2`
`int m = l + r >> 1` 有溢出风险

trick2: 
`nums[m] <= target`，target 写在右边
为了 C++ 的范型？向 STL 看齐？but 我现在好像是在写 Java…以及以 target 为基准判断更自然嘛，所以我还是选择不这么写 [冷漠.jpg]

### 进阶理解
[33. Search in Rotated Sorted Array 题目链接](https://leetcode.com/problems/search-in-rotated-sorted-array)
> Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.
>
> (i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).
>
> You are given a target value to search. If found in the array return its index, otherwise return -1.
>
> You may assume no duplicate exists in the array.

一开始想的是先二分一遍找出 privot，再对两边分别二分，写了整整 30 行 - -。其实一开始就隐隐约约感觉一次二分就可以了，就是不想写。但是三次二分写完才发现太挫了 - - ，所以还是强行重新写。

```java
public class Solution {
    public static int search(int[] nums, int target) {
        if(nums == null || nums.length == 0) return -1;
        int l = 0, r = nums.length - 1; // [l,r]
        while(l < r) {
            int m = l + (r - l) / 2;
            if(nums[l] <= nums[m]) {
                if(target >= nums[l] && target <= nums[m]) r = m;
                else l = m + 1;
            } else {
                if(target > nums[m] && target <= nums[r]) l = m + 1;
                // 为了维持 l = m + 1 & r = m 的习惯性写法，target > nums[m] 没有 =
                else r = m ;
            }
        }
        if(nums[l] != target) l = -1; // nums.length can't be 0
        return l;
    }
}
```
二分后，两边必有一部分是整体有序的，如果数据在有序的范围内，就可以做普通二分了，否则继续强行分再判断哪边有序。

注意这里我没有用常见的左闭右开 [l,r) 的写法，而是整个闭区间 [l, r]。因为一开始习惯性写左闭右开遇到了一些问题，写得不那么优雅，干脆换成闭区间了。
判断中也要注意 `=` 和 `l = m + 1` 的问题。

应该可以再进一步简化代码，把判断条件堆在一起，不过现在越来越觉得还是可读性比较重要了，为了一点点看上去的优雅牺牲可读性意义不大。

至于很挫的代码在[这里的search1()](https://github.com/Cloverii/LeetCode/blob/master/33**.java)。