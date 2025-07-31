---
title: Kruskal重构树(合集)
date: 2025-07-30 21:31:44
tags:
  - Kruskal重构树
  - LCA
  - 线段树
  - 树链剖分
categories:
  - 算法笔记
mathjax: true
sticky: 1
---

## $Kruskal$ 重构树
$Kruskal$ 重构树（又称并查集生成树、合并树）是把「并查集合并过程」映射成一棵包含原始点和合并节点的树结构，从而让一些「连通性／极值」类的离线查询问题可以转化为树上 $LCA$ 或区间最值查询。下面从原理、构造和典型应用场景三个层面详细说明。
基于阈值的连通性判断和MST 瓶颈值查询
---
### 一、为什么要用 $Kruskal$ 重构树？

在一张带「时间」或「权值」标号的无向图中，常见的两点性质有：

- **相连时间**：两点 $u,v$ 在添加前 $k$ 条边后是否连通？最早需要多少条边
- **最大边权**：在某条最小生成树（$MST$）中，$u$ 到 $v$ 的路径上最大的边权是多少

直接对每个查询单独跑并查集或者在树上搜，往往会导致 $\mathcal O((n+m)α)$ 或 $\mathcal O(logn)$ 的复杂度累加上去就超时。$Kruskal$ 重构树能把这些「两点最早连通的时刻」或「两点在 $MST$ 中的最大边权」一次性预处理成树上 $LCA$ 的值，后续查询都能 $\mathcal O(logn)$ 甚至 $\mathcal O(1)$ 完成。

---

### 二、Kruskal 重构树的构造回顾

1. **按照时间／权值**（边编号或边权）从小到大枚举每条边，做 $DSU$ 合并；
2. 每次真正把两棵不同的并查集合并时，新建一个树节点（编号从 $n+1$ 开始），把它的两个孩子指向被合并集合的根，同时记录“合并时间”或“权值”（即当前边的编号／权值）；
3. 合并结束后，最终的并查集森林恰好就是一棵「二叉」合并树（或多叉也可，视实现而定）；
4. 原始点（$1…n$）是叶子，合并节点（$n+1…cnt$）是内部节点。

**关键性质**：任意两个叶子 $u,v$ 的 $LCA$ 节点所存的时间／权值，就是这对点最早连通所需的“阈值”。

---

### 三、典型应用场景
以下这些常见题型，都能利用 Kruskal 重构树大幅简化或提速。

| 场景分类                     | 问题形式                                  | 利用方式                                            |
| :----------------------- | :------------------------------------ | :---------------------------------------------- |
| **离线连通性阈值查询**            | 给若干查询 $(u,v)$，问两点在第几条边加入后首次连通？        | 构造重构树，查询 $LCA$ 的“合并时间”                          |
| **区间连通性**                | 问区间 $[l,r]$ 有点两两连通的最早时刻               | 化为相邻点 $(i,i+1)$ 的阈值数组，建线段树或 $RMQ$               |
| **MST 路径极值**             | 在给定 $MST$ 上，查询 $u$ 到 $v$ 路径的最大（或最小）边权 | 按权排序做合并树，查询 $LCA$ 的权值                           |
| **动态图离线**                | 边按时间插入／删除后，离线回答若干连通性查询                | 把插入时刻／删除时刻当作权值，构造两棵重构树；或扫描线+双重重构树               |
| **分层聚类（Single-Linkage）** | 将点两两距离做聚类，查询聚成 $k$ 类时的距离阈值            | 重构树本质就是层次聚类树（$dendrogram$）                      |
| **第二小生成树**               | 计算每条边加入 $MST$ 后，与之对应的次优替换代价           | 对每条非 $MST$ 边 $(u,v)$，求路径最大边权 → 可用重构树或普通树上 $RMQ$ |

**Kruskal 重构树最擅长的，就是把「按阈值（权值或时间）合并」的过程一次性预处理成树上的 LCA 问题。**
这样无论是：
- **基于阈值的连通性判断**（最早连通时间）
- **MST 瓶颈值查询**（路径最大边权）

都可以做到 **预处理 $\mathcal O(n log n)$ + 查询 $\mathcal O(log n)$**，比每次二分+DSU高效很多。

