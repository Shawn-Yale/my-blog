---
title: P4377【USACO18OPEN】Talent Show G
date: 2025-08-02 13:57:29
tags:
  - 01分数规划
  - 二分答案
  - 01背包
  - 提高+/省选−
categories:
  - 算法刷题
mathjax: true
---

**[题目链接](https://www.luogu.com.cn/problem/P4377)**

本题多了分母至少为 $W$ 的限制，无法使用贪心算法（排序）。可以考虑 **01背包**。

**01 背包**：用来解决「每件物品只能取 0 或 1 次，满足重量或容量约束下，最大化价值」的问题。

二分一个答案 $x$，把 $w_i$ 作为第 i 个物品的重量，t_i - x * w_i 作为第 i 个物品的价值，那么 $f[n][W]$ 就是最大化价值。
**注意：$\sum w_i$ 可能超过 $W$，直接视为 $W$ 即可。**
若 $f[n][W] \geq 0$，就继续最大化 $x$。

时间复杂度：$\mathcal O(nW * log(1e8))$

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 3e2 + 10, M = 1e3 + 10, inf = 0x3f3f3f3f;

int n, W, w[N], t[N];
double dp[M];

bool check(double x){
    for(int i = 1; i <= W; i++) dp[i] = -inf;
    for(int i = 1; i <= n; i++)
        for(int j = W; j >= 0; j--){
            int k = min(W, j + w[i]);
            dp[k] = max(dp[k], dp[j] + t[i] - x * w[i]);
        }
    return dp[W] >= 0;
}

void solve(){
    cin >> n >> W;
    for(int i = 1; i <= n; i++) cin >> w[i] >> t[i];
    double l = 0, r = 1e3;
    while(r - l > 1e-5){
        double mid = (l + r) / 2;
        if(check(mid)) l = mid;
        else r = mid;
    }
    cout << (int)(r * 1000) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```