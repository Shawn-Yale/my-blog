---
title: Codeforces Round 1023 (Div. 2)
date: 2025-05-06 11:05:15
tags:
  - Codeforces
categories:
  - 比赛
---

## [CF2107A. LRC and VIP](https://codeforces.com/contest/2107/problem/A)(贪心)
如果 `数组a` 的 `gcd(a) == x`
对于数组中的元素，如果存在`a[i] != x`，那么肯定有 `a[i] == kx, k != 1`
显然，如果`数组a` 删去这个 `a[i]`，必定存在`gcd(a) != kx`
**证明**：
如果`数组a` 删去这个 `a[i]`，`gcd(a) == kx`
那么当`数组a` 重新加上这个 `a[i]`之后，一定有`gcd(a) == kx`与`gcd(a) == x`矛盾
**综上**：只需要找到一个`a[i] != x`，将其单独分成一组即可
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n;

void solve(){
    cin >> n;
    vi a(n);
    for(int &x : a) cin >> x;
    int all_gcd = a[0];
    for(int &x : a) all_gcd = gcd(all_gcd, x);
    for(int i = 0; i < n; i++){
        if(a[i] != all_gcd){
            cout << "Yes" << endl;
            for(int j = 0; j < n; j++)
                cout << "21"[j == i] << " \n"[j == n - 1];
            return;
        }
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
**法二**：
找到`数组a`的最大值`mx`和最小值`mn`
如果`mx ==`mn`，那么说明整个数组都是同一个数，与题意不符
如果`mx != mn` ，只需要将最大的值放在一组，其他的值放在另一组即可
**证明**：
如果 `mx !=mn`，显然有 `mx < mn`
对于删去了 `mx` 的 `数组a`，`gcd(a) <= mn < mx`
那么拆分出来的两个数组的 `gcd` 必定不相等
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n;

void solve(){
    cin >> n;
    vi a(n);
    for(int &x : a) cin >> x;
    // ranges::max(a) 为 c++20 版本的语法
    // 可用for循环求最大值代替
    int mx = ranges::max(a), mn = ranges::min(a);
    if(mn == mx) cout << "No" << endl;
    else{
        cout << "Yes" << endl;
        for(int i = 0; i < n; i++)
            cout << "21"[a[i] == mx] << " \n"[i == n - 1];
    }
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF2107B. Apples in Boxes](https://codeforces.com/contest/2107/problem/B)(博弈论)(贪心)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n, k;

void solve(){
    cin >> n >> k;
    vi a(n);
    for(int &x : a) cin >> x;
    sort(a.begin(), a.end());
    // 判断刚开始是不是必输的局面
    if((a[n - 1] - a[0] > k + 1) || (a[n - 2] - a[0] > k))
        return void(cout << "Jerry" << endl);
    // 谁最后拿完，谁就赢
    // accumulate 是 c++98 的语法
    // 0ll 是 整数字面量，从 c++11开始支持
    cout << (accumulate(a.begin(), a.end(), 0ll) & 1 ? "Tom" : "Jerry") << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```