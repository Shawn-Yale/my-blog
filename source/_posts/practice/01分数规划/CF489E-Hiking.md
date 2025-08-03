---
title: CF489E Hiking
date: 2025-08-03 16:41:47
tags:
  - 01分数规划
  - 二分答案
  - 线性DP
  - 提高+/省选−
categories:
  - 算法刷题
mathjax: true
---

**[题目链接](https://codeforces.com/contest/489/problem/E)**

时间复杂度：$\mathcal O(n^2 * log(1e13))$

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 1e3 + 10, inf = 0x3f3f3f3f;

int n, nl;
int p[N], b[N], pre[N];
double dp[N];
// dp[i]: 从起点0 走到第 i 号休息点，修改后总代价最小值

bool check(double x){
    for(int i = 1; i <= n; i++){
        dp[i] = inf;
        for(int j = 0; j < i; j++){
            double tmp = dp[j] + sqrt(abs(p[i] - p[j] - nl)) - x * b[i];
            if(dp[i] > tmp){
                dp[i] = tmp;
                pre[i] = j;
            }
        }
    }
    return dp[n] >= 0;
}

void dfs(int u){
    if(pre[u]) dfs(pre[u]);
    cout << u << " \n"[u == n];
}

void solve(){
    cin >> n >> nl;
    for(int i = 1; i <= n; i++) cin >> p[i] >> b[i];
    double l = 0, r = 1e6;
    while(r - l > 1e-7){
        double mid = (l + r) / 2;
        if(check(mid)) l = mid; // 若 dp[n] >= 0, 则说明 mid 可以尝试取更大
        else r = mid;
    }
    dfs(n);
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    // cin >> T;
    for(int i = 1; i <= T; i++) solve();
    return 0;
}
```