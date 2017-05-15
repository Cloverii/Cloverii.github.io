---
title: HDU 4302 Holedox Eating（线段树/优先队列/multiset）
date: 2015-09-14 23:58
categories: ALGORITHM
tags: HDU
---

**Keywords: Segment tree, Priority queue, Multiset**

题意：一条线段，在某些时刻上面的某些点会随机出现蛋糕。某种动物最初在 0 位置，在该动物想吃蛋糕时，它会跑到离他最近的蛋糕那里；如果有两个蛋糕与它的距离相同，就按照上次前进的方向走。输出它走过的总路程。

<!--more-->

其实就是个水题，然而坑了一天，交了两页的记录 - - 写篇博客纪念下
赛上用线段树区间最值过的，赛后看题解提到也可以线段树 + 二分做，又听说了 pq 和 multiset 的版本，就写了一发 pq，结果有个地方觉得写得不对但还是莫名 AC 了，觉得奇怪就坑了一下午 - - 还好最后终于找到了原因，并且用下面的数据叉掉了集训队里一个汉子的 AC 代码……这里 output 应该是26~
关于这个 case 和这个隐蔽修改方向导致 WA 的问题的解释以及 multiset 版本的代码，详见 [tt 哒博客](http://blog.csdn.net/lwt36/article/details/48446687)
总之，写代码前一定要思考清楚，要写出美观不易错的代码~

```
1  
20 13  
0 4  
1  
0 3  
1  
0 3  
1  
0 1  
0 5  
0 20  
1  
1  
1  
1  
```

- 线段树区间最值
  网上思路较多，就不细说了 - -
```C++
//赛上的线段树区间最值版
#define ALL(v) v.begin(), v.end()
#define CLR(array) memset(array, 0, sizeof(array))
#define DEBUG cout << "bug "
#define Fr first
#define FOR(i, n) for(int i = 0; i < n; i++)
#define MEM(array) memset(array, 0x3f, sizeof(array))
#define ls id << 1
#define lson id << 1, l, m
#define OFF(array) memset(array, -1, sizeof(array))
#define PR(x) cout << #x << " = " << x << "  "
#define PRLN(x) cout << #x << " = " << x << endl
#define rep(i, a, b) for(int i = a; i <= b; i++)
#define root 1, 1, n
#define rs id << 1 | 1
#define rson id << 1 | 1, m + 1, r
#define Rep(i, a, b) for(int i = a; i >= b; i--)
#define repk(i, a, b) for(i = a; i <= b; i++)
#define Repk(i, a, b) for(i = a; i >= b; i--)
#define Sc second
#define SZ size()
using namespace std;

typedef long long ll;
typedef pair<int, int> P;
const int inf = 0x3f3f3f3f, maxn = 1e5 + 5;
struct Node {
    int lc, rc, cnt; 
    //这里因为某点上可能有多个cake，所以还记录了区间内的cake数，其实单开数组记录每个点的cake数也可以
} tree[maxn << 2];
int lef, rig;

void build(int id, int l, int r) {
    tree[id].lc = inf;
    tree[id].cnt = 0;
    tree[id].rc = -inf;
    if(l == r) 
        return;
    int m = l + r >> 1;
    build(lson);
    build(rson);
}

void push_up(int id) {
    Node& now = tree[id];
    now.lc = min(tree[ls].lc, tree[rs].lc);
    now.rc = max(tree[ls].rc, tree[rs].rc);
}

void update(int id, int l, int r, int pos, int d) {
    Node& now = tree[id];
    now.cnt += d;
    if(l == r) {
        if(now.cnt) 
            now.lc = now.rc = l;
        else {
            now.lc = inf;
            now.rc = -inf;
        }
        return;
    }
    int m = l + r >> 1;
    if(pos <= m) update(lson, pos, d);
    else update(rson, pos, d);
    push_up(id);
}

void query(int id, int l, int r, int a, int b, int op) {
    if(!tree[id].cnt) return;
    if(a <= l && r <= b) {
        if(op) rig = min(rig, tree[id].lc);
        else lef = max(lef, tree[id].rc);
        return;
    }
    int m = l + r >> 1;
    if(a <= m) query(lson, a,  b, op);
    if(b > m) query(rson, a, b, op);
}


ll solve(void) {
    int n, q, a, b;
    ll ans = 0;
    int pre = 1, now = 1;
    scanf("%d%d", &n, &q);
    n++;
    build(root);
    while(q--) {
        scanf("%d", &a);
        if(a) {
            int goal;
            lef = -inf, rig = inf;
            query(root, 1, now, 0);
            query(root, now + 1, n, 1);
            if(now - lef != rig - now)
                goal = now - lef < rig - now ? lef : rig;
            else 
                goal = pre > 0 ? rig : lef;
            if(abs(goal) == inf) continue;
            
            update(root, goal, -1);
            ans += abs(goal - now);
            if(goal > now) pre = 1;
            else if(goal < now) pre = -1;
            now = goal;
        } else {
            scanf("%d", &b);
            update(root, b + 1, 1);
        }
    }
    return ans;
}

int main(void) {
    int t, kase = 0;
    scanf("%d", &t);
    while(t--) 
        printf("Case %d: %I64d\n", ++kase, solve());
    return 0;
}
```

- 然后是二分版本哒~可以不记录区间最值，只要记录区间内 cake 数就可以了，但是要写两个 query, 不太好看。
  思路是每次查询到某个区间时，如果当前区间内没有 cake, 直接返回。如果要查找区间最右边的值，就优先查找右子树，然后才是左子树，查找区间左值类比即可。
  不过，网上虽然有人提了这个思路，但是几乎没有清晰代码，所以也贴下~
```C++
// 二分版本~
// 省略头文件50行
const int inf = 0x3f3f3f3f, maxn = 1e5 + 5;
int sum[maxn << 2];

void build(int id, int l, int r) {
    sum[id] = 0;
    if(l == r) return;
    int m = l + r >> 1;
    build(lson);
    build(rson);
}

void update(int id, int l, int r, int pos, int d) {
    sum[id] += d;
    if(l == r) return;
    int m = l + r >> 1;
    if(pos <= m) update(lson, pos, d);
    else update(rson, pos, d);
}

int queryl(int id, int l, int r, int a, int b) { 
// 查找区间最左边的cake的位置，没有则返回inf
    if(l == r) return l;
    int m = l + r >> 1;
    int res = inf;
    if(a <= m && sum[ls]) 
        res = queryl(lson, a, b);
    if(res == inf && b > m && sum[rs])
        res = queryl(rson, a, b);
    return res;
}
// 注意两个query的区别
int queryr(int id, int l, int r, int a, int b) { 
// 查找区间最右边的cake的位置，没有则返回 -inf
    if(l == r) return l;
    int m = l + r >> 1;
    int res = -inf;
    if(b > m && sum[rs])
        res =  queryr(rson, a, b);
    if(res == -inf && a <= m && sum[ls])
        res = queryr(lson, a, b);
    return res;
}

ll solve(void) {
    int n, q, a, b;
    ll ans = 0;
    int pre = 1, now = 1;
    scanf("%d%d", &n, &q);
    n++;
    build(root);
    while(q--) {
        scanf("%d", &a);
        if(!a) {
            scanf("%d", &b);
            update(root, b + 1, 1);
        } else {
            int goal, lef, rig;
            lef = queryr(root, 1, now);
            rig = queryl(root, now + 1, n);
            if(now - lef != rig - now)
                goal = now - lef < rig - now ? lef : rig;
            else 
                goal = pre > 0 ? rig : lef;
            if(abs(goal) == inf) continue;

            GOTO: update(root, goal, -1);
            ans += abs(goal - now);
            if(goal > now) pre = 1;
            else if(goal < now) pre = -1;
            now = goal;        
        }
    }
    return ans;
}

int main(void) {
    int t, kase = 0;
    scanf("%d", &t);
    while(t--) 
        printf("Case %d: %I64d\n", ++kase, solve());
    return 0;
}
```

- pq 版本也来一发，网上代码也很多，不细说了 - -
  不过说实话，用两个pq维护虽然听上去很机智，但是代码比较丑 也可能是我写的不好看吧 - - 感觉 multiset 或者 map 写会更简单
```C++
// pq 版~
const int inf = 0x3f3f3f3f, maxn = 1e5 + 5;

ll solve(void) {
    priority_queue <int> pql;
    priority_queue <int, vector<int>, greater<int> > pqr;
    int n, q, a, b;
    ll ans = 0;
    int pre = 1, now = 0;
    scanf("%d%d", &n, &q);
    while(q--) {
        scanf("%d", &a);
        if(!a) {
            scanf("%d", &b);
            if(b >= now) pqr.push(b);
            else pql.push(b);
        } else {
            int choice = 1, lef = -inf, rig = inf;
            if(pql.size())
                lef = pql.top();
            if(pqr.size())
                rig = pqr.top();
            if(now - lef != rig - now) {
                if(now - lef < rig - now) 
                    choice = -1;
            } else choice = pre;
            if(choice > 0 && rig != inf) {
                pqr.pop();
                ans += rig - now;
                if(rig != now)
                    pre = choice;                
                now = rig;
            } else if(choice < 0 && lef != -inf) {
                pql.pop();
                ans += now - lef;
                pre = choice;
                now = lef;
            }
        }
    }
    return ans;
}

int main(void) {
    int t, kase = 0;
    scanf("%d", &t);
    while(t--) 
        printf("Case %d: %I64d\n", ++kase, solve());
    return 0;
}
```