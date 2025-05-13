---
title: ThemeCP Round 1(Level 15)
date: 2025-05-13 14:58:35
tags:
  - ThemeCP
categories:
  - 比赛
---

## T1：[CF1872B. The Corridor or There and Back Again](https://codeforces.com/problemset/problem/1872/B)(900)
```cpp

```

##  T2：[CF1873E. Building an Aquarium](https://codeforces.com/problemset/problem/1873/E)(二分)(1100)
```cpp

```

## T3：[CF1925B. A Balanced Problemset?](https://codeforces.com/problemset/problem/1925/B)(数学)(1200)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

void solve(){
    int sum, n, ans = 1;
    cin >> sum >> n;
    for(int i = 1; i * i <= sum; i++){
        if(sum % i == 0){
            int x = i, y = sum / i;
            if(x * n <= sum) ans = max(ans, x);
            if(y * n <= sum) ans = max(ans, y);
        }
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

## T4：[CF2033E. Sakurako, Kosuke, and the Permutation](https://codeforces.com/problemset/problem/2033/E)(Permutation)(1400)
**我们知道：**$p_i​ = i$ 称为长度为 1 的环，将 $p_{p_i}​​=i$ 称为长度为 2 的环
**题意转化为 将所有环拆成长度为1或者长度为2的环，问最少需要拆几次**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int n; cin >> n;
    vi a(n + 1), bel(n + 1, -1), sz(n + 1, 0);
    for(int i = 1; i <= n; i++) cin >> a[i];
    for(int i = 1; i <= n; i++){
        if(bel[i] != -1) continue;
        for(int j = i; bel[j] == -1; j = a[j]){
            bel[j] = i;
            sz[i]++;
        }
    }
    int ans = 0;
    for(int i = 1; i <= n; i++)
        ans += abs(sz[i] - 1) / 2;
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