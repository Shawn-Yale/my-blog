---
layout: posy
title: ThemeCP Round 5(Level 17)
date: 2025-05-15 16:02:42
tags:
  - ThemeCP
categories:
  - 比赛
---

## T1：[CF1848A. Vika and Her Friends](https://codeforces.com/problemset/problem/1848/A)(900)
只要 Vika 和朋友之间的距离存在偶数，就会被逮捕
否则就可以永远逃离
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

void solve(){
    int n, m, k, a, b;
    cin >> n >> m >> k >> a >> b;
    int ok = 0;
    for(int i = 1; i <= k; i++){
        int x, y; cin >> x >> y;
        if(abs(x + y - a - b) % 2 == 0)
            ok = 1;
    }
    cout << (ok ? "NO" : "YES") << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## T2：[CF1741C. Minimize the Thickness](https://codeforces.com/problemset/problem/1741/C)(前缀和)(1100)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int n; cin >> n;
    vi a(n + 1, 0), pre(n + 1, 0);
    for(int i = 1; i <= n; i++) cin >> a[i], pre[i] = pre[i - 1] + a[i];
    int ans = n; // 最差情况是整个序列为一段
    for(int i = 1; i <= n; i++){
        int sum = pre[i]; // 枚举第一段区间和
        int mx = i; // 所有序列的最长长度
        int id = i; // 前一个区间尾部下标
        for(int j = i + 1; j <= n; j++){
            if(pre[j] - pre[id] == sum){
                mx = max(mx, j - id);
                id = j;
            }
        }
        if(id == n) ans = min(ans, mx);
    }
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
## T3：[CF1736C1. Good Subarrays (Easy Version)](https://codeforces.com/problemset/problem/1736/C1)(贪心)(1300)
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
    int ans = 0, pre = 0;
    for(int i = 1; i <= n; i++){
        ans += min(pre + 1, a[i]);
        pre = min(pre + 1, a[i]);
    }
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
## T4：[CF1817A. Almost Increasing Subsequence](https://codeforces.com/problemset/problem/1817/A)(前缀和)(贪心)(1500)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int n, q; cin >> n >> q;
    vi a(n + 1, 0);
    for(int i = 1; i <= n; i++) cin >> a[i];
    vi bad(n + 1, 0);
    // bad[i]: 以 i+2 结尾的连续三个元素，有多少组 x >= y >= z
    // 每组x, y, z都必须至少删掉一个，因此答案只要减去区间中坏子段数即可
    for(int i = 1; i <= n - 2; i++){
        if(a[i] >= a[i + 1] && a[i + 1] >= a[i + 2])
            bad[i] = 1;
    }
    for(int i = 1; i <= n - 2; i++)
        bad[i] += bad[i - 1];
    while(q--){
        int l, r; cin >> l >> r;
        int len = r - l + 1;
        if(len < 3) cout << len << endl;
        else cout << len - (bad[r - 2] - bad[l - 1]) << endl;
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