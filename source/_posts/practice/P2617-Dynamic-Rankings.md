---
title: P2617 Dynamic Rankings
date: 2025-07-20 12:27:39
tags: [树状数组, 整体二分, 省选/NOI−]
categories: [算法刷题]
mathjax: true
---

**[题目链接](https://www.luogu.com.cn/problem/P2617)**
**[董晓算法讲解 -- b站](https://www.bilibili.com/video/BV1hC411877G?spm_id_from=333.788.videopod.sections&vd_source=f0489718ccab992000c983a006bde4a5)**
**[董晓算法代码 -- 博客园](https://www.cnblogs.com/dx123/p/18090964)**

### 非离散化值域二分 + 树状数组
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
//#define int long long
#define vi vector<int>
#define lowbit(x) ((x) & (-x))

struct BIT{
    int n; vi tr;
    BIT(int _n = 0){init(_n);}
    void init(int _n){
        n = _n;
        tr.assign(n + 1, 0);
    }
    void add(int x, int k){
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += k;
    }
    int sum(int x){
        int ans = 0;
        for(int i = x; i; i -= lowbit(i)) ans += tr[i];
        return ans;
    }
    int sum(int l, int r){
        return sum(r) - sum(l - 1);
    }
    int get(int i){
        return tr[i];
    }
}tr;
struct query{
    int x, y, k, id, op;
    // 查询：[x, y]第k小，id编号，op = 1
    // 修改：x位置，y值，k贡献，op = 0
};
//struct num{int p, x;};
vi ans;
vector<query> q;

void check(int l, int r, vector<query> &q){
    if(q.empty()) return;
    if(l == r){
        for(auto &qu : q) ans[qu.id] = l;
        return;
    }
    int mid = (l + r) >> 1;
    vector<query> q1, q2;
    for(auto &qu : q){
        if(qu.op == 0){ // 修改操作
            if(qu.y <= mid){
                tr.add(qu.x, qu.k);
                q1.push_back(qu);
            }else q2.push_back(qu);
        }else{ // 查询操作 
            int cnt = tr.sum(qu.x, qu.y);
            if(qu.k <= cnt) q1.push_back(qu);
            else{
                qu.k -= cnt;
                q2.push_back(qu);
            }
        }
    }
    for(auto &qu : q1)
        if(qu.op == 0) tr.add(qu.x, -qu.k);
    check(l, mid, q1);
    check(mid + 1, r, q2);
}

void solve(){
    int n, m; cin >> n >> m;
    char op; int x, y, k;
    tr.init(n);
    ans.assign(m + 1, -1);
    vi a(n + 1);
    for(int i = 1; i <= n; i++){
        cin >> a[i];
        q.push_back({i, a[i], 1, 0, 0});
    }
    for(int i = 1; i <= m; i++){
        cin >> op >> x >> y;
        if(op == 'C'){
            q.push_back({x, a[x], -1, 0, 0});
            q.push_back({x, a[x] = y, 1, 0, 0});
        }else{
            cin >> k;
            q.push_back({x, y, k, i, 1});
        }
    }
    check(0, 1e9, q);
    for(int i = 1; i <= m; i++)
        if(~ans[i]) cout << ans[i] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

### 离散化值域二分 + 树状数组
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
//#define int long long
#define vi vector<int>
#define lowbit(x) ((x) & (-x))

struct BIT{
    int n; vi tr;
    BIT(int _n = 0){init(_n);}
    void init(int _n){
        n = _n;
        tr.assign(n + 1, 0);
    }
    void add(int x, int k){
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += k;
    }
    int sum(int x){
        int ans = 0;
        for(int i = x; i; i -= lowbit(i)) ans += tr[i];
        return ans;
    }
    int sum(int l, int r){
        return sum(r) - sum(l - 1);
    }
    int get(int i){
        return tr[i];
    }
}tr;
struct query{
    int x, y, k, id, op;
    // 查询：[x, y]第k小，id编号，op = 1
    // 修改：x位置，y值，k贡献，op = 0
};
vi ans;
vector<query> q;

void binary(int l, int r, vector<query> &q){
    if(q.empty()) return;
    if(l == r){
        for(auto &i : q) ans[i.id] = l;
        return;
    }
    int mid = (l + r) >> 1;
    vector<query> q1, q2;
    for(auto &i : q){
        if(!i.op){ // 修改操作
            if(i.y <= mid){
                tr.add(i.x, i.k);
                q1.push_back(i);
            }else q2.push_back(i);
        }else{ // 查询操作 
            int cnt = tr.sum(i.x, i.y);
            if(i.k <= cnt) q1.push_back(i);
            else{
                i.k -= cnt;
                q2.push_back(i);
            }
        }
    }
    for(auto &i : q1)
        if(!i.op) tr.add(i.x, -i.k);
    binary(l, mid, q1);
    binary(mid + 1, r, q2);
}

void solve(){
    int n, m; cin >> n >> m;
    char op; int x, y, k;
    tr.init(n);
    ans.assign(m + 1, -1);
    vi a(n + 1);
    vi all;
    for(int i = 1; i <= n; i++){
        cin >> a[i];
        q.push_back({i, a[i], 1, 0, 0});
    }
    for(int i = 1; i <= m; i++){
        cin >> op >> x >> y;
        if(op == 'C'){
            q.push_back({x, a[x], -1, 0, 0});
            q.push_back({x, a[x] = y, 1, 0, 0});
        }else{
            cin >> k;
            q.push_back({x, y, k, i, 1});
        }
    }

    // 离散化
    for(auto &i : q) 
        if(!i.op && i.k) all.push_back(i.y);
    sort(all.begin(), all.end());
    all.erase(unique(all.begin(), all.end()), all.end());
    for(auto &i : q)
        if(!i.op)
            i.y = lower_bound(all.begin(), all.end(), i.y) - all.begin() + 1;
    
    binary(1, all.size(), q);

    for(int i = 1; i <= m; i++)
        if(~ans[i]) cout << all[ans[i] - 1] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```