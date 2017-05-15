---
title: UVA 10817 Headmaster's Headache（状压DP）
date: 2015-09-04 22:45
categories: ALGORITHM
tags: UVA
---

**Keywords: Dynamic Programming**

题意：某校有一定数目的在职教师和求职者，给出每位教师（求职者）的工资和能教的科目集合，求所支付的最少的工资，使得每个科目都至少有两名教师可以教。在职教师不能辞退。

<!--more-->

状压DP第一题。一开始抄了一发 lrj 的代码，理解了很久，后来自己写了一发三进制的（・∀・）。但自己的三进制版时间比lrj版多了100ms左右，也许是模拟三进制的乘法和取模操作较慢的原因？（其实每次要反复取模的时候都会胆颤心惊的）
```C++
// 三进制版
// 分别用0，1，2表示某个科目没人教，恰有一位教师教和两位以上教师教
// 用一个三进制数保存状态
#include <algorithm>
#include <cctype>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iostream>
#include <map>
#include <queue>
#include <sstream>
#include <string>
#include <vector>
#define ALL(v) v.begin(), v.end()
#define CLR(array) memset(array, 0, sizeof(array))
#define DEBUG cout << "bug "
#define Fr first
#define FOR(i, n) for(int i = 0; i < n; i++)
#define MEM(array) memset(array, 0x3f, sizeof(array))
#define OFF(array) memset(array, -1, sizeof(array))
#define PB push_back
#define PR(x) cout << #x << " = " << x << "  "
#define PRLN(x) cout << #x << " = " << x << endl
#define rep(i, a, b) for(int i = a; i <= b; i++)
#define Rep(i, a, b) for(int i = a; i >= b; i--)
#define repk(i, a, b) for(i = a; i <= b; i++)
#define Repk(i, a, b) for(i = a; i >= b; i--)
#define Sc second
#define SZ size()
using namespace std;

typedef long long ll;
typedef pair<int, int> P;
const int inf = 0x3f3f3f3f, maxn = 1e5 + 5;
int s, m, n, goal;
int salary[125], course[125];
int dp[125][maxn];

// encode 和decode 函数模拟三进制位运算
int encode(int* a) {
    int res = 0;
    rep(i, 1, s) 
        res = res * 3 + a[i];
    return res;
}
void decode(int x, int* a) {
    Rep(i, s, 1) {
        a[i] = x % 3;
        x /= 3;
    }
}

int dfs(int i, int st) {
    if(i == m + n) return st == goal ? 0 : inf;
    int& res = dp[i][st];
    if(res >= 0) return res;
    
    res = inf;
    if(i >= m) res = dfs(i + 1, st); // 不选
    int p[10], a[10];
    decode(course[i], p);
    decode(st, a);
    rep(j, 1, s) {
        a[j] += p[j];
        if(a[j] > 2) a[j] = 2;
    }
    res = min(res, salary[i] + dfs(i + 1, encode(a))); //选
    return res;
}

int solve(void) {
    CLR(course); OFF(dp);
    string in; getline(cin, in);
    int a[10];
    fill(a + 1, a + s + 1, 2);
    goal = encode(a); //目标集合为所有科目都有至少两个人可以教
    FOR(i, m + n) {
        getline(cin, in);
        stringstream ss(in);
        ss >> salary[i];
        int u; CLR(a);
        while(ss >> u) a[u] = 1;
        course[i] = encode(a); 
        // course[i]表示第i个人能教的科目集合
    }
    return dfs(0, 0);
}

int main(void) {
    //freopen("F:\\Desktop\\in.txt", "r", stdin);
    while(cin >> s >> m >> n && s + m + n)
        cout << solve() << '\n';
    return 0;
}
```

```C++
// lrj大大的版本(⌒～⌒)
// 用两个集合分别表示恰有一个人能教和至少两个人能教的科目
// 编程复杂度较低
//此处省略头文件50行
const int inf = 0x3f3f3f3f, maxn = 1e5 + 5;
int salary[125], course[125];
int dp[125][1 << 10][1 << 10];
int n, m, s;

int dfs(int id, int s0, int s1, int s2) {
    if(id == m + n) return s2 == (1 << s) - 1 ? 0 : inf;
    int& res = dp[id][s1][s2];
    if(res >= 0) return res;

    res = inf;
    if(id >= m) res = dfs(id + 1, s0, s1, s2);
    int m1 = s0 & course[id];
    int m2 = s1 & course[id];
    s0 ^= m1, s1 = (s1 ^ m2) | m1;
    s2 |= m2;
    res = min(res, salary[id] + dfs(id + 1, s0, s1, s2));
    return res;
}

int solve(void) {
    CLR(course); OFF(dp);
    string in; getline(cin, in);
    FOR(i, m + n) {
        getline(cin, in);
        stringstream ss(in);
        ss >> salary[i];
        int u;
        while(ss >> u)
            course[i] |= 1 << u - 1;
    }
    return dfs(0, (1 << s) - 1, 0, 0);

}

int main(void) {
    //freopen("F:\\Desktop\\in.txt", "r", stdin);
    while(cin >> s >> m >> n && s + m + n)
        cout << solve() << '\n';
    return 0;
}
```