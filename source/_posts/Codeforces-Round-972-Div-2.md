---
title: Codeforces Round 972 (Div.2)
date: 2025-04-28 22:12:43
tags:
  - Codeforces
categories:
  - 比赛
---

## [CF2005B2. The Strict Teacher (Hard Version)](https://codeforces.com/contest/2005/problem/B2)(二分)(1200)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n, m, q, x;

void solve(){
    cin >> n >> m >> q;
    vi a(m + 1, 0);
    for(int i = 1; i <= m; i++) cin >> a[i];
    sort(a.begin() + 1, a.end());
    while(q--){
        cin >> x;
        int pos = upper_bound(a.begin() + 1, a.end(), x) - a.begin() - 1;
        if(pos == m) cout << n - a[m] << endl;
        else if(pos == 0) cout << a[1] - 1 << endl;
        else cout << (a[pos + 1] - a[pos]) / 2 << endl;
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
## [CF2005C. Lazy Narek](https://codeforces.com/contest/2005/problem/C)(DP)(1800)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
#define pii pair<int, int>
int n, m, ans;
// 每个narek字母为 -1 分
// 每个组合为 10 分
// 计算每个字符串 如果从 n,a,r,e,k 开始找能获得的得分
string t = "narek";

// 返回: {得分，现在需要哪个字符}
pii get_score(string s, int pos){
    int score = 0;
    for(int i = 0; i < m; i++){
        if(s[i] == t[pos]){
            pos++;
            if(pos == 5) score += 10, pos = 0;
        }
        if(t.find(s[i]) != string::npos) score--;
    }
    return {score, pos};
}

void solve(){
    cin >> n >> m;
    vii dp(n + 1, vi(5, -inf));
    dp[0][0] = 0, ans = 0;
    for(int i = 1; i <= n; i++){
        string s; cin >> s;
        for(int j = 0; j < 5; j++){
            dp[i][j] = max(dp[i][j], dp[i - 1][j]);
            auto [score, nxt] = get_score(s, j);
            dp[i][nxt] = max(dp[i][nxt], dp[i - 1][j] + score); 
        }
    }
    for(int i = 0; i < 5; i++) ans = max(ans, dp[n][i]);
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
