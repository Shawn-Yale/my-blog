---
title: P3531【POI 2012】LIT-Letters
date: 2025-07-21 11:00:58
tags: [树状数组, 提高+/省选−]
categories: [算法刷题]
mathjax: true
---

**[题目链接](https://www.luogu.com.cn/problem/P3531)**

交换相邻两个数就决定了是求**逆序对**

对于A中的一个字符在B中的位置，我们可以发现同一个字符肯定是按从前往后的顺序出现在B中的。

比如A串ABABA，B串BAABA

我们肯定是把A中的第一个A放在B中的第一个A上，依次类推。

因为如果第一个A放在了后面，那么就有可能产生更多逆序对（因为你把大的数放在了前面）。由此得证。

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
#define pii pair<int, int>
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

void solve(){
    int n; string s1, s2;
    cin >> n >> s1 >> s2;
    vii b(26); // 记录b中每个字符按顺序出现的下标
    for(int i = 0; i < n; i++)
        b[s1[i] - 'A'].push_back(i + 1); // (1...based)
    vi cnt(26), a(n + 1);
    // cnt记录每个字母分别出现到第几个了
    // a记录A字符串每个下标要转移到的位置
    for(int i = 1; i <= n; i++)
        a[i] = b[s2[i - 1] - 'A'][cnt[s2[i - 1] - 'A']++];

    int ans = 0;
    tr.init(n);
    for(int i = n; i; i--){
        ans += tr.sum(a[i] - 1);
        tr.add(a[i], 1);
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