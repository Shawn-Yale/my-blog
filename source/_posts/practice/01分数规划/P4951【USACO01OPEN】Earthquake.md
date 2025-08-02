---
title: P4951【USACO01OPEN】Earthquake
date: 2025-08-02 16:42:30
tags:
  - 01分数规划
  - 二分答案
  - Kruskal
  - 提高+/省选−
categories:
  - 算法刷题
mathjax: true
---

**[题目链接](https://www.luogu.com.cn/problem/P4951)**

$\displaystyle \frac{f - \sum c_i}{\sum t_i} \geq x \ \Longrightarrow \ f - \sum c_i \geq x \sum t_i \ \Longrightarrow \ f - \sum (c_i + x * t_i) \geq 0$，其中 $i \in {选择修复的道路}$

**显然**，选择修复的道路越多，利润相对越低，因此我们要选择**尽可能少的道路**进行修复；
但同时，我们**需要保证所有牧场连通**，所以我们需要选择 $n - 1$ 条边，构建**最小生成树**。

二分一个答案 $x$，把 $c_i + x * w_i$ 作为每一条边的边权，从小到大排序，构建**最小生成树**。
如果**最小生成树的总权重** $\leq f$，则说明当前 $x$ 可行，尝试更大的 $x$；否则当前的 $x$ 不可行，尝试更小的 $x$。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 4e2 + 10, M = 1e4 + 10, inf = 0x3f3f3f3f;
int n, m, f;
struct edge{
    int u, v, c, t;
    double w;
    bool operator< (const edge &t) const{
        return w < t.w;
    }
}e[M];

int p[N];
int find(int x){
    return p[x] == x ? x : p[x] = find(p[x]);
}

bool check(double x){
    double sum = 0;
    for(int i = 1; i <= m; i++) e[i].w = e[i].c + x * e[i].t;
    sort(e + 1, e + m + 1);
    iota(p, p + n + 1, 0ll);
    for(int i = 1; i <= m; i++){
        int u = find(e[i].u), v = find(e[i].v);
        if(u != v){
            sum += e[i].w;
            p[u] = v;
        }
    }
    return f >= sum;
}

void solve(){
    cin >> n >> m >> f;
    for(int i = 1; i <= m; i++)
        cin >> e[i].u >> e[i].v >> e[i].c >> e[i].t;
    double l = 0, r = 2e9;
    while(r - l > 1e-6){
        double mid = (l + r) / 2;
        if(check(mid)) l = mid;
        else r = mid;
    }
    cout << fixed << setprecision(4) << r << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```