---
title: P2868【USACO07DEC】Sightseeing Cows G
date: 2025-08-03 16:41:59
tags:
  - 01分数规划
  - 二分答案
  - SPFA
  - 省选/NOI−
categories:
  - 算法刷题
mathjax: true
---

**[题目链接](https://www.luogu.com.cn/problem/P2868)**

边权 $w_i$，点权 $f_i$，求一个环 $C$，使得 $\displaystyle \frac{\sum_{i \in C} f_i}{\sum_{i \in C} w_i}$ 最大。

$\displaystyle \frac{\sum_{i \in C} f_i}{\sum_{i \in C} w_i} \leq x \Longrightarrow \sum (f_{u_i} - x * w_i) \leq 0 \Longrightarrow \sum (x * w_i - f_{u_i}) \geq 0$
$(u 为边 i 的出点，当然，因为是环，用入点也可)$

因此，二分一个答案 $x$，将 $x * w_i - f_{u_i}$ 作为边权，判断图上是否存在负环，若存在负环，则 $x$ 应当增大，取 $l = mid$；否则，$x$ 可尝试变小，取 $r = mid$。

时间复杂度：$\mathcal O(nm * log(1e7))$

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 1e3 + 10, M = 6e3 + 10, inf = 0x3f3f3f3f, INF = 1e18;

int n, m;
int h[N], e[M], ne[M], w[M], idx, f[N];
double dist[N];
int cnt[N], vis[N];

void add(int a, int b, int c){
    w[idx] = c;
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}

bool check(double x){
    for(int i = 1; i <= n; i++) dist[i] = inf, cnt[i] = 0, vis[i] = 0;
    queue<int> q;
    q.push(0);
    vis[0] = 1;
    while(!q.empty()){
        int u = q.front(); q.pop();
        vis[u] = 0;
        for(int i = h[u]; ~i; i = ne[i]){
            int v = e[i];
            if(dist[v] > dist[u] + x * w[i] - f[u]){
                dist[v] = dist[u] + x * w[i] - f[u];
                cnt[v] = cnt[u] + 1;
                if(cnt[v] >= n + 1) return true; // 存在负环
                if(!vis[v]) q.push(v), vis[v] = 1;
            }
        }
    }
    return false;
}

void solve(){
    cin >> n >> m;
    memset(h, -1, sizeof h);
    for(int i = 1; i <= n; i++) cin >> f[i], add(0, i, 0);
    for(int i = 1; i <= m; i++){
        int a, b, c; cin >> a >> b >> c;
        add(a, b, c);
    }
    double l = 0, r = 1e3;
    while(r - l > 1e-4){
        double mid = (l + r) / 2;
        if(check(mid)) l = mid; // 存在负环
        else r = mid;
    }
    cout << fixed << setprecision(2) << r << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    // cin >> T;
    for(int i = 1; i <= T; i++) solve();
    return 0;
}
```