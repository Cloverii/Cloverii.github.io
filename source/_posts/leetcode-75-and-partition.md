---
title: LeetCode 75. Sort Colors 与 partition
date: 2017-05-18 19:55:34
categories: ALGORITHM
tags: LeetCode
mathjax: true
---

**Keywords: Two Pointers**

[题目链接](https://leetcode.com/problems/sort-colors/#/description)

> Given an array with *n* objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.
>
> Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.
>
> **Note:**
> You are not suppose to use the library's sort function for this problem.

<!--more-->

只看上面的描述的话，非常显然是计数排序。不过还有下面的 tips: 

> **Follow up:**
> A rather straight forward solution is a two-pass algorithm using counting sort.
> First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
>
> Could you come up with an one-pass algorithm using only constant space?

手滑点开了 `Show Tags`, 赫然有 `Two Pointers`, 猝不及防被剧透一脸……没办法看都看了只能写了。一开始虽然大致有个想法，两个指针 $l$ 和 $r$, $l$ 的左边全是 0, $r$ 的右边全是 1, 但是因为没想清楚 WA 了好几发……对着错误样例瞎改改到最后居然 A 了，达成成就：虽然不会但我能 AC. 下午上完实验课回来又理解了一下 AC 代码，总算是想明白了吧。

在下面的代码里，`while` 循环的每次迭代开始的时候，子数组 $nums[0..l]$ 中所有元素均为 0, 子数组 $nums[l+1..i-1]$ 中所有元素均为1, 子数组 $nums[r..nums.size() - 1]$ 中所有元素均为 2, 而数组的其他部分还没有遍历到。事实上我们已经得到了**[循环不变量（loop invariant）](https://zh.wikipedia.org/zh-hans/%E5%BE%AA%E7%8E%AF%E4%B8%8D%E5%8F%98%E9%87%8F)**。算导翻译成“循环不变式”但我觉得这个翻译容易把人带歪。接下来就没什么可说的了。

```c++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        // elements on the left of l (included) is 0,
        // and those on the right of r (included) is 2;
        // element between (l, i) is 1
        int l = -1, r = nums.size();
        int i = 0;
        while(i < r) { // nums[r] = 2 (or null)
            if(nums[i] == 2)
                swap(nums[--r], nums[i]);
            else {
                if(nums[i] == 0) 
                    swap(nums[++l], nums[i]);
                ++i;
            }
        }
    }
};
```

其实，这道题让我想起了快排的 `partition`. `swap` 的过程几乎一模一样吧。

```c++
void mySort(vector<int> &nums, int l, int r) { // [l, r)
    if(l >= r - 1) return;
    int p = partition(nums, l, r);
    mySort(nums, l, p);
    mySort(nums, p + 1, r);
}
int partition(vector<int> &nums, int l, int r) { // [l, r)
  	// always choose the last element as pivot
  	// those in thr range of nums[l..i] always <= nums[r-1];
  	// nums[i+1..j-1] > nums[r-1]
    // nums[j..r-2] not sure
    int x = nums[r - 1];
    int i = l - 1;
    for(int j = l; j < r - 1; j++)
        if(nums[j] <= x)
            swap(nums[++i], nums[j]);
    swap(nums[r - 1], nums[++i]);
    return i;
}
```

