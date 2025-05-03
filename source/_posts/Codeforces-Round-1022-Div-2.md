---
title: Codeforces Round 1022 (Div. 2)
date: 2025-05-03 21:08:35
mathjax: true
tags:
  - Codeforces
categories:
  - 比赛
---

## [CF2108A. Permutation Warm-Up](https://codeforces.com/contest/2108/problem/A)(数学)
第一眼没什么思路，尝试先枚举一下：
- `1, 2, 3, 4, ..., n-1, n`, 此时`f(p) = 0`
- `2, 1, 3, 4, ..., n-1, n`, 此时`f(p) = 2`
- `3, 2, 1, 4, ..., n-1, n`, 此时`f(p) = 4`
- `......`
因为是两个数交换，显然交换的操作对$f(p)$的贡献是**偶数**
显然可以知道$f(p)$是偶数
随便guess一下, 不难想到 $f(p) \in [0, \ x]$
所以，$f(p)$ 有 $x / 2 + 1$个
现在，我们只需要求 $f(p)$ 的最大值 $x$ 即可
显然，当`n, n-1, ..., 3, 2, 1`时，$f(p)$ 取到最大值
**不难求得：**
$$
\max f(p) = \frac{n^2-1}{2}\quad(n\text{ 是奇数})
$$
$$
\max f(p) = \frac{n^2}{2}\quad(n\text{ 是偶数})
$$

**所以：**
$$
ans = \frac{n^2-1}{4}+1\quad(n\text{ 是奇数})
$$
$$
ans = \frac{n^2}{4}+1\quad(n\text{ 是偶数})
$$

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
int n;
/*
2 * (i-1, i-3, i-5, ..., 1/0)
(2i - 2) * (i + 1) / 4     (i^2 - 1)/4 + 1     i&1
i^2 / 2                     i^2/4 + 1         
*/

void solve(){
    cin >> n;
    if(n & 1) cout << ((n * n - 1) / 4) + 1 << endl;
    else cout << ((n * n) / 4) + 1 << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF2108B. SUMdamental Decomposition](https://codeforces.com/contest/2108/problem/B)(位运算)(模拟)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
int n, x, cnt;

void solve(){
    cin >> n >> x;
    cnt = __builtin_popcountll(x);
    if(n == 1 && x == 0) cout << -1 << endl;
    else if(n <= cnt) cout << x << endl;
    else{
        n -= cnt;
        if(n & 1){
            if(x <= 1) cout << x + n + 3 << endl;
            else cout << x + n + 1 << endl;
        }else cout << x + n << endl;
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
