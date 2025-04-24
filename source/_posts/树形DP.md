---
title: 树形DP
date: 2025-04-21 15:09:26
tags:
  - DP
  - 树形DP
categories:
  - 算法笔记
mathjax: true
---

#### [abc394_f. Alkane](https://atcoder.jp/contests/abc394/tasks/abc394_f)(树形DP)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
#define arr3 array<int, 3>
#define lowbit(x) (x) & (-x)
const int N = 2e5 + 10, inf = 0x3f3f3f3f, Inf = INT_MAX, mod = 1e9 + 7;
int n, du[N], vis[N], dp[N][2], ans;
// dp[i][0]:以自己为根 dp[i][1]: 把自己当儿子
vi G[N];

void dfs(int u){
    vis[u] = 1;
    vi tmp;
    for(int v : G[u])
        if(du[v] >= 4 && !vis[v]){
            dfs(v);
            tmp.push_back(dp[v][1]);
        }
    sort(tmp.rbegin(), tmp.rend()); // 从大到小排序
    dp[u][1] = 1;
    for(int i = 0; i < tmp.size() && i < 3; i++) dp[u][1] += tmp[i];
    dp[u][0] = dp[u][1];
    if(tmp.size() >= 4) dp[u][0] += tmp[3];
    ans = max(ans, dp[u][0]);
}

