---
title: LeetCode 60. Permutation Sequence 与康托逆展开
date: 2017-05-19 12:57:16
categories: ALGORITHM
tags: LeetCode
mathjax: true
---

[题目链接](https://leetcode.com/problems/permutation-sequence/#/description)

> The set `[1,2,3,…,*n*]` contains a total of *n*! unique permutations.
>
> By listing and labeling all of the permutations in order,
> We get the following sequence (ie, for *n* = 3):
>
> 1. `"123"`
> 2. `"132"`
> 3. `"213"`
> 4. `"231"`
> 5. `"312"`
> 6. `"321"`
>
> Given *n* and *k*, return the *k*th permutation sequence.
>
> **Note:** Given *n* will be between 1 and 9 inclusive.

<!--more-->

时隔多年终于弄明白[康托展开](https://zh.wikipedia.org/wiki/%E5%BA%B7%E6%89%98%E5%B1%95%E5%BC%80)到底是个什么东西啦，可喜可贺～

> 自然数 $X$ 的康托展开是
>
> $X = a_n(n - 1)!+a_{n − 1}(n −2)!+.... + a_2\times 1!+a_1\times 0!$
>
> 其中，$a_{i}$ 为整数，并且 $0\leq a_i < i,1\leq i\leq k$.

你看到这里时心情可能和当年的我一样：什么鬼……所以先看题吧。

$9!$ 并不大，所以我一开始写了个暴力，T 了，看来 case 数不是一般的多，那就只能找规律了。

观察题面中 $\{1,2,3\}$ 的全排列，可以发现是由 $1+\{2,3\}的全排列$，$2+\{1,3\}的全排列$ 和 $3+\{1,2\}的全排列$ 组成的，而 $1+\{2,3\}的全排列=(1+2+\{3\}的全排列) + (1+3+\{2\}的全排列)$。好像懂了点什么……在已知 $k$ 和 $n$ 的情况下，显然我们可以得到第 $k$ 个排列的第一个数为 $(k-1)/(n-1)!$，而剩余的 $n-1$ 个数以此类推，可以转化成规模较小的子问题……简单粗暴地贴一下代码好了：

```c++
class Solution {
public:
    string getPermutation(int n, int k) {
        string ans;
        vector<int> v;
        int fac[n + 1] = {1};
        for(int i = 1; i <= n; i++) {
            v.push_back(i);
            fac[i] = fac[i - 1] * i;
        }
        cout << fac[1];
        k--;
        do {
            int tmp = k / fac[n - 1];
            ans.push_back(v[tmp] + '0');
            v.erase(v.begin() + tmp);
            k %= fac[n - 1];
        } while(--n);
        return ans;
    }
};
```

再看维基百科的定义：

> 康托展开是一个全排列到一个自然数的双射，常用于构建哈希表时的空间压缩。 康托展开的实质是计算当前排列在所有由小到大全排列中的顺序，因此是可逆的。

这道题显然是从自然数到排列的转换，也就是康托逆展开。

那么康托展开呢？例如，有排列 $\{2,3,1\}$，怎么计算它是第几个排列呢？

很简单，对于排列中的第 $i$ 个数 $p_i$，只要计算 $p_i$ 后小于 $p_i$ 的数的个数，记为 $a_i$，则该排列的序号为 $X = a_n(n - 1)!+a_{n − 1}(n −2)!+.... + a_2\times 1!+a_1\times 0!$（从0开始）。这样我们就理解开头的式子啦～代码就不写咯

不知道为啥以 cantor expansion 为关键词搜到的结果并不太多呢。