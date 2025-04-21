---
title: 树形DP
date: 2025-04-21 15:09:26
tags: [DP, 树形DP]
categories: [算法笔记]
mathjax: true
---

#### [AcWing 285. 没有上司的舞会](https://www.acwing.com/problem/content/description/287/)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
#define arr3 array<int, 3>
#define lowbit(x) (x) & (-x)
const int N = 6e3 + 10, inf = 0x3f3f3f3f, Inf = INT_MAX, mod = 1e9 + 7;
int n, happy[N], h[N], e[N], ne[N], vis[N], dp[N][2], idx;
// vis标记是否有上司, 1 为有上司
// dp[i][0] 表示这个人不参加 dp[i][1] 表示这个人参加

void add(int a, int b){
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
    return;
}

void dfs(int u){
    dp[u][1] = happy[u];
    for(int i = h[u]; ~i; i = ne[i]){
        int j = e[i];
        dfs(j);
        dp[u][0] += max(dp[j][0], dp[j][1]);
        dp[u][1] += dp[j][0];
    }
}

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> happy[i];
    memset(h, -1, sizeof h);
    for(int i = 1; i < n; i++){
        int u, v; cin >> u >> v;
        add(v, u);
        vis[u] = 1;
    }
    int st = 1;
    while(vis[st]) st++;
    dfs(st);
    cout << max(dp[st][0], dp[st][1]) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [abc394_f. Alkane](https://atcoder.jp/contests/abc394/tasks/abc394_f)
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
#### [abc397_e. Path Decomposition of a Tree](https://atcoder.jp/contests/abc397/tasks/abc397_e)
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