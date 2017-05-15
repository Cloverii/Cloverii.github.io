---
title: POJ 2528 Mayor's posters（线段树染色+离散化）
date: 2015-08-29 16:39
categories: ALGORITHM
tags: POJ
mathjax: true
---

**Keywords: Segment tree**

[题目来源](http://poj.org/problem?id=2528)：POJ
题意：在墙上贴海报，后贴的会覆盖之前的，问最后能看见几张海报
<!--more-->

**20170515 Update:**
NotOnlySuccess菊苣的博客打不开了，不清楚情况。话说菊苣现在在微信啊，膜拜。想看菊苣的文章的话，建议以 ”notonlysuccess 【完全版】线段树“ 为关键词进行搜索。

---

线段树染色问题，注意题目里每个数字代表一个单位长度而不是点，普通离散化会出现问题。以下例子来自[NotOnlySuccess菊苣的博客](http://notonlysuccess.me/?p=978)
> **例子一**:1-10 1-4 5-10
> **例子二**:1-10 1-4 6-10
> 普通离散化后都变成了$[1,4][1,2][3,4]$
> 线段2覆盖了$[1,2]$, 线段3覆盖了$[3,4]$, 那么线段1是否被完全覆盖掉了呢?
> 例子一是完全被覆盖掉了,而例子二没有被覆盖

针对这个问题，网上常见的做法是对离散化的数组做一个小处理。以下同样来自[NotOnlySuccess菊苣的博客](http://notonlysuccess.me/?p=978)
> 为了解决这种缺陷,我们可以在排序后的数组上加些处理,比如说$[1,2,6,10]$
> 如果相邻数字间距大于1的话,在其中加上任意一个数字,比如加成$[1,2,3,6,7,10]$,然后再做线段树就好了.

但是，这样做会增加 `maxn` 的大小，难以忍受……

事实上，只要在输入$[l, r]$的时候进行处理，变成$[l, r + 1]$，相当于线段树存储的是一整段区间的信息（传说中的“段树”），就可以应用普通离散化了，具体见代码

另外，由于POJ这题数据奇弱无比，一种叫做浮水法的 $n^2$ 的做法也是可以过的，这个是我搜本题题解的时候搜到的一种做法，网上资料很少，下面同样附上代码和本人的理解o(*￣▽￣*)ブ

**浮水法**：本题中，将每条线段和其后的线段进行比较，没有重叠部分就继续和下一条比较，有重叠部分则去掉重叠部分再继续比较，比较是递归的。这个递归过程就像一层一层上浮最后浮到水面上一样，所以叫浮水法（让人联想到冒泡啊）。如果某条线段最后能浮到水面上，`ans++`

```C++
// 线段树
// 省略头文件
#define ALL(v) v.begin(), v.end()
#define CLR(array) memset(array, 0, sizeof(array))
#define DEBUG cout << "bug "
#define Fr first
#define FOR(i, n) for(int i = 0; i < n; i++)
#define MEM(array) memset(array, 0x3f, sizeof(array))
#define ls L(id)
#define lson id << 1, l, mid
#define OFF(array) memset(array, -1, sizeof(array))
#define PR(x) cout << #x << " = " << x << "  "
#define PRLN(x) cout << #x << " = " << x << endl
#define rep(i, a, b) for(int i = a; i <= b; i++)
#define root 1, 1, n
#define rs R(id)
#define rson id << 1 | 1, mid, r // 注意这里是 mid
#define Rep(i, a, b) for(int i = a; i >= b; i--)
#define repk(i, a, b) for(i = a; i <= b; i++)
#define Repk(i, a, b) for(i = a; i >= b; i--)
#define Sc second
#define SZ size()
using namespace std;

inline int L(int x){ return x << 1; }
inline int R(int x){ return x << 1 | 1; }
inline int MID(int l, int r){ return l + r >> 1; }
typedef long long ll;
typedef pair<int, int> P;
const int inf = 0x3f3f3f3f, maxn = 2e4 + 5;
int color[maxn << 2];
int x[maxn >> 1], y[maxn >> 1];
int vis[maxn >> 1];

void push_down(int id) {
    color[ls] = color[rs] = color[id];
    color[id]= -1;
}

void update(int id, int l, int r, int a, int b, int val) {
    if(a <= l && r <= b) {
        color[id] = val;
        return;
    }
    if(r > l && ~color[id]) push_down(id);
    int mid = MID(l, r);
    if(mid > a) update(lson, a, b, val);
    //第一次写段树，一开始总是莫名崩溃，后来发现此处的判断跟点树不一样，不应该有等号，否则会无限递归 -> 越界
    if(mid < b) update(rson, a, b, val);
}

void query(int id, int l ,int r) {
    if(~color[id]) {
        vis[color[id]] = 1;
        return;
    }
    if(l + 1 == r) return;
    int mid = MID(l, r);
    query(lson);
    query(rson);
}

int compress(int m) { // 离散化部分
    vector<int> v;
    FOR(i, m) {
        scanf("%d%d", x + i, y + i);
        y[i]++; // 此处 +1, 使 x[i], y[i] 表示区间的左右端点
        v.push_back(x[i]);
        v.push_back(y[i]);
    }
    sort(ALL(v));
    v.resize(unique(ALL(v)) - v.begin());
    FOR(i, m) {
        x[i] = 1 + lower_bound(ALL(v), x[i]) - v.begin();
        y[i] = 1 + lower_bound(ALL(v), y[i]) - v.begin();
    }
    return v.SZ;
}

int solve(void) {
    int n, m, ans = 0; 
    scanf("%d", &m);
    n = compress(m);
    color[1] == m; //开始全部无色，用 m 表示
    FOR(i, m) update(root, x[i], y[i], i);
    CLR(vis);
    query(root); 
    FOR(i, m) if(vis[i]) ans++;
    return ans;
}

int main(void) {
    //freopen("F:\\Desktop\\in.txt", "r", stdin);
    int t; scanf("%d", &t);
    while(t--) printf("%d\n", solve());
    return 0;
}
```

```C++
// 浮水法
// 此处省略50行头文件及宏
const int inf = 0x3f3f3f3f, maxn = 1e4 + 5;
int a[maxn], b[maxn];
int n;

int cover(int l, int r, int id) {
    if(id > n) return 1;
    int nowl = a[id], nowr = b[id];
    if(l > nowr || r < nowl) return cover(l, r, id + 1);
    if(r > nowr && cover(nowr + 1, r, id + 1)) return 1;
    if(nowl > l && cover(l, nowl - 1, id + 1)) return 1;
    return 0;
}

int solve(void) {
    int ans = 0; 
    scanf("%d", &n);
    rep(i, 1, n) scanf("%d%d", a + i, b + i);
    rep(i, 1, n) 
        if(cover(a[i], b[i], i + 1)) ans++;
    return ans;
}

int main(void) {
    //freopen("F:\\Desktop\\in.txt", "r", stdin);
    int t; scanf("%d", &t);
    while(t--) printf("%d\n", solve());
    return 0;
}
```

