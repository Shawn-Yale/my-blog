---
title: EPIC Institute of Technology Round August 2024 (Div. 1 + Div. 2)
date: 2025-05-01 20:59:13
tags:
  - Codeforces
categories:
  - 比赛
---

## [CF2002A. Distanced Coloring](https://codeforces.com/contest/2002/problem/A)(暴力)(800)
显然只需 `k * k` 的网格的颜色各不相同
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
int n, m, k;

void solve(){
    cin >> n >> m >> k;
    cout << min(m, k) * min(n, k) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF2002B. Removals Game](https://codeforces.com/contest/2002/problem/B)(博弈论)(1000)
显然 `Bob` 很难赢得这个游戏，考虑一下 `Bob`赢的情况。
只有当 `Bob` 每次都取与 `Alice`相同的元素时，`Bob`才能赢。
也就是`a = b` 或者 `a` 翻转之后 与 `b` 相同
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n;

void solve(){
    cin >> n;
    vi a(n), b(n);
    for(auto &x : a) cin >> x;
    for(auto &x : b) cin >> x;
    cout << (a == b || a == vi(b.rbegin(), b.rend()) ? "Bob" : "Alice") << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF2002C. Black Circles](https://codeforces.com/contest/2002/problem/C)(数学)(1200)
可以走到终点的条件：起点与终点的距离 > 所有圆心到终点的距离
![EPIC Institute of Technology Round August 2024](/images/EPIC%20Institute%20of%20Technology%20Round%20August%202024.webp)
**反证**：如果 A -> B 会与圆C有交点D，那边有`AD = CD`，又因为`CD > CB - DB`，所以`AD > CB - DB`，也就是`AB > CB`。再考虑一下临界条件，可以知道，`AB = CB`时，会在终点恰好碰到圆周，与题目要求的不符。综上，只需要`AB > CB`即可满足题目要求。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n, sx, sy, ex, ey, dis;

void solve(){
    cin >> n;
    vi x(n), y(n);
    for(int i = 0; i < n; i++) cin >> x[i] >> y[i];
    cin >> sx >> sy >> ex >> ey;
    dis = (sx - ex) * (sx - ex) + (sy - ey) * (sy - ey);
    for(int i = 0; i < n; i++)
        if((ex - x[i]) * (ex - x[i]) + (ey - y[i]) * (ey - y[i]) <= dis)
            return void(cout << "NO" << endl);
    cout << "YES" << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
