---
title: ThemeCP Round 3(Level 15)
date: 2025-05-14 19:20:57
tags:
  - ThemeCP
categories:
  - 比赛
---

## T1：[CF1875A. Jellyfish and Undertale](https://codeforces.com/problemset/problem/1875/A)(900)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

void solve(){
    int a, b, n;
    cin >> a >> b >> n;
    for(int i = 1; i <= n; i++){
        int x; cin >> x;
        x = min(a - 1, x);
        b += x;
    }
    cout << b << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## T2：[CF1780B. GCD Partition](https://codeforces.com/problemset/problem/1780/B)(前缀和)(数学)(1100)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int n; cin >> n;
    int ans = 1;
    vi a(n + 1, 0);
    for(int i = 1; i <= n; i++) cin >> a[i], a[i] = a[i - 1] + a[i];
    for(int i = 1; i <= n - 1; i++)
        ans = max(ans, gcd(a[i], a[n] - a[i]));
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
## T3：[CF1975C. Chamo and Mocha's Array](https://codeforces.com/problemset/problem/1975/C)(贪心)(1200)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int n; cin >> n;
    vi a(n + 1, 0);
    for(int i = 1; i <= n; i++) cin >> a[i];
    int ans = 0;

    // 相邻的两个取 med
    for(int i = 1; i <= n - 1; i++)
        ans = max(ans, min(a[i], a[i + 1]));
    // 相邻的三个取 med
    for(int i = 1; i <= n - 2; i++)
        ans = max(ans, min(a[i], a[i + 2]));
    
    //之后相邻的数都被包含在 两个数 和 三个数 的情况中
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
## T4：[CF1840D Wooden Toy Festival](https://codeforces.com/problemset/problem/1840/D)(二分答案)(1400)
**赛时**分析到应该将所有的值分到三块范围，直接去想枚举第一块范围的边界，然后再二分求第二块和第三块了。
其实还有一种做法：
**题意转化为：** 给定数组a。设定三个整数 $x_1$，$x_2$，$x_3$。然后为每个 $a_i$ 选择一个 $x_j$，求**最小化 $|a_i - x_j|$ 的最大值**。看到**最小化最大值**，可以想到**二分答案**，只需 $logn，n \le 2e8$ 的时间复杂度。

下面给出两种做法：
1. **赛时做法**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int inf = 0x3f3f3f3f;

void solve(){
    int n; cin >> n;
    vi a(n);
    for(auto &x : a) cin >> x;
    sort(a.begin(), a.end());
    a.erase(unique(a.begin(), a.end()), a.end());
    n = a.size();
    if(n <= 3) return void(cout << 0 << endl);

    int ans = inf;
    for(int i = 0; i < n - 2; i++){
        int tmp = a[i] - a[0];
        int l = i + 1, r = n - 2;
        int cur = inf;

        auto check = [&](int mid) -> bool{
            cur = min(cur, max(a[mid] - a[i + 1], a[n - 1] - a[mid + 1]));
            return (a[mid] - a[i + 1] < a[n - 1] - a[mid + 1] ? true : false);
        };

        while(l < r){
            int mid = (l + r) >> 1;
            if(check(mid)) l = mid + 1;
            else r = mid;
        }
        cur = min(cur, max(a[l] - a[i + 1], a[n - 1] - a[l + 1]));
        tmp = max(tmp, cur);
        ans = min(ans, tmp);
    }
    cout << (ans + 1) / 2 << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
2. **赛后**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int n; cin >> n;
    vi a(n);
    for(auto &x : a) cin >> x;
    sort(a.begin(), a.end());
    
    // 其实不去重也可以
    a.erase(unique(a.begin(), a.end()), a.end());
    n = a.size();
    if(n <= 3) return void(cout << 0 << endl);
    
    int l = 0, r = 2e8;
    // 只要 r * 2 * 3 > 1e9 就行
    while(l < r){
        int mid = (l + r) >> 1;
        int i = 0;
        for(int t = 0; t < 3 && i < n; t++)
            i = upper_bound(a.begin(), a.end(), a[i] + 2 * mid) - a.begin();
        if(i == n) r = mid;
        else l = mid + 1;
    }
    cout << l << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```