---
title: P5094【USACO04OPEN】MooFest G 加强版
date: 2025-07-20 14:09:24
tags: [树状数组, 普及+/提高]
categories: [算法刷题]
mathjax: true
---

**[题目链接](https://www.luogu.com.cn/problem/P5094)**
- **`tr1`（用于记录奶牛数量）**：
    - `tr1` 主要用来存储每个坐标位置 `x` 上有多少只奶牛（数量）。它是一个标准的 **二叉索引树**（或树状数组），用于对奶牛的数量进行累积和查询。
    - `tr1.add(x, 1)` 操作会将坐标为 `x` 的奶牛的数量增加 1。
    - `tr1.sum(x)` 用来查询坐标小于等于 `x` 的奶牛数量的累积和。具体来说，`sum(x)` 返回的是所有坐标 `<= x` 的奶牛的数量。
- **`tr2`（用于记录奶牛坐标的累积和）**：
    - `tr2` 用来存储奶牛坐标的累积和。它用于记录到当前坐标 `x` 为止的所有奶牛坐标的总和。
    - `tr2.add(x, x)` 操作会将坐标为 `x` 的奶牛的坐标加入到累积和中。
    - `tr2.sum(x)` 用来查询坐标小于等于 `x` 的所有奶牛的坐标和，`sum(x)` 返回的是坐标 `<= x` 的奶牛坐标的累积和。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
#define lowbit(x) ((x) & (-x))
const int N = 5e4 + 10;

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
}tr1, tr2;

void solve(){
    int n; cin >> n;
    tr1.init(N), tr2.init(N);
    vector<pii> all(n);
    for(auto &i : all) cin >> i.first >> i.second;
    sort(all.begin(), all.end());
    int ans = 0, cnt_all = 0, sum_all = 0;
    for(auto &[v, x] : all){
        int cnt_l = tr1.sum(x - 1), cnt_r = cnt_all - cnt_l;
        int sum_l = tr2.sum(x - 1), sum_r = sum_all - sum_l;
        ans += (cnt_l * x - sum_l + sum_r - cnt_r * x) * v;
        tr1.add(x, 1), tr2.add(x, x);
        cnt_all++, sum_all += x;
    }
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