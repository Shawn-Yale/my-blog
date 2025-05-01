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
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 1e5 + 10;
int n, a[N], dp[N];
// dp[i]: 第 i 朵花的前 dp[i] 秒不变，之后开始每秒 -1;

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i];
    dp[n] = 0;
    for(int i = n - 1; i; i--)
        dp[i] = max(a[i + 1] - a[i] + 1 + dp[i + 1], 0ll);
    // 显然只有当第 i+1 朵花变为 0之后, 第 i 朵花才会变为 0;
    // 所以第一朵花一定是最后变为 0 的, 只需要输出 a[1]+dp[1] 即可;
    cout << a[1] + dp[1] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
