---
title: 期望DP
date: 2025-04-21 19:28:42
tags: [DP, 期望DP]
categories: [算法笔记]
mathjax: true
---

#### [CF398B. Painting The Wall](https://codeforces.com/problemset/problem/398/B)(期望DP)(1700)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/11/1128/solution/cf398b.md)**
**状态表示：**$dp[i][j]$为给`i`行`j`列涂色的期望
**状态转移方程：**
$$
dp[i][j] = 1 + \frac{i \cdot j}{n^2}\,dp[i-1][j-1] + \frac{i \cdot (n-j)}{n^2}\,dp[i-1][j] 
$$
$$ + \frac{(n-i) \cdot j}{n^2}\,dp[i][j-1] + \frac{(n-i) \cdot (n-j)}{n^2}\,dp[i][j].$$
**也可转化为：**

$$
dp[i][j] = \frac{n^2 + i \cdot j\,dp[i-1][j-1] + i \cdot (n-j)\,dp[i-1][j] + (n-i) \cdot j\,dp[i][j-1]}
           {n^2 - (n-i)(n-j)}.
$$
**边界状态：**
1. 当 i = 0 且 j = 0（所有行和列均满足）时，$dp[0][0] = 0.$
2. 当只有行不满足（即 $j = 0$）时，$dp[i][0] = dp[i-1][0] + \frac{n}{i}.$
3. 同理，当只有列不满足（即 $i = 0$）时，$dp[0][j] = dp[0][j-1] + \frac{n}{j}.$

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
#define pii pair<int, int>
#define arr3 array<int, 3>
const int N = 2e3 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7, MOD = 998244353;
int n, m, cnt_row, cnt_col, row[N], col[N];
double dp[N][N];

void solve(){
    cin >> n >> m;
    while(m--){
        int r, c; cin >> r >> c;
        if(!row[r]) row[r] = 1, cnt_row++;
        if(!col[c]) col[c] = 1, cnt_col++;
    }
    int a = n - cnt_row, b = n - cnt_col;
    dp[0][0] = 0.0;
    for(int i = 1; i <= a; i++) dp[i][0] = dp[i - 1][0] + n * 1.0 / i;
    for(int i = 1; i <= b; i++) dp[0][i] = dp[0][i - 1] + n * 1.0 / i;
    for(int i = 1; i <= a; i++)
        for(int j = 1; j <= b; j++)
            dp[i][j] = (1.0 * n * n + i * j * dp[i - 1][j - 1] + i * (n - j) * dp[i - 1][j] + (n - i) * j * dp[i][j - 1]) / (1.0 * n * n - 1.0 * (n - i) * (n - j));
    cout << fixed << setprecision(10) << dp[a][b] << endl;
}
 
signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

#### [CF518D. Ilya and Escalator](https://codeforces.com/problemset/problem/518/D)(期望DP)(1700)(感觉其实分打高了)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2025/02/0212/solution/cf518d.md)**
**提示 1：** 典型的期望 DP ，考虑一秒后人数如何变化。
很明显的是，每个时刻，关键的信息只有当前在电梯上的人数。
考虑这个变量如何变化。
如果电梯上已经有 $n$ 个人了，则人数不变。
否则，假设当前有 $i$ 个人，则有 $p$ 的概率变成 $i+1$ 个人，有 $1-p$ 的概率仍然是 $i$ 个人。
因此完成状态转移，进行 DP 即可。
时间复杂度为 $\mathcal{O}(tn)$ 
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
#define pii pair<int, int>
#define arr3 array<int, 3>
const int N = 2e3 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7, MOD = 998244353;
int n, t;
double p, dp[N][N], ans;
// dp[i][j]: 第 i 秒电梯上有 j 个人的概率
void solve(){
    cin >> n >> p >> t;
    dp[0][0] = 1.0;
    for(int i = 0; i < t; i++){
        for(int j = 0; j < n; j++)
            dp[i + 1][j + 1] += dp[i][j] * p, dp[i + 1][j] += dp[i][j] * (1 - p);
        dp[i + 1][n] += dp[i][n];
    }
    for(int i = 0; i <= n; i++)
        ans += dp[t][i] * i;
    cout << fixed << setprecision(10) << ans << endl;
}
 
signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```