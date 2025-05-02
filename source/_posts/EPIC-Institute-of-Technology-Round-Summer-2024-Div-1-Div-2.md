---
title: EPIC Institute of Technology Round Summer 2024 (Div. 1 + Div. 2)
date: 2025-05-01 22:36:48
tags:
  - Codeforces
categories:
  - 比赛
---

## [CF1987A. Upload More RAM](https://codeforces.com/contest/1987/problem/A)(800)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
int n, k;

void solve(){
    cin >> n >> k;
    cout << (n - 1) * k + 1 << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF1987B. K-Sort](https://codeforces.com/contest/1987/problem/B)(贪心)(1000)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 1e5 + 10;
int n, a[N], pre, cnt, ans;
// pre: 前面数字的最大值
// cnt: 需要操作几次

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i];
    pre = a[1], cnt = ans = 0;
    for(int i = 2; i <= n; i++){
        if(a[i] >= pre){
            pre = a[i];
            continue;
        }
        ans += pre - a[i];
        cnt = max(cnt, pre - a[i]);
    }
    ans += cnt;
    cout << ans << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF1987C. Basil's Garden](https://codeforces.com/contest/1987/problem/C)(线性DP)(1200)
发现最后一个变为 0 的一定是第一个，对于每一个位置而言，如果它后面的位置没变为 0 ，那么它就不可能变为 0，所以它变为 0 的时刻最小应该是在它后一个位置变为 0 的时刻加 1，如果这个位置太高的话，就需要 `a[i]` 个时间变为 0，所以当前位置变为 0 的时刻就是二者取最大值。
设计 `dp[i]` 表示第 i 朵花变成 0 需要的时间，有转移方程: `dp[i] = max(dp[i + 1] + 1, a[i])`
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 1e5 + 10;
int n, a[N], dp[N];
// dp[i]: 第 i 朵花变成 0 需要的时间;

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i];
    memset(dp, 0, sizeof dp);
    for(int i = n; i; i--)
        dp[i] = max(dp[i + 1] + 1, a[i]);
    cout << dp[1] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF1987D. World is Mine](https://codeforces.com/contest/1987/problem/D)(背包)(1800)(有点阴间了)
### 一、问题回顾
- 有 $n$ 个蛋糕，第 $i$ 个蛋糕有美味值 $a_i$。  
- Alice 和 Bob 轮流吃，Alice 先手：  
  1. **Alice** 只能吃一个比她之前所有吃过的都要**更大**的美味值。  
  2. **Bob** 可以随意吃任何剩下的蛋糕，目的是阻止 Alice 吃更多。  
- 当某一方无法合法吃蛋糕时，游戏结束。记 Alice 吃掉的蛋糕数为 $x$，Alice 想**最大化** $x$，Bob 想**最小化** $x$。求在最优对抗下的 $x$。
### 二、关键观察：相同美味值的“阻拦成本”
- 如果所有美味值都**不同**，Alice 每次都吃最小可用值，Bob 无法有效阻拦。  
- **重点**在于“重复值”——一批相同美味值的蛋糕：  
  - **Alice**：吃掉其中任意一个，就算拿下了这一美味级别。  
  - **Bob**：要彻底铲除这一级别，必须在 Alice 下次吃之前，把这一批**全吃光**，成本等于批大小 $c$。
因此，把相同美味值的蛋糕看成一组，记下它的大小 $c$。
### 三、减少问题规模：分组后只看“组数”
1. **统计频次**  
   对原数组按值统计：`cnt[val]++`。  
2. **抽出非零组**  
   将所有 `cnt[val]>0` 收入数组 `vec`，元素是各组大小。  
   - 令 $m=\mathtt{vec.size()}$，即不同美味值种类数。  
3. **先手组（最小值）必由 Alice 吃**  
   Alice 一定先吃最小那组，Bob 无法拦截。后续只对 `vec[1..m-1]` 做决策。
### 四、把博弈建模成“背包 DP”

定义一维数组  
$$
\mathtt{dp}[j] = \text{在 Alice 最终吃掉恰好 }j\text{ 组时，Bob 最多能拦截多少组。}
$$

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n, m;
// dp[j]：表示 Alice 最终吃了恰好 j 组，Bob 最多能拦截多少组。

void solve(){
    cin >> n;
    vi a(n + 1), cnt(n + 1, 0), vec, dp(n + 1, 0);
    for(int i = 1; i <= n; i++) cin >> a[i], cnt[a[i]]++;
    for(int i = 1; i <= n; i++)
        if(cnt[i]) vec.push_back(cnt[i]);
    m = vec.size();
    // 从 vec[1] 开始做背包, 跳过 vec[0]
    for(int i = 1; i < m; i++)
        for(int j = i; j >= vec[i]; j--)
            dp[j + 1] = max(dp[j + 1], dp[j - vec[i]] + 1);
    cout << m - *max_element(dp.begin() + 1, dp.end()) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF1987E. Wonderful Tree!](https://www.luogu.com.cn/problem/CF1987E)(树形DP)(2000)
**父节点和子节点有特定的关系，可以考虑树形DP**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 5e3 + 10, M = 4e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
const int INF = 0x3f3f3f3f3f3f3f3f, MOD = 998244353;
int n, a[N], p[N], dep[N], dp[N][N], cost;
//dp[i][j]: 将节点i的所有子树都变成wonderful后，节点i有dp[i][j]次机会花j块钱增加1
vi e[N];

void dfs(int u){
    int sum = 0; // 子节点总和
    for(auto v : e[u]){
        dfs(v);
        dep[u] = max(dep[v] + 1, dep[u]);
        for(int i = 1; i <= dep[v]; i++){
            dp[u][i + 1] += dp[v][i];
            dp[u][i + 1] = min(dp[u][i + 1], INF);
			// 取 min, 将最大值截断为INF, 防止爆long long
        }
        sum += a[v];
    }
    dp[u][1] = max(sum - a[u], 0ll);
    dp[u][dep[u]] = INF;
    int i = 2;
    while(e[u].size() && a[u] > sum){
        int add = min(a[u] - sum, dp[u][i]);
        cost += add * (i - 1);
        sum += add;
        dp[u][i] -= add;
        i++;
    }
}

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++) e[i].clear(), dep[i] = 1;
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= n; j++) dp[i][j] = 0;
    cost = 0;
    for(int i = 1; i <= n; i++) cin >> a[i];
    for(int i = 2; i <= n; i++) cin >> p[i], e[p[i]].push_back(i);
    dfs(1);
    cout << cost << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```