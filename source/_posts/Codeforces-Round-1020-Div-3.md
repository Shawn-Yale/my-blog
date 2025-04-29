---
title: Codeforces Round 1020 (Div. 3)
date: 2025-04-28 19:03:58
tags:
  - Codeforces
categories:
  - 比赛
---

## [CF2106D. Flower Boy](https://codeforces.com/problemset/problem/2106/D)(前后缀和 + 枚举隔板)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 3e5 + 10, M = 4e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
const int INF = 0x3f3f3f3f3f3f3f3f, MOD = 998244353;
int n, m, ans;

void solve(){
    cin >> n >> m;
    ans = inf;
    vi a(n + 1), b(m + 2), pre(n + 1, 0), suf(n + 2, 0);
    for(int i = 1; i <= n; i++) cin >> a[i];
    for(int i = 1; i <= m; i++) cin >> b[i];
    for(int i = 1; i <= n; i++)
        pre[i] = pre[i - 1] + (a[i] >= b[pre[i - 1] + 1] && pre[i - 1] < m);
    for(int i = n; i; i--)
        suf[i] = suf[i + 1] + (a[i] >= b[m - suf[i + 1]] && suf[i + 1] < m);
    if(pre[n] == m) return void(cout << 0 << endl);
    for(int i = 0; i <= n; i++)
        if(pre[i] + suf[i + 1] >= m - 1)
            ans = min(ans, b[pre[i] + 1]);
    cout << (ans == inf ? -1 : ans) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF2106E. Wolf](https://codeforces.com/problemset/problem/2106/E)(模拟二分)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 2e5 + 10, M = 4e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
const int INF = 0x3f3f3f3f3f3f3f3f, MOD = 998244353;
int n, q, a[N], p[N], x;
int l, r, small, big, tot_small, tot_big;
// big: 遇到的大于X的，需要改成小于X的数字数量
// small: 遇到的小于X的，需要改成大于X的数字数量
// tot_big: 需要遇到的大于X的数字数量
// tot_small: 需要遇到的小于X的数字数量

void solve(){
    cin >> n >> q;
    for(int i = 1; i <= n; i++) cin >> a[i], p[a[i]] = i;
    while(q--){
        cin >> l >> r >> x;
        if(p[x] < l || p[x] > r){
            cout << -1 << " \n"[q == 0];
            continue;
        }
        small = big = tot_small = tot_big = 0;   
        while(l <= r){
            int mid = (l + r) >> 1;
            if(a[mid] == x) break;
            else if(a[mid] < x){ // 即将前往右半边
                if(p[x] > mid) l = mid + 1, tot_small++;
                // 目标确实在右边
                else r = mid - 1, big++, tot_big++;
            }else{ // 即将前往左半边
                if(p[x] < mid) r = mid - 1, tot_big++;
                // 目标确实在左边
                else l = mid + 1, small++, tot_small++;
            }
        }
        cout << (tot_small >= x || tot_big > n - x ? -1 : max(small, big) * 2) << " \n"[q == 0];
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
## [CF2106F. Goblin](https://codeforces.com/contest/2106/problem/F)(DP)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
int n, ans;
string s;

void solve(){
    ans = 0;
    cin >> n >> s;
    vii dp(n + 1, vi(3, 0));
    // dp[i][0]: 到第 i 列为止, 与 (i, i) 上方联通的方块数量
    // dp[i][1]: 到第 i 列为止, 与 (i, i) 下方联通的方块数量
    // dp[i][2]: 到第 i 列为止, 与 (i, i) 联通的方块数量
    for(int i = 1; i <= n; i++){
        if(s[i - 1] == '0'){
            dp[i][0] = dp[i - 1][0] + dp[i - 1][2] + i - 1;
            dp[i][1] = dp[i - 1][1] + n - i;
        }else dp[i][2] = dp[i - 1][1] + 1;
        ans = max({ans, dp[i][0], dp[i][1], dp[i][2]});
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
