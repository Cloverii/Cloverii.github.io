---
title: 从 K Sum 问题想到的
date: 2017-03-26 16:32:14
categories: LEARNING
tags: [Algorithm, Java, C++]
---
LeetCode 上有好几道 K sum 的题：[1. Two Sum (E)](https://leetcode.com/problems/two-sum/#/description), [15. 3Sum (M)](https://leetcode.com/problems/3sum/#/description), [16. 3Sum Closest (M)](https://leetcode.com/problems/3sum-closest/), [18. 4Sum(M)](https://leetcode.com/problems/4sum/#/description)。大致就是给一个无序数组和一个 target，从数组里找出 k 个数使它们的和为 target （或最接近 target)。

<!--more-->
2Sum 因为要输出两个数的 indices，加上是 Easy，没多想就写了个 O(n^2) 的暴力。3Sum 要求的是输出这三个数，就排了个序，枚举前两个数再二分第三个数，O(n^2logn)。到 3Sum Closest 的时候就纠结了，枚举 + 二分虽然理论上也还能凑合凑合，但是太麻烦了(虽然我确实这么 A 了)。等看到 4Sum 就尴尬了，总不能继续枚举前三个数吧。想了半天也没什么头绪（可见思维定势可怕），只好滚回 2Sum 看 Solutions。真是一看吓一跳……

### 2Sum
**Description:**
Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution, and you may not use the same element twice.

**Solutions:**
1. 暴力枚举 *O(n^2)*
2. 带 indices sort 一遍，然后 two pointers *O(nlogn)*
   这样的话还要写个 compareTo()，我当然是拒绝的
   不过是后面 Nsum (N > 2) 的基础
3. 利用 HashMap(Java), unordered_map(C++) *平均O(n)*
   原理是从头开始扫一遍数组，如果 target - nums[i] 不在 map 里，就把 (nums[i], i) 丢进 map，如果在的话就是 answer 了。
```Java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> mp = new HashMap<Integer, Integer>();
        for(int i = 0; i < nums.length; i++) {
            int tmp = target - nums[i];
            if(mp.containsKey(tmp)) {
                return new int[] {mp.get(tmp), i};
            } else {
                mp.put(nums[i], i);
            }
        }
        return new int[]{0, 0};
    }
}
```
4. 在某 HashMap 写法的讨论区还看见了[一个比较丧病的写法](https://discuss.leetcode.com/topic/2447/accepted-java-o-n-solution/31)，不用 HashMap 直接开数组……虽然也花了点时间控制了下数组的大小但是对极限数据并没有什么用。只能说数据弱吧，不然这种程度的空间换时间简直找死啊。

PS: 虽然似乎听韬韬提过 unordered_map 但是从来没有用过，也是第一次知道它的插入查找平均复杂度居然是线性。cplusplus.com 上说是它是把元素根据 hash 值丢进桶里面的，那就相当于 Java 的 HashMap 咯，单个元素的插入查找比 map (TreeMap) 要快。

### 3Sum && 3Sum Closest
**Description:**
3Sum:
Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.
Note: The solution set must not contain duplicate triplets.

3Sum Closest:
Given an array S of n integers, find three integers in S such that the sum is closest to a given number, target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

**Solutions:**
1. 像我一开始一样，sort 一把再枚举前两个数+二分第三个数 *O(n^2logn)*
2. sort 一把，枚举一个数 + two pointers *O(n^2)*
3Sum Closest 在 3Sum 的基础上改改就好

```Java
public class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans = new LinkedList<>(); // or new LinkedList<List<Integer>() 
        Arrays.sort(nums);
        for(int i = 0; i < nums.length; i++) {
            if(i > 0 && nums[i] == nums[i - 1]) continue;
            int target = 0 - nums[i];
            
            int lo = i + 1, hi = nums.length - 1;
            while(lo < hi) {
                int sum = nums[lo] + nums[hi];
                if(sum < target) {
                    lo++;
                }
                else if(sum > target) {
                    hi--;
                } else {
                    ans.add(Arrays.asList(nums[i], nums[lo], nums[hi]));
                    while(lo < hi && nums[lo] == nums[lo + 1]) lo++;
                    while(lo < hi && nums[hi] == nums[hi - 1]) hi--;
                    lo++; hi--;
                }
            }
        }
        return ans;
    }
}
```

### 4Sum
**Description:**
Given an array S of n integers, are there elements a, b, c, and d in S such that a + b + c + d = target? Find all unique quadruplets in the array which gives the sum of target.
Note: The solution set must not contain duplicate quadruplets.

到了这一步，如果继续写 two pointers 的话，就要开始考虑代码重用性了……

下面代码中剪枝的灵感来自[这里](https://discuss.leetcode.com/topic/22705/python-140ms-beats-100-and-works-for-n-sum-n-2)。太久没写过搜索了如果不是看见这份代码根本想不起来剪枝这回事了……
```Java
public class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Arrays.sort(nums);
        return NSum(4, nums, 0, target);
    }
    
    private ArrayList<List<Integer>> NSum(int n, int[] nums, int begin, int target) {
        ArrayList<List<Integer>> ans = new ArrayList<List<Integer>>();
        if(n == 2) {
            int lo = begin; int hi = nums.length - 1;
            while(lo < hi) {
                int sum = nums[lo] + nums[hi];
                if(sum < target) {
                    lo++;
                }
                else if(sum > target) {
                    hi--;
                } else {
                    ans.add(new LinkedList<Integer>(Arrays.asList(nums[lo], nums[hi])));
                    while(lo < hi && nums[lo] == nums[lo + 1]) lo++;
                    while(lo < hi && nums[hi] == nums[hi - 1]) hi--;
                    lo++; hi--;
                }
            }
        } else {
            List<List<Integer>> tmp/* = new ArrayList<>()*/;
            for(int i = begin; i < nums.length - n + 1; i++) {
                if(nums[i] * n > target) break; // pruning
                tmp = NSum(n - 1, nums, i + 1, target - nums[i]);
                if(!tmp.isEmpty()) {
                    for(List<Integer> l: tmp) {
                        l.add(0, nums[i]);
                    }
                    ans.addAll(tmp);
                }
                while(i < nums.length - 1 && nums[i] == nums[i + 1]) i++;
            }
        }
        return ans;
    }
}
```
但是又看到了[一个神奇的利用 HashMap 的 solution](https://discuss.leetcode.com/topic/12893/on-average-o-n-2-and-worst-case-o-n-3-java-solution-by-reducing-4sum-to-2sum)，平均 O(n^2)，最坏 O(n^3)，吓得我瓜子都掉了……

### 总结
1. 暴力 *O(n^k)*
2. two pointers *O(n^(k-1))(k > 2) || O(nlogn)(k ==2)*
3. 复杂度和可扩展性成谜的 HashMap 