[Kruskal重构树讲解——左程云](https://www.bilibili.com/video/BV1xVZhYmEti/?spm_id_from=333.337.search-card.all.click&vd_source=f0489718ccab992000c983a006bde4a5)

[Kruskal重构树题单——洛谷](https://www.luogu.com.cn/training/247303#problems)

## [P1967 [NOIP 2013 提高组] 货车运输](https://www.luogu.com.cn/problem/P1967)

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
//#define int long long
#define vi vector<int>
const int N = 2e4 + 10, M = 5e4 + 10, inf = 0x3f3f3f3f;
// 重构树最多有 2N-1 个点，这里已经开了双倍
struct edge{
    int u, v, w;
    bool operator< (const edge &t)const{
        return w > t.w;
    }
}e[M];
int p[N];
int val[N]; // 根节点上的值保存另外两个点的距离
vi G[N];
int sz[N], dep[N], fa[N], son[N], top[N];

int find(int x){
    if(p[x] != x) return p[x] = find(p[x]);
    return p[x];
}

void dfs1(int u){
    sz[u] = 1;
    dep[u] = dep[fa[u]] + 1;
    for(auto &v : G[u]){
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
    for(auto &v : G[u]){
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
    int n, m; cin >> n >> m;
    int cnt = n;
    for(int i = 0; i < m; i++) cin >> e[i].u >> e[i].v >> e[i].w;
    sort(e, e + m);
    iota(p, p + 2 * n, 0ll);
    for(int i = 0; i < m; i++){
        int u = find(e[i].u), v = find(e[i].v), w = e[i].w;
        if(u == v) continue;
        val[++cnt] = w;
        p[u] = p[v] = cnt;
        G[cnt].push_back(u); // 重构树的形状已经确定，所以在根节点保存其他节点即可
        G[cnt].push_back(v);
    }
    for(int i = 1; i <= cnt; i++) // 注意每棵树都需要初始化
        if(p[i] == i) dfs1(i), dfs2(i, i);
    
    int q; cin >> q;
    while(q--){
        int u, v; cin >> u >> v;
        if(find(u) != find(v)) cout << -1 << endl;
        else cout << val[lca(u, v)] << endl;
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

## [P2245 星际导航](https://www.luogu.com.cn/problem/P2245)

与前一题类似，双倍经验

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 2e5 + 10, M = 3e5 + 10, inf = 0x3f3f3f3f;
struct edge{
    int u, v, w;
    bool operator< (const edge &t)const{
        return w < t.w;
    }
}e[M];
int p[N];
int val[N];
vi G[N];
int sz[N], dep[N], fa[N], son[N], top[N];

int find(int x){
    if(p[x] != x) return p[x] = find(p[x]);
    return p[x];
}

void dfs1(int u){
    sz[u] = 1;
    dep[u] = dep[fa[u]] + 1;
    for(auto &v : G[u]){
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
    for(auto &v : G[u]){
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
    int n, m; cin >> n >> m;
    int cnt = n;
    for(int i = 0; i < m; i++) cin >> e[i].u >> e[i].v >> e[i].w;
    sort(e, e + m);
    iota(p, p + 2 * n, 0ll);
    for(int i = 0; i < m; i++){
        int u = find(e[i].u), v = find(e[i].v), w = e[i].w;
        if(u == v) continue;
        val[++cnt] = w;
        p[u] = p[v] = cnt;
        G[cnt].push_back(u);
        G[cnt].push_back(v);
    }
    for(int i = 1; i <= cnt; i++)
        if(p[i] == i) dfs1(i), dfs2(i, i);
    
    int q; cin >> q;
    while(q--){
        int u, v; cin >> u >> v;
        if(find(u) != find(v)) cout << "impossible" << endl;
        else cout << val[lca(u, v)] << endl;
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

## [CF1706E Qpwoeirut and Vertices](https://codeforces.com/contest/1706/problem/E)

这道题的核心在于：对于每个查询区间 $[l,r]$，要找出一个最小的 $k$，使得只使用编号为 $1,2,…,k$ 的前 $k$ 条边，就能让区间内任意两点两两连通。直接对每个查询套一遍二分＋联通判断（$DSU$）显然超时，因为 $\sum q$ 和 $\sum m$ 都可达 $2×10^5$。

下面分步讲解这种「离线预处理＋区间最值查询」的做法。

---

### 1. 用「Kruskal 重构树」记录连通时间

我们把所有原始节点标号为 $1$ 到 $n$。然后按边的编号（也就是按时间顺序）从小到大枚举每条边，做并查集（$DSU$）合并：

1. 初始时，每个集合的代表就是它自己，集合编号就用 $1…n$。
    
2. 当遍历到第 $i$ 条边 ($u_i,v_i$) 时，如果 $u_i$ 和 $v_i$ 已经在同一个集合，就跳过；否则把两个集合合并：
    
    - 新建一个「并查树」节点，编号为 `++cnt`，记作合并节点。
        
    - 令这个新节点的 `val[cnt] = i`，表示在时间（边编号）$i$ 时，两个子树合并。
        
    - 在树中把新节点指向被合并的两个子集合的根节点。
        
    - 在 $DSU$ 里把这两个集合的根都指向 `cnt`。
        

最终，我们会得到一棵 **森林**（实际上图是连通的，所以最后是一棵树），树中共有 $cnt \le n+m$ 个节点。原始 $1…n$ 号节点是叶子，编号 $n+1$ 开始的都是合并节点，每个合并节点上保存了它对应的「合并时间」$val[⋅]$。

**性质**：在这棵树中，任意两个原始节点 $a,b$ 的 **最近公共祖先**（$LCA$）所对应的合并时间，就是它们在并查集里连通所需要的最小边编号。

---

### 2. 重链剖分（$HLD$）＋快速 $LCA$

为了多次 $\mathcal O(1)$ 查询任意两叶子的 $LCA$，我们对这棵树跑一次：

1. **$dfs1$** 计算每个子树大小、找到重儿子；
2. **$dfs2$** 给每个节点打上 “$top$ 链” 标签，保证同一条重链上的节点在序列上是连续的；
3. 用“跳链”的方式在 $\mathcal O(\log N)$ 内求两点 LCA。

至此，任何一对原始节点 $(i,i+1)$ 的连通时间：$T_i  =  val(LCA(i,i+1))$ 都能快速得到。

---

### 3. 化简查询：区间内任意两两连通 $<=>$ 相邻点都连通

当 $l = r$ 时，显然答案 $k = 0$；
当 $l<r$ 时，$[l,r]$ 区间中的点全部两两连通 $<=>$ $l$ 与 $l+1$ 连通，$l+1$ 与 $l+2$ 连通 ⋯ $r−1$ 与 $r$ 连通。此时 $\displaystyle k = \max_{i = l}^{r - 1} T_i$

---

### 4. 区间最大值预处理——线段树

把所有 $T_1,T_2,\dots,T_{n-1}$ 存到一个大小 $n-1$ 的数组 `nw[i] = T_i`。再对这个数组建一棵线段树，支持：
- **建树**：$\mathcal O(n)$；
- **单次区间最大值查询** $\displaystyle \max_{i=L}^{R}nw[i]：\mathcal O(\log n)$。

对于每个查询 $(l,r)$，直接询问线段树区间 $[l,\,r-1]$ 的最大值，就是答案。

---

### 5. 复杂度分析

- 每个测试用例：
    
    - 建 $DSU$ 并做 $Kruskal$ 重构树：$\mathcal O((n+m) α(n+m))$；
        
    - 一次 HLD：$\mathcal O((n+m))$；
        
    - 建线段树：$\mathcal O(n)$；
        
    - 每个查询 $\mathcal O(\log n)$，一共 $\sum q\le2\times10^5$。
        
总的对所有测试点，时间复杂度约为：$\mathcal O\Bigl((n+m)\,\alpha(n+m) + (n+m) + n + q\log n\Bigr)$

满足 $\sum n\le10^5、\sum m,q\le2\times10^5$ 的要求。

---

### 总结

1. **$Kruskal$ 重构树** 记录「两点连通所需最早的边编号」；
2. $HLD+LCA$ 快速得到相邻节点 $(i,i+1)$ 的连通时间 $T_i$；
3. **线段树** 在 $\mathcal O(\log n)$ 内做区间最大值查询，得到答案。

这种思路把每个查询的「最小 $k$」问题，转化为「区间最大值」问题，既避免了在线二分也能批量快速回答。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define rt tr[u]
#define ls tr[u << 1]
#define rs tr[u << 1 | 1]
const int N = 2e5 + 10, M = 3e5 + 10, inf = 0x3f3f3f3f;
struct edge{
    int u, v;
}e[N];
int p[N];
int find(int x) { return (p[x] == x ? x : p[x] = find(p[x])); }
int val[N];
vi G[N];
int sz[N], dep[N], fa[N], son[N], top[N];
int nw[N];

struct Node{
    int l, r, mx;
}tr[N << 2];

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
void pushup(int u){
    rt.mx = max(ls.mx, rs.mx);
}
void build(int u, int l, int r){
    rt = {l, r, nw[l]};
    if(l == r) return;
    int mid = l + r >> 1;
    build(u << 1, l, mid), build(u << 1 | 1, mid + 1, r);
    pushup(u);
}
int query(int u, int l, int r){
    if(l <= rt.l && rt.r <= r) return rt.mx;
    int mid = rt.l + rt.r >> 1;
    int mx = 0;
    if(l <= mid) mx = query(u << 1, l, r);
    if(r > mid) mx = max(mx, query(u << 1 | 1, l, r));
    return mx;
}

void solve(){
    int n, m, q; cin >> n >> m >> q;
    for (int i = 1; i <= 2 * n; i++){
        G[i].clear();
        fa[i] = son[i] = 0;
    }
    for(int i = 1; i <= m; i++) cin >> e[i].u >> e[i].v;
    int cnt = n;
    iota(p, p + 2 * n, 0ll);
    for(int i = 1; i <= m; i++){
        int u = find(e[i].u), v = find(e[i].v);
        if(u == v) continue;
        val[++cnt] = i;
        p[u] = p[v] = cnt;
        G[cnt].push_back(u), G[cnt].push_back(v);
    }
    for(int i = 1; i <= cnt; i++)
        if(p[i] == i) dfs1(i), dfs2(i, i);
    for(int i = 1; i < n; i++) nw[i] = val[lca(i, i + 1)];
    build(1, 1, n - 1);
    for(int i = 1; i <= q; i++){
        int l, r; cin >> l >> r;
        cout << query(1, l, r - 1) << " \n"[i == q];
    }
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF1578L Labyrinth](https://codeforces.com/contest/1578/problem/L)

首先注意到一个性质，刚开始的时候身体宽度没有限制(身体宽度 $<= min_w$)，即刚开始可以在图上随便走，所以从 1 号节点开始的限制等于无。

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