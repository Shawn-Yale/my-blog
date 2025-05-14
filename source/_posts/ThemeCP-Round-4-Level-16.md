---
title: ThemeCP Round 4(Level 16)
date: 2025-05-14 22:42:49
tags:
  - ThemeCP
categories:
  - 比赛
---



## T1：[CF2060C. Game of Mathletes](https://codeforces.com/problemset/problem/2060/C)(博弈论)(900)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int n, k; cin >> n >> k;
    vi a(n + 1, 0);
    for(int i = 1; i <= n; i++){
        int x; cin >> x;
        a[x]++;
    }
    int ans = 0;
    if(k % 2 == 0) ans += a[k / 2] / 2;
    for(int i = 1; i < (k + 1) / 2 && i <= n; i++)
        if(k - i >= 1 && k - i <= n)
            ans += min(a[i], a[k - i]);
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
## T2：[CF1933C. Turtle Fingers: Count the Values of k](https://codeforces.com/problemset/problem/1933/C)(枚举)(1100)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int a, b, l; cin >> a >> b >> l;
    map<int, int> mp;
    int cur = 1;
    for(int i = 0; i <= 20; i++){
        if(i >= 1) cur *= a;
        if(l % cur != 0) break;
        int tmp = cur;
        for(int j = 0; j <= 20; j++){
            if(j >= 1) tmp *= b;
            if(l % tmp != 0) break;
            mp[l / tmp]++;
        }
    }
    cout << mp.size() << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## T3：[CF1717C. Madoka and Formal Statement](https://codeforces.com/problemset/problem/1717/C)(贪心)(1300)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>

void solve(){
    int n; cin >> n;
    vi a(n + 1), b(n + 1);
    for(int i = 0; i < n; i++) cin >> a[i];
    for(int i = 0; i < n; i++) cin >> b[i];
    a[n] = a[0], b[n] = b[0];
    for(int i = 0; i < n; i++)
        if(a[i] > b[i] || (b[i] > b[i + 1] + 1 && a[i] != b[i]))
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
## T4：[CF2038L. Bridge Renovation](https://codeforces.com/problemset/problem/2038/L)(贪心)(1400)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

void solve(){
    int n; cin >> n;
    int tot = 0;
    tot += (n / 3) * 2;
    if(n % 3 == 1) tot++;
    else if(n % 3 == 2){
        tot += 2;
        n--;
    }
    tot += (n + 1) / 2;
    cout << tot << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```