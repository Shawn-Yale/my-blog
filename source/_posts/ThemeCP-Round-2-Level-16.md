---
title: ThemeCP Round 2(Level 16)
date: 2025-05-13 23:26:17
tags:
  - ThemeCP
categories:
  - 比赛
---

## T1：[CF1904A. Forked!](https://codeforces.com/problemset/problem/1904/A)(900)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define pii pair<int, int>

void solve(){
    int a, b, kx, ky, qx, qy;
    cin >> a >> b >> kx >> ky >> qx >> qy;
    set<pii> st;
    int ans = 0;
    int a1, b1;
    for(int i = -1; i <= 1; i += 2){
        for(int j = -1; j <= 1; j += 2){
            int nx = kx + i * a;
            int ny = ky + j * b;
            st.insert({kx + i * a, ky + j * b});
            st.insert({kx + i * b, ky + j * a});
        }
    }

    for(int i = -1; i <= 1; i += 2){
        for(int j = -1; j <= 1; j += 2){
            int nx = qx + i * a;
            int ny = qy + j * b;
            if(st.count({nx, ny}))
                ans++, a1 = nx, b1 = ny;
        }
    }
    for(int i = -1; i <= 1; i += 2){
        for(int j = -1; j <= 1; j += 2){
            int nx = qx + i * b;
            int ny = qy + j * a;
            if(st.count({nx, ny}) && (nx != a1 && ny != b1))
                ans++;
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
## T2：[CF1993B. Parity and Sum](https://codeforces.com/problemset/problem/1993/B)(贪心)(1100)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int inf = 0x3f3f3f3f;

void solve(){
    int n, cnt1 = 0, cnt2 = 0, mx_odd = 0, mx_even = 0, ans = inf;
    cin >> n;
    vi a(n + 1), even;
    for(int i = 1; i <= n; i++){
        cin >> a[i];
        if(a[i] & 1){
            cnt1++;
            mx_odd = max(mx_odd, a[i]);
        }else{
            cnt2++;
            mx_even = max(mx_even, a[i]);
            even.emplace_back(a[i]);
        }
    }
    if(cnt1 == n || cnt2 == n) return void(cout << 0 << endl);
    int cur = mx_odd;
    sort(even.begin(), even.end());
    for(int i = 0; i < even.size(); i++){
        if(cur < even[i]) break;
        cur += even[i];
        if(i == cnt2 - 1)
            return void(cout << cnt2 << endl);
    }
    cout << cnt2 + 1 << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## T3：[CF1864C. Divisor Chain](https://codeforces.com/problemset/problem/1864/C)(二进制)(构造题)(1300)
**记一下这个构造方法**
首先我随便胡诌一个二进制数字，1101101，对于这个二进制数字，我们可以发现我们能够把右边的1一个一个删掉，比如1101101可以被1整除，后面变成1101100，然后发现可以被100整除，然后变为1101000，以此类推，可以变为1000000，然后对于这个数字实际上我们可以考虑每次减半，比如1000000可以被100000整除，那么他会变成100000，以此类推，我们发现一个值最多选两次，很好地解决了这个问题。---- 冬权九暮
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define lowbit(x) (x & -x)

void solve(){
    int x; cin >> x;
    vi a;
    a.emplace_back(x);
    while(x != lowbit(x)){
        x -= lowbit(x);
        a.emplace_back(x);
    }
    while(x != 1){
        x /= 2;
        a.emplace_back(x);
    }
    int n = a.size();
    cout << n << endl;
    for(int i = 0; i < n; i++)
        cout << a[i] << " \n"[i == n - 1];
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## T4：[CF1974D. Ingenuity-2](https://codeforces.com/problemset/problem/1974/D)(模拟)(构造题)(1400)
**这个写法值得复习**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

void solve(){
    int n, ok = 0;
    string s, ans;
    int w[128];
    w['N'] = w['S'] = 0;
    w['W'] = w['E'] = 1;
    cin >> n >> s;
    for(int i = 0; i < n; i++){
        ans += (w[s[i]] == 0 ? 'R' : 'H');
        if(ans[i] == 'R') ok |= 1;
        if(ans[i] == 'H') ok |= 2;
        w[s[i]] ^= 1;
    }
    if(ok == 3 && w['N'] == w['S'] && w['W'] == w['E']) cout << ans << endl;
    else cout << "NO" << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