void solve(){
    cin >> n;
    for(int i = 1; i < n; i++){
        int a, b; cin >> a >> b;
        G[a].push_back(b);
        G[b].push_back(a);
        du[a]++, du[b]++;
    }
    for(int i = 1; i <= n; i++)
    // 只算主干, 总节点 = 3 * 主干 + 2;
        if(du[i] >= 4 && !vis[i]) dfs(i);
    cout << (ans == 0 ? -1 : 3 * ans + 2) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [abc397_e. Path Decomposition of a Tree](https://atcoder.jp/contests/abc397/tasks/abc397_e)(树形DP)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
#define arr3 array<int, 3>
#define lowbit(x) (x) & (-x)
const int N = 2e5 + 10, inf = 0x3f3f3f3f, Inf = INT_MAX, mod = 1e9 + 7;
int n, k, sz[N];
vi G[N];

void no(){
    cout << "No" << endl;
    exit(0); // 终止程序
}

void dfs(int u, int fa){
    sz[u] = 1;
    int du = 0;
    for(auto v : G[u])
        if(v != fa){
            dfs(v, u);
            if(sz[v]){
                du++;
                sz[u] += sz[v];
            }
        }
    if(sz[u] < k){
        if(u == 1 || du > 1) no();
    }else if(sz[u] == k){
        if(du > 2) no();
        sz[u] = 0;
    }else no(); // sz[u] > k;
}

void solve(){
    cin >> n >> k;
    for(int i = 1; i < n * k; i++){
        int u, v; cin >> u >> v;
        G[u].push_back(v);
        G[v].push_back(u);
    }
    dfs(1, 0);
    cout << "Yes" << endl;
    return;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [CF1056D. Decorate Apple Tree](https://codeforces.com/problemset/problem/1056/D)(树形DP)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/05/0518/solution/cf1056d.md)**
**问题等价于问以每个点为根的子树有多少叶子节点**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 1e6 + 10, M = 1e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, h[N], e[N], ne[N], idx, cnt[N], x;

void add(int a, int b){
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
    return;
}

void dfs(int u){
    for(int i = h[u]; ~i; i = ne[i]){
        int v = e[i];
        dfs(v);
        cnt[u] += cnt[v];
    }
    if(cnt[u] == 0) cnt[u]++;
}

void solve(){
    cin >> n;
    memset(h, -1, sizeof h);
    for(int i = 2; i <= n; i++){
        cin >> x; // 读入根节点 
        add(x, i);
    }
    dfs(1);
    sort(cnt + 1, cnt + n + 1);
    for(int i = 1; i <= n; i++)
        cout << cnt[i] << " \n"[i == n];
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [CF1083A. The Fair Nut and the Best Path](https://codeforces.com/problemset/problem/1083/A)(树形DP)(1800)
**提示 1：** 路径总共有 $\mathcal{O}(n^2)$ 条，无法直接枚举。尝试固定一个重要点。
**提示 2：** 固定一点后，路径的总价值如何计算？
**提示 3：** 总价值最大时，是否可能出现负数情形。
这里用到了**处理树上路径价值最大值的常用方法：固定端点的[最近公共祖先](https://oi-wiki.org/graph/lca/)**，可以查看链接了解树上路径的这一个特殊点。
`显然树上任意路径 u → v 都可以转换为 u → LCA(x, y) 和 LCA(x, y) → v 两部分`
其实，我们在计算树的直径的时候也用过类似的方法。下面简单进行阐述。
我们考虑所有节点往下的路径中最长的两条，这件事情可以通过 DP 实现。接着计算所有节点的这两条路径之和的最大值，这两条路径合在一起一定构成树上的一条路径。由于路径一定存在一个树上的“最高点”，因此我们枚举完所有的“最高点”，也就一定考虑完全了所有的路径，因此得到的所有最大值再取最大值即为直径。
这题也是类似的，先把图看成有根图。接着，我们枚举路径端点的 LCA，看其向下的两条最大价值的路径。但这个路径不像原来一样可以通过距离和计算，这里计算的路径包含了点的正权和边的负权。
那么如何定义从“最高点”出发向下的路径的价值呢？直接去掉最高点，剩下的每个点和它上方的边配对产生权重，重新产生新的该边的边权，形成类似于直径中定义的距离即可。计算总权和时再加入最高点即可。
而最大 / 次大权重边可以通过树上 DP 实现（用最长路径向父节点更新最长 / 次长路径即可），时间复杂度为 $\mathcal{O}(n)$ .
为何这样找到的路径不会中间出现负数呢？因为一旦出现负数，我们可以把当前路径从此截断，后面的路径权值更大。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
#define pii pair<int, int>
#define arr3 array<int, 3>
const int N = 3e5 + 10, M = 6e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7, MOD = 998244353;
int n, a[N], h[N], e[M], ne[M], w[M], idx, dp[N], ans;
// dp[i]: 以 v 为结束的子树内最长链

void add(int a, int b, int c){
    w[idx] = c;
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}

void dfs(int u, int fa){
    int bst1 = 0, bst2 = 0;
    for(int i = h[u]; ~i; i = ne[i]){
        int v = e[i];
        if(v == fa) continue;
        dfs(v, u);
        int contrib = dp[v] - w[i];
        if(contrib > 0){
            if(contrib >= bst1) bst2 = bst1, bst1 = contrib;
            else if(contrib > bst2) bst2 = contrib;
        }
    }
    dp[u] = a[u] + bst1;
    ans = max(ans, dp[u] + bst2);
}

void solve(){
    memset(h, -1, sizeof h);
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i];
    for(int i = 1; i < n; i++){
        int a, b, c; cin >> a >> b >> c;
        add(a, b, c), add(b, a, c);
    }
    dfs(1, -1);
    cout << ans << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [CF1833G. Ksyusha and Chinchilla](https://codeforces.com/problemset/problem/1833/G)(树形DP)(1800)([与abc397_e类似](https://atcoder.jp/contests/abc397/tasks/abc397_e))
**提示 1：** 我们要把树拆成一系列组件。请从最容易确定组件的位置开始拆。
**提示 2：** 上述过程相当于一个树形 DP 。
简单的贪心。
我们从叶子出发，这个叶子一定和其父节点在同一个组件中。剩下一个元素呢？
- 其有可能是另一个叶子，如果这样，其父节点除了这两个子节点外，剩余的子树都应该自己拆分。
- 其有可能是父节点的父节点，此时，父节点和父节点的父节点的其余子树都应该能够自行拆分为组件。
- 由于子树的处理与上述过程过程一致，因此可以在子树中不断找到叶子进行上述组件的寻找，直到没有其他连接的子树。在这种情况下，相当于找到了一个大小为 $3$ 的子树，再把它切掉。
上述逻辑进一步提炼，可以发现，我们从叶子出发不断往根走，贪心地取大小为 $3$ 的子树即满足要求。
从代码逻辑来看，相当于自叶子往上的 DP，一旦遇到一个当前大小为 $3$ 的子树，就切断其与其父亲之间连的边。
时间复杂度为 $\mathcal{O}(n)$ .
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
#define pii pair<int, int>
#define arr3 array<int, 3>
#define m (2 * n + 10)
const int N = 1e4 + 10, M = 2e4 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7, MOD = 998244353;
int n, idx;

void solve(){
    cin >> n;
    idx = 0;
    vi sz(n + 1, 1), h(n + 1, -1), e(m), ne(m), ans;

    function<void(int, int)> add = [&](int a, int b){
        e[idx] = b;
        ne[idx] = h[a];
        h[a] = idx++;
    };

    function<void(int, int)> dfs = [&](int u, int fa){
        for(int i = h[u]; ~i; i = ne[i]){
            int v = e[i];
            if(v == fa) continue;
            dfs(v, u);
            if(sz[v] == 0) ans.push_back(i);
            else sz[u] += sz[v];
        }
        if(sz[u] == 3) sz[u] = 0;
    };

    for(int i = 1; i < n; i++){
        int a, b; cin >> a >> b;
        add(a, b), add(b, a);
    }

    dfs(1, -1);
    
    if((ans.size() + 1) * 3 == n){
        cout << ans.size() << endl;
        // 同一条边加了两次, 所以 0: 0/1  1: 2/3  2: 4/5
        for(auto &x : ans) cout << x / 2 + 1 << " \n"[x == ans.back()];
        if(n == 3) cout << endl; // 特判 n == 3
    }else cout << -1 << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [CF743D. Chloe and pleasant prizes](https://codeforces.com/problemset/problem/743/D)(树形DP)(LCA)(1800)(与[CF1083A](https://codeforces.com/problemset/problem/1083/A)类似)
**提示 1：** 翻译题目：找到以 $1$ 为根的有根树的两个不交子树的最大权值和。
**提示 2：** 一定能找到一个点，使得我们选取的树都在这个点对应的不同的子树内。对这个点而言，选取最大的两个子树即可。
**提示 3：** 如何维护某个点对应的最大子树。
如果你没看懂题的话，请先看提示 1 的题意。
怎么找到两个不交的子树呢？
考虑两个不交的子树，其根节点往整棵树的根节点不断延申，一定会汇集到一个点（即 LCA，lowest common ancestor），则对于 LCA 对应的子树而言，这两棵子树也在 LCA 连出的不同的子树内。
于是，只需对于 LCA 连出的不同的子树分别求最大子树和，再选取最大的两个相加即可。
而只需枚举所有可能的 LCA 即可覆盖所有的子树对。
接下来便只有一件事：如何维护一棵树内的最大子树和。
首先，维护一棵子树内的权值和，直接使用树形 DP 即可，用所有子树的结果相加再加上根节点。
而同时最大子树和等于各个子树的最大子树和与整棵树的和取最大值。因此也可以树形 DP 维护。
综上，使用树形 DP 完成最大子树和的维护，再枚举 LCA ，对于每个 LCA 选取所有儿子中最大的两个子树和即可。
时间复杂度为 $\mathcal{O}(n)$ 。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 2e5 + 10, M = 4e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
const int INF = 0x3f3f3f3f3f3f3f3f, MOD = 998244353;
int n, a[N], h[N], e[M], ne[M], idx;
int sum_subtree[N], dp[N], ans = -INF;
// sum_subtree[i]: 以 i 为根的整棵子树的权值和。
// dp[i]: 以 i 为根的子树中，权值和最大的某棵子树的值。

void add(int a, int b){
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}

void dfs(int u, int fa){
    sum_subtree[u] = a[u];
    for(int i = h[u]; ~i; i = ne[i]){
        int v = e[i];
        if(v == fa) continue;
        dfs(v, u);
        sum_subtree[u] += sum_subtree[v];
    }
    dp[u] = sum_subtree[u];
    int bst1 = -INF, bst2 = -INF;
    for(int i = h[u]; ~i; i = ne[i]){
        int v = e[i];
        if(v == fa) continue;
        if(dp[v] >= bst1){
            bst2 = bst1;
            bst1 = dp[v];
        }else if(dp[v] > bst2) bst2 = dp[v];
        dp[u] = max(dp[u], dp[v]);
    }
    if(bst2 > -INF) ans = max(ans, bst1 + bst2);
}

void solve(){
    memset(h, -1, sizeof h);
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i];
    for(int i = 1; i < n; i++){
        int u, v; cin >> u >> v;
        add(u, v); add(v, u);
    }
    dfs(1, 0);
    if(ans > -INF) cout << ans << endl;
    else cout << "Impossible" << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```