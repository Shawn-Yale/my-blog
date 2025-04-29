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

## [CF2104D. Array and GCD](https://codeforces.com/contest/2104/problem/D)(质数筛 + 前缀和)
**n 个数字组成的总和最小的 ideal数组是什么？**
- `2 3 5 7 11 13 17 ...`
**显然, 如果保留 i 个数字, 只要保证最大的前 i 个数字总和 >= 前 i 个质数总和即可**
**求最大的前 i 个数字总和, 可以通过 排序 + 前缀和 预处理**

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 1e7 + 10;
int n;
vi is_prm(N, 1), prms;

void init(){ // 预处理质数的前缀和
    for(int i = 2; i < N; i++) // 埃氏筛筛质数
        if(is_prm[i])
            for(int j = i * i; j < N; j += i)
                is_prm[j] = 0;
    prms.push_back(0);
    for(int i = 2; i < N; i++)
        if(is_prm[i]) prms.push_back(i);
    for(int i = 1; i < prms.size(); i++)
        prms[i] += prms[i - 1];
}

void solve(){
    cin >> n;
    vi a(n + 1, 0);
    for(int i = 1; i <= n; i++) cin >> a[i];
    sort(a.begin() + 1, a.end(), greater<int>());
    for(int i = 1; i <= n; i++) a[i] += a[i - 1];
    for(int i = n; i >= 0; i--)
        if(a[i] >= prms[i]) return void(cout << n - i << endl);
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    init();
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```