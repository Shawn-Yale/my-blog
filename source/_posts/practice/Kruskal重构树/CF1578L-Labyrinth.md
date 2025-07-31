---
title: CF1578L Labyrinth
date: 2025-07-31 16:55:29
tags:
  - Kruskal重构树
  - LCA
  - 提高+/省选−
categories:
  - 算法刷题
mathjax: true
---

**[题目链接](https://codeforces.com/contest/1578/problem/L)**

首先注意到一个性质，刚开始的时候身体宽度没有限制(身体宽度 $<= min_w$)，即刚开始可以在图上随便走，所以从 $1$ 号节点开始的限制等于无。

**根据贪心的思想**，走的时候我们要**尽可能的使路径上最小的边权最大**，所以不难想到要在**最大生成树上**跑。

先把最大生成树求出来，然后为了方便建上 $Kruskal$ 重构树，**以下分析在重构树上面考虑**。

现在我们考虑怎样吃是最优的。容易发现，我们**一定是先吃完一颗子树再去吃另一棵子树最优**。简要证明一下，如果我们还没吃完一颗子树就去吃另一棵，那么当我们回来吃这颗子树的时候时宽度一定会增大，完全不优，所以证明完毕。

性质都出来以后，我们考虑如何合并子树信息，我们设 $dp_i​$ 表示吃完子树 $i$ 所需的最大初始宽度，$sum_i$​ 表示子树 $i$ 的点权和。

现在我们要合并两棵子树 $a$ 和 $b$ 的信息，分两种情况，即 先吃 $a$ 再吃 $b$ **或** 先吃 $b$ 再吃 $a$。

**以第一种情况为例**，设 $u$ 表示 $a$ 和 $b$ 子树的父亲节点，$w$ 为它的点权。因为我们求的是最大生成树，所以这个点的点权一定是当前所有点中最小的。
吃完 $a$ 子树以后，如果我们想要经过 $u$ 走到 $b$，那么需要保证 $w ≥ sum_a​+dp_u$​，
如果我们想要继续吃掉 $b$，那么还需要保证 $dp_b​ ≥ sum_a​+dp_u$​，
所以得出 $dp_u​=min(w−sum_a​,dp_b​−sum_a​)$。
**另一种情况同理**，可以得到转移方程为：
$dp_u​=max \Bigl(min(w−sum_a​,dp_b​−sum_a​),min(w−sum_b​,dp_a​−sum_b​)\Bigr)$


```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 2e5 + 10, M = 1e5 + 10, inf = 0x3f3f3f3f;
struct edge{
    int u, v, w;
    bool operator< (const edge &t)const{
        return w > t.w;
    }
}e[M];
int p[N];
int sum[N], dp[N];

int find(int x){return (p[x] == x ? x : p[x] = find(p[x]));}

void solve(){
    int n, m; cin >> n >> m;
    for(int i = 1; i <= n; i++) cin >> sum[i];
    for(int i = 1; i <= m; i++) cin >> e[i].u >> e[i].v >> e[i].w;
    sort(e + 1, e + m + 1);
    int cnt = n;
    iota(p, p + 2 * n, 0ll);
    fill(dp, dp + 2 * n, inf);
    for(int i = 1; i <= m; i++){
        int u = find(e[i].u), v = find(e[i].v), w = e[i].w;
        if(u != v){
            p[u] = p[v] = ++cnt;
            dp[cnt] = max(min(w - sum[u], dp[v] - sum[u]), min(w - sum[v], dp[u] - sum[v]));
            sum[cnt] = sum[u] + sum[v];
        }
    }
    cout << (dp[cnt] > 0 ? dp[cnt] : -1ll) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```