---
title: CF1253F Cheap Robot
date: 2025-08-01 10:47:48
tags:
  - Kruskal重构树
  - LCA
  - Dijkstra
  - 提高+/省选−
categories:
  - 算法刷题
mathjax: true
---

**[题目链接](https://codeforces.com/contest/1253/problem/F)**

设 $dist_i$ 表示：从点 $i$ 走到 距离 $i$ 最近的 "充电点" 的距离

如果存在一条权值为 $w$ 的连边 $(a, \ b)$，那么想要成功通过这条边的条件就是 $c >= dist_a + w + dist_b$

因此，如果在原来的每条边的权值 $w$ 的基础上，我们加上 $dist_a + dist_b$，我们就可以得到 $a$ 和 $b$ 两点之间需要满足的电量 $c$。

然后，我们从小到大排序所有的边，并构建 $Kruskal$重构树，树上的 $lca(a, b)$ 即记录从 $a$ 到 $b$ 的最大瓶颈。**因为我们每次都可以从某个点到其最近的 "充电点" 充满点，所以只需要考虑最大瓶颈。(可以画图理解一下)**

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 1e6 + 10, M = 1e6 + 10, inf = 0x3f3f3f3f;

int n, m, k, q;
int h[N], e[M], ne[M], w[M], idx, dist[N]; // djk
bool vis[N]; // djk

int p[N]; // 记录并查集父节点

struct edge{
    int u, v, w;
    bool operator< (const edge &t) const{
        return w < t.w;
    }
}E[M];

vi G[N]; // kruskal 重构树邻接表
int val[N];
int sz[N], dep[N], fa[N], son[N], top[N]; // 树链剖分求 LCA

void add(int a, int b, int c){
    w[idx] = c;
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}
void djk(){
    memset(dist, 0x3f, sizeof dist);
    dist[0] = 0;
    priority_queue<pii, vector<pii>, greater<pii>> q;
    q.push({0, 0});
    while(!q.empty()){
        auto [d, u] = q.top(); q.pop();
        if(vis[u]) continue;
        vis[u] = true;
        for(int i = h[u]; ~i; i = ne[i]){
            int v = e[i];
            if(dist[v] > d + w[i]){ // dist[u] + w[i]也可
                dist[v] = d + w[i];
                if(!vis[v]) q.push({dist[v], v});
            }
        }
    }
}

int find(int x) {return (p[x] == x ? x : p[x] = find(p[x]));}

void dfs1(int u){
    sz[u] = 1;
    dep[u] = dep[fa[u]] + 1;
    for(int &v : G[u]){
        if(v == fa[u]) continue;
        fa[v] = u;
        dfs1(v);
        sz[u] += sz[v];
        if(sz[v] > sz[son[u]]) son[u] = v;
    }
}
void dfs2(int u, int h){
    top[u] = h;
    if(son[u]) dfs2(son[u], h);
    for(int &v : G[u]){
        if(v == fa[u] || v == son[u]) continue;
        dfs2(v, v);
    }
}
int lca(int u, int v){
    while(top[u] != top[v]){
        if(dep[top[u]] < dep[top[v]]) swap(u, v);
        u = fa[top[u]];
    }
    return (dep[u] < dep[v] ? u : v);
}

void solve(){
    cin >> n >> m >> k >> q;
    memset(h, -1, sizeof h);
    for(int i = 1; i <= m; i++){
        int u, v, w; cin >> u >> v >> w;
        E[i] = {u, v, w};
        add(u, v, w), add(v, u, w);
    }
    for(int i = 1; i <= k; i++) add(0, i, 0), add(i, 0, 0);
    // 加入超级源点0
    
    djk(); // 求所有节点到最近中心点的最短距离
    
    for(int i = 1; i <= m; i++) 
        E[i].w += dist[E[i].u] + dist[E[i].v];
    // 修改每条边权重用于 Kruskal 重构树：w += dist[u] + dist[v]
    sort(E + 1, E + m + 1);
    
    iota(p, p + 2 * n, 0ll); // kruskal重构树
    int cnt = n;
    for(int i = 1; i <= m; i++){
        int u = find(E[i].u), v = find(E[i].v), w = E[i].w;
        if(u != v){
            val[++cnt] = w;
            p[u] = p[v] = cnt;
            G[cnt].push_back(u), G[cnt].push_back(v);
        }
    }
    
    dfs1(cnt), dfs2(cnt, cnt); // 将kruskal树，树链剖分

    while(q--){
        int u, v; cin >> u >> v;
        cout << val[lca(u, v)] << endl;
    }
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```