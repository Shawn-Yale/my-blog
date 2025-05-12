---
title: AtCoder Beginner Contest 405
date: 2025-05-11 21:19:50
tags:
  - AtCoder
categories:
  - 比赛
---

## [abc405_d. Escape Route](https://atcoder.jp/contests/abc405/tasks/abc405_d)(多源最短路)
把出口当起点，将多个出口同时放入队列中，求最短路 （ 多源最短路）。
因为每次转移都是一个单位，所以直接bfs即可，当然也可以写一个dijkstra（不局限于转移一个单位）。
**因为**是从终点（出口）开始求的，**所以**对应的箭头方向应该是相反的
**bfs：**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define pii pair<int, int>
const int N = 1e3 + 10;
int n, m;
int dx[] = {-1, 0, 1, 0}, dy[] = {0, 1, 0, -1};
char mp[N][N];
char ac[] = {'v','<','^','>'};

void solve(){
    cin >> n >> m;
    queue<pii> q;
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++){
            cin >> mp[i][j];
            if(mp[i][j] == 'E')
                q.emplace(i, j);
        }
    while(!q.empty()){
        auto [x, y] = q.front(); q.pop();
        for(int i = 0; i < 4; i++){
            int nx = x + dx[i], ny = y + dy[i];
            if(nx < 1 || nx > n || ny < 1 || ny > m) continue;
            if(mp[nx][ny] != '.') continue;
            mp[nx][ny] = ac[i];
            q.emplace(nx, ny);
        }
    }
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++)
            cout << mp[i][j];
        cout << endl;    
    }
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
**dijkstra：**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define arr3 array<int, 3>
const int N = 1e3 + 10;
int n, m;
int dx[] = {-1, 0, 1, 0}, dy[] = {0, 1, 0, -1};
char mp[N][N];
char ac[] = {'v','<','^','>'};

void solve(){
    cin >> n >> m;
    priority_queue<arr3, vector<arr3>, greater<arr3> > pq;
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++){
            cin >> mp[i][j];
            if(mp[i][j] == 'E')
                pq.push({0, i, j});
        }
    while(!pq.empty()){
        auto [d, x, y] = pq.top(); pq.pop();
        for(int i = 0; i < 4; i++){
            int nx = x + dx[i], ny = y + dy[i];
            if(nx < 1 || nx > n || ny < 1 || ny > m) continue;
            if(mp[nx][ny] != '.') continue;
            mp[nx][ny] = ac[i];
            pq.push({d + 1, nx, ny});
        }
    }
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++)
            cout << mp[i][j];
        cout << endl;    
    }

}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

## [abc405_e. Fruit Lineup](https://atcoder.jp/contests/abc405/tasks/abc405_e)(组合数学)(乘法逆元)
**[视频讲解](https://www.bilibili.com/video/BV1QiEgzVEnf/?share_source=copy_web&vd_source=2c70ea18309f42b6e2db78b52312b69e&t=2912)**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 4e6 + 10, mod = 998244353;

int n, fact[N] = {1}, inv[N];
// factorial: 阶乘
// inverse

int qpow(int a, int b){
    int ans = 1;
    while(b){
        if(b & 1) ans = ans * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return ans;
}

int binom(int n, int m){
    // binomial coefficient: 二项式系数(组合数)  
    if(n < 0 || m < 0 || n < m) return 0ll;
    if(n == m) return 1ll;
    return fact[n] * inv[m] % mod * inv[n - m] % mod;
}

void solve(){
    //  初始化阶乘和逆元
    for(int i = 1; i < N; i++) fact[i] = fact[i - 1] * i % mod;
    inv[N - 1] = qpow(fact[N - 1], mod - 2);
    for(int i = N - 2; i >= 0; i--)
        inv[i] = inv[i + 1] * (i + 1) % mod;
    
    int a, b, c, d, n; cin >> a >> b >> c >> d;
    n = a + b + c + d;
    int ans = 0;
    for(int i = 0; i <= b; i++)
        (ans += binom(a + i - 1, a - 1) * binom(n - a - i, c) % mod) %= mod;
    cout << ans << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
