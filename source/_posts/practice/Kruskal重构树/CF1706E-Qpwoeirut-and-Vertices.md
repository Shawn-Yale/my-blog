---
title: CF1706E Qpwoeirut and Vertices
date: 2025-07-31 14:43:53
tags:
  - Kruskal重构树
  - LCA
  - 树链剖分
  - 线段树
  - 提高+/省选−
categories:
  - 算法刷题
mathjax: true
---

**[题目链接](https://codeforces.com/contest/1706/problem/E)**

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