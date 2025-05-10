---
title: Educational Codeforces Round 177 (Rated for Div. 2)
date: 2025-05-09 15:13:37
tags:
  - Educational Codeforces
categories:
  - 比赛
---

## [CF2086A. Cloudberry Jam](https://codeforces.com/contest/2086/problem/A)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
int n;

void solve(){
    cin >> n;
    cout << 2 * n << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF2086B. Large Array and Segments](https://codeforces.com/contest/2086/problem/B)(贪心)(后缀和)(1100)
对于某一点`l`，只需要`l`的右侧存在一个`r`满足 $\displaystyle \biggl(\sum_{i = l}^{r}b[i] \biggr) > x$，又因为所有数都是正数，
因此我们可以贪心地让`r`固定在数组的最右端。
因为`数组b`的大小最大为 1e10，显然不可能将它初始化出来

我们想从`数组b`的最右端开始，让其跨过`t`个完整的`数组a`(令$\sum a[i] = S$)，使得：$t\cdot S < x \le (t+1)\cdot S.$
这样才保证：跨过 `t` 个完整块之后仍然凑不够 `x`，而跨过 `t+1` 个块就一定可以。
为此，我们可以取：$\displaystyle t = \left\lfloor \frac{x-1}{S} \right\rfloor$
**证明：**
- $t⋅S<x \ (两边同时减去1)\quad\Longrightarrow\quad t\cdot S \le x - 1.$
	（因为 $t·S$ 和 $x$ 都是整数， $t·S < x$ 等价于 $t·S \le x−1$）
 - $x \le (t+1)\cdot S(两边同时减去1)\quad\Longrightarrow\quad x - 1 < (t+1)\cdot S$
	 （同理，整数上的 $x \le (t+1)S$ 等价于 $x−1 < (t+1)S$）
- 将这两条合并：$t\cdot S \le x - 1 < (t+1)\cdot S.$
	两边同除 $S$，$\displaystyle t \le \frac{x - 1}{S} < (t + 1)$
- **因此**，正好说 _t_ 是把 $\displaystyle \frac{x - 1}{S}$ 向下取整得到的数：$\displaystyle \left\lfloor \frac{x-1}{S} \right\rfloor$

接下来，我们只需要在`数组a`中确定最小的后缀起点即可，**可以直接线性扫描也可以二分**，这部分的时间复杂度对整体时间复杂度的影响不是很大，因为 $n \le 10^5$ 且 $\sum n \le 10^6$
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n, k, x;

void solve(){
    cin >> n >> k >> x;
    vi a(n), suf(n + 1, 0);
    for(int &num : a) cin >> num;
    // 构造后缀和 suf[i] = a[i] + a[i+1] + ... + a[n-1]
    for(int i = n - 1; i >= 0; i--)
        suf[i] = suf[i + 1] + a[i];
    // 两种情况下的特判
    if(suf[0] * k < x) return void(cout << 0 << endl);
    if(a[n - 1] >= x) return void(cout << n * k << endl);

    int t = (x - 1) / suf[0];
    //  suf[0] 是 a[i]整体的和
    int cur = t * suf[0];
    int pos = n - 1;
    while(cur + suf[pos] < x) pos--;
    cout << (k - t - 1) * n + pos + 1 << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF2086C. Disappearing Permutation](https://codeforces.com/contest/2086/problem/C)(图论)(并查集)(permutation)(1300)
**Permutation(排列、置换)**。对于 $n$ 的全排列 $P_n$ ，可以看成特殊的 $Functional \ Graph$，其中只有环。
对于一个排列中的，长度为 $n$ 的环，需要 $n-1$ 次可以把环排好序。每次交换相当于把环变成两个更小的环，直至变成大小为 1 的自环。
对于上方概念不理解的话可以看这两个视频的前半部分理解一下：
1. [Functional graph](https://www.bilibili.com/video/BV1mC411b7sU/?vd_source=f0489718ccab992000c983a006bde4a5)
2. [排列构成环](https://www.bilibili.com/video/BV1w2421N75o/?vd_source=f0489718ccab992000c983a006bde4a5)

对于本题，每次查询都会把一个点变成0，如果如果这个点的下标为 **i**，题目规定只能将这个点 `a[i] = i`，也就是将这个点强制变成自环，即出边指向自己，但是这个点的入边没有改变，为此我们可以知道，只有将这个点原来所在的整个环上的所有点都变成自环，才能满足题意。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n;

void solve(){
    cin >> n;
    vi a(n + 1);
    for(int i = 1; i <= n; i++) cin >> a[i];
    vi bel(n + 1, -1), sz(n + 1, 0);
    // bel: belong, sz: size
    // bel[i] = i 所属的环代表（初始化为 -1 表示未访问）
    // siz[i] = 以 i 为代表的环的长度
    for(int i = 1; i <= n; i++){
        if(bel[i] != -1) continue;
        for(int j = i; bel[j] == -1; j = a[j]){
            bel[j] = i; // 标记它属于以 i 为代表的环
            sz[i]++; // 环大小 +1
        }
    }
    int ans = 0;
    for(int i = 1; i <= n; i++){
        int pos; cin >> pos;
        ans += sz[bel[pos]]; // 找到这个位置所在的环以及这个环的大小
        sz[bel[pos]] = 0; // 之后同一环不再贡献
        cout << ans << " \n"[i == n];
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