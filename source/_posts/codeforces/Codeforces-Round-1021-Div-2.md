---
title: Codeforces Round 1021 (Div. 2)
date: 2025-05-15 22:01:05
tags:
  - Codeforces
categories:
  - 比赛
---

## [CF2098A. Vadim's Collection](https://codeforces.com/contest/2098/problem/A)(800)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

void solve(){
    string s; cin >> s;
    sort(s.begin(), s.end());
    for(int i = 9; i >= 0; i--){
        int pos = lower_bound(s.begin(), s.end(), '0' + i) - s.begin();
        cout << s[pos];
        s.erase(s.begin() + pos);
    }
    cout << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF2098B. Sasha and the Apartment Purchase](https://codeforces.com/contest/2098/problem/B)(货物集中问题)(1400)
- 关闭右边的 $k$ 家酒吧，求出所有可取区间的最左端：$\displaystyle \left \lfloor \frac{1 + (n - k)}{2} \right \rfloor =\frac{n - k + 1}{2}$
- 关闭左边的 $k$ 家酒吧，求出所有可取区间的最右端：$\displaystyle \left \lceil \frac{(k + 1) + n}{2} \right \rceil = \frac{n + k + 2}{2} = \frac{n + k}{2} + 1$
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int n, k; cin >> n >> k;
    vi a(n + 1, 0);
    for(int i = 1; i <= n; i++) cin >> a[i];
    sort(a.begin() + 1, a.end());
    int l = (n - k + 1) / 2, r = (n + k) / 2 + 1;
    cout << a[r] - a[l] + 1 << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF2098C. Sports Betting](https://codeforces.com/contest/2098/problem/C)(贪心)(1400)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

void solve(){
    int n; cin >> n;
    map<int, int> mp, ok;
    for(int i = 1; i <= n; i++){
        int x; cin >> x;
        mp[x]++;
    }
    for(auto [x, y] : mp){
        if(y >= 4) return void(cout << "Yes" << endl);
        if(y >= 2) ok[x + 2] = 1;
        if(ok[x + 1] && ok[x + 2]) return void(cout << "Yes" << endl);
        if(ok[x + 1]) ok[x + 2] = 1;
    }
    cout << "No" << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
