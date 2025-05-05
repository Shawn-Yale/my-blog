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
## [CF2108C. Neo's Escape](https://codeforces.com/contest/2108/problem/C)
权重相同的连续按钮不会影响结果，保留其中一个按钮。
在得到的数组中，我们会发现**峰值（局部最大值 -- 严格大于其两个相邻元素的元素）数量就是答案**，因为
- 每个峰值与其他峰值之间都隔着较小的元素。因此，到达峰值的唯一方法就是在那里创建一个克隆。
- 如果一个按钮被访问过，我们就可以返回它。
- 除了山峰以外的任何元素都可以从较大的相邻元素到达，因为我们已经访问过它。因此，没有必要在所有其他元素中创建克隆体。
复杂度： $\text{O}(n)$ 。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int inf = 0x3f3f3f3f;
int n, x, cnt;

void solve(){
    vi a;
    cnt = 0;
    cin >> n;
    a.emplace_back(-inf);
    for(int i = 1; i <= n; i++){
        cin >> x;
        if(x != a.back()) a.emplace_back(x);
    }
    a.emplace_back(-inf);
    for(int i = 1; i < a.size() - 1; i++)
        if(a[i] > a[i - 1] && a[i] > a[i + 1]) cnt++;
    cout << cnt << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF2108D. Needle in a Numstack](https://codeforces.com/contest/2108/problem/D)(交互题)(二分)(太阴间了😄)
对于数组$A$, 数组$C$(排除数组$A$和$B$都符合的位置)的状态为: 1111...1111000...000
可以二分出 1 的最右端 `l_max`
**同理**: 对于数组$B$, 数组$C$(排除数组$A$和$B$都符合的位置)的状态为: 0000...0000111...111
可以二分出 1 的最左端 `r_min`
如果 `l_max + 1 == r_min` 说明数组$A$和$B$的分界处唯一，为`l_max`和`r_min`
否则，说明分界点不唯一，输出 `-1`
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define vi vector<int>
int n, k, t, l, r, l_max, r_min;

int ask(int x){
    cout << "? " << x + 1 << endl;
    cout.flush();
    cin >> t;
    return t;
};

void solve(){
    cin >> n >> k;
    vi a(k), b(k), vec;
    for(int i = 0; i < k; i++) a[i] = ask(i);
    for(int i = 0; i < k; i++) b[i] = ask(n - i - 1);
    vec.emplace_back(k);
    for(int i = k; i < n - k; i++)
        if(a[i % k] != b[(n - i - 1) % k])
        // 把两边都符合的点删去
            vec.emplace_back(i + 1);
    vec.emplace_back(n - k + 1);
    // 加入 k, n-k+1两个点，确保存在二分有解

    l = 0, r = vec.size() - 2;
    while(l < r){
        int mid = (l + r + 1) >> 1;
        if(ask(vec[mid] - 1) == a[(vec[mid] - 1) % k]) l = mid;
        else r = mid - 1;
    }
    l_max = vec[l];
    
    l = 1, r = vec.size() - 1;
    while(l < r){
        int mid = (l + r) >> 1;
        if(ask(vec[mid] - 1) == b[(n - vec[mid]) % k]) r = mid;
        else l = mid + 1;
    }
    r_min = vec[l];

    if(l_max + 1 == r_min) cout << "! " << l_max << ' ' << n - l_max << endl;
    else cout << "! -1" << endl;
    cout.flush();
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```