---
title: Educational Codeforces Round 178 (Rated for Div. 2)
date: 2025-04-29 14:57:46
tags:
  - Educational Codeforces
categories:
  - 比赛
---
## [CF2104A. Three Decks](https://codeforces.com/contest/2104/problem/A)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
int a, b, c;

void solve(){
    cin >> a >> b >> c;
    cout << ((a + b + c) % 3 != 0 || b * 2 > a + c ? "NO" : "YES") << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF2104B. Move to the End](https://codeforces.com/contest/2104/problem/B)(前缀最大值 + 后缀和)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n;

void solve(){
    cin >> n;
    vi a(n + 1, 0), mx(n + 1, 0), suf(n + 2, 0);
    for(int i = 1; i <= n;  i++) cin >> a[i], mx[i] = max(mx[i - 1], a[i]);
    for(int i = n; i; i--) suf[i] = suf[i + 1] + a[i];
    for(int i = 1; i <= n; i++)
        cout << suf[n - i + 2] + mx[n - i + 1] << " \n"[i == n];
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF2104C. Card Game](https://codeforces.com/contest/2104/problem/C)(博弈论)
**注意到谁第一局赢，接下来就会一直赢**
**可以考虑 Alice 和 Bob 在哪些情况下会获得胜利**
**我的代码是考虑了 Bob 赢的情况**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n;
string s;

void solve(){
    cin >> n >> s;
    vi bob;
    for(int i = 0; i < s.size(); i++)
        if(s[i] == 'B') bob.push_back(i + 1);
    cout << ((bob.size() >= 2 && s[n - 1] == 'B') || (s[0] == 'B' && s[n - 2] == 'B') ? "Bob" : "Alice") << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
