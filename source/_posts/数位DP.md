---
title: 数位DP
date: 2025-04-21 19:20:01
tags: [DP, 数位DP]
categories: [算法笔记]
mathjax: true
---

#### [abc387_c. Snake Numbers](https://atcoder.jp/contests/abc387/tasks/abc387_c)(数位DP)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define li __int128
#define vi vector<int>
#define pii pair<int, int>
#define arr3 array<int, 3>
#define lowbit(x) (x & -x)
const int N = 20 + 10, M = 1e2 + 10, inf = 0x3f3f3f3f, Inf = INT_MAX, mod = 1e9 + 7;
int l, r, p, ans, last;

int qpow(int a, int b){
    int ans = 1;
    while(b){
        if(b & 1) ans *= a;
        a *= a;
        b >>= 1;
    }
    return ans;
}

int sum(int n){
    if(n < 10) return 0;
    vi nums;
    while(n) nums.push_back(n % 10), n /= 10;
    ans = 0, last = nums[nums.size() - 1]; // last 记录最高位
    // 情况1: 位数小于 n 时
    for(int i = 2; i < nums.size(); i++)
        for(int j = 1; j <= 9; j++)
            ans += qpow(j, i - 1);
    // 情况2: 位数等于 n 时
    // 子情况1:最高位小于 n 的最高位
    for(int i = 1; i < last; i++) ans += qpow(i, nums.size() - 1);
    // 子情况2: 最高位等于 n 的最高位
    for(int i = nums.size() - 2; i >= 0; i--){
        int x = nums[i];
        // 0, 1, 2, ... , min(x, last) - 1
        ans += min(x, last) * qpow(last, i);
        if(x >= last) break;
        if(!i) ans++;
    }
    return ans;
}

void solve(){
    cin >> l >> r; 
    cout << sum(r) - sum(l - 1) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

#### [CF914C. Travelling Salesman and Special Numbers](https://codeforces.com/problemset/problem/914/C)(数位DP)(1800)
**提示 1：** 一次操作后，数字会不超过多少？接下来进行的操作次数是可以预处理的。
**提示 2：** 我们只需要知道一次操作后，这个数变成了多少，就可以知道这个数多少次操作后会变成 $1$ 。
**提示 3：** 我们统计一次操作后，变成每个数的分别有多少个。
首先，由于 $n\lt 2^{1000}$ ，因此二进制表示中最多有 $1000$ 个 $1$ ，一次操作后必然变成不超过 $1000$ 的数。
而我们只需要预处理 $1 - 1000$ 的数多少次操作后会变成 $1$ ，又由于我们知道原数有多少个 $1$ ，即经过一次操作后会变成的数，于是可以知道原数经过多少次操作后会变成 $1$ 。
因此，我们先将原有的 $x$ 经过一次操作，接下来找所有恰经过 $k-1$ 次操作可以变成 $1$ 的数即可。
因此我们对所有 $[1,n]$ 的数，统计其经过一次操作后，变为 $c$ 的元素的数量即可。
也就是说，我们要统计 $[1,n]$ 中，二进制表示里出现 $1$ 次数为 $c$ 的数的数量，这是个典型的[数位 DP](https://oi-wiki.org/dp/number/) 问题。
具体而言，我们从高到低枚举数位，枚举新的一位是 $0$ 还是 $1$ 。如果此前的数字已经严格更小，那么下一位可以随意填写 $0 / 1$ ，否则只能选择不超过当前位数码的数字。
总时间复杂度为 $\mathcal{O}(\log^2n)$。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 1e3 + 10, M = 6e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7, MOD = 998244353;
int len, k, ans, dp[N][N], f[N];
string s;
// dp[i][j]: (组合数) i 个数里选 j 个
// f[i]: 表示 i 变成 1 需要几次操作
void init(){
    for(int i = 0; i < N; i++)
        for(int j = 0; j <= i; j++)
            if(!j) dp[i][j] = 1;
            else dp[i][j] = (dp[i - 1][j] + dp[i - 1][j - 1]) % mod;
}

void solve(){
    init();
    cin >> s >> k;
    len = s.size();
    s = " " + s;
    for(int i = 2; i <= 1000; i++) f[i] = f[__builtin_popcount(i)] + 1;
    //如果 k = 0, 直接输出 1 (因为只有 x = 1 本身“0 次操作到 1”)
    if(k == 0) return void(cout << 1 << endl);
    // 如果 k = 1, 只有为 2^k 时成立, 又 2^0 不成立, 输出 len - 1
    if(k == 1) return void(cout << len - 1 << endl);
    for(int i = 1; i <= len; i++){
        if(f[i] != k - 1) continue;
        int t = i; // 还需要放 t 个 1
        for(int pos = 1; pos <= len && t >= 0; pos++){
            if(s[pos] == '1'){
                // 当前位选 '0', 后面 len - pos 位里选 t 个 '1'
                ans = (ans + dp[len - pos][t]) % mod;
                //当前位选 1, 消耗一个 1, 继续向下判断
                t--;
            }
            // 如果 s[pos] == '0'，只能放 0，不影响 t
        }
        // 一直到最后，如果刚好 `t == 0`
        // 说明我们在严格按照 `s` 的路径上，构造出了一个合法的数，它的 1 的个数刚好为目标 t
        if(t == 0) ans = (ans + 1) % mod;
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
