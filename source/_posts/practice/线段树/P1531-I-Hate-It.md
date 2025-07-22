---
title: P1531 I Hate It
date: 2025-07-22 21:57:00
tags: [线段树, 普及+/提高]
categories: [算法刷题]
mathjax: true
---

**[题目链接](https://www.luogu.com.cn/problem/P1531)**

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 2e5 + 10;

int a[N];
struct Node{
    int l, r, mx;
}tr[N << 2];

void pushup(int u){
    tr[u].mx = max(tr[u << 1].mx, tr[u << 1 | 1].mx);
}
void build(int u, int l, int r){
    tr[u] = {l, r};
    if(l == r) return void(tr[u].mx = a[l]);
    int mid = l + r >> 1;
    build(u << 1, l, mid), build(u << 1 | 1, mid + 1, r);
    pushup(u);
}
void modify(int u, int x, int k){
    if(tr[u].l == x && tr[u].r == x) tr[u].mx = k;
    else{
        int mid = tr[u].l + tr[u].r >> 1;
        if(x <= mid) modify(u << 1, x, k);
        else modify(u << 1 | 1, x, k);
        pushup(u);
    }
}
int query(int u, int l, int r){
    if(l <= tr[u].l && tr[u].r <= r) return tr[u].mx;
    int mid = tr[u].l + tr[u].r >> 1;
    int mx = 0;
    if(l <= mid) mx = query(u << 1, l, r);
    if(r > mid) mx = max(mx, query(u << 1 | 1, l, r));
    return mx;
}

void solve(){
    int n, m; cin >> n >> m;
    for(int i = 1; i <= n; i++) cin >> a[i];
    build(1, 1, n);
    char op; int x, y;
    for(int i = 1; i <= m; i++){
        cin >> op >> x >> y;
        if(op == 'Q') cout << query(1, x, y) << endl;
        else if(y > query(1, x, x)) modify(1, x, y);
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