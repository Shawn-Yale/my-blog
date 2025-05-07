---
title: Codeforces Round 1023 (Div. 2)
date: 2025-05-06 11:05:15
tags:
  - Codeforces
categories:
  - 比赛
---

## [CF2107A. LRC and VIP](https://codeforces.com/contest/2107/problem/A)(贪心)
如果 `数组a` 的 `gcd(a) == x`
对于数组中的元素，如果存在`a[i] != x`，那么肯定有 `a[i] == kx, k != 1`
显然，如果`数组a` 删去这个 `a[i]`，必定存在`gcd(a) != kx`
**证明**：
如果`数组a` 删去这个 `a[i]`，`gcd(a) == kx`
那么当`数组a` 重新加上这个 `a[i]`之后，一定有`gcd(a) == kx`与`gcd(a) == x`矛盾
**综上**：只需要找到一个`a[i] != x`，将其单独分成一组即可
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n;

void solve(){
    cin >> n;
    vi a(n);
    for(int &x : a) cin >> x;
    int all_gcd = a[0];
    for(int &x : a) all_gcd = gcd(all_gcd, x);
    for(int i = 0; i < n; i++){
        if(a[i] != all_gcd){
            cout << "Yes" << endl;
            for(int j = 0; j < n; j++)
                cout << "21"[j == i] << " \n"[j == n - 1];
            return;
        }
    }
    cout << "No" << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

**法二**：
找到`数组a`的最大值`mx`和最小值`mn`
如果`mx == mn`，那么说明整个数组都是同一个数，与题意不符
如果`mx != mn` ，只需要将最大的值放在一组，其他的值放在另一组即可
**证明**：
如果 `mx !=mn`，显然有 `mx < mn`
对于删去了 `mx` 的 `数组a`，`gcd(a) <= mn < mx`
那么拆分出来的两个数组的 `gcd` 必定不相等

```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n;

void solve(){
    cin >> n;
    vi a(n);
    for(int &x : a) cin >> x;
    // ranges::max(a) 为 c++20 版本的语法
    // 可用for循环求最大值代替
    int mx = ranges::max(a), mn = ranges::min(a);
    if(mn == mx) cout << "No" << endl;
    else{
        cout << "Yes" << endl;
        for(int i = 0; i < n; i++)
            cout << "21"[a[i] == mx] << " \n"[i == n - 1];
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

## [CF2107B. Apples in Boxes](https://codeforces.com/contest/2107/problem/B)(博弈论)(贪心)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n, k;

void solve(){
    cin >> n >> k;
    vi a(n);
    for(int &x : a) cin >> x;
    sort(a.begin(), a.end());
    // 判断刚开始是不是必输的局面
    if((a[n - 1] - a[0] > k + 1) || (a[n - 2] - a[0] > k))
        return void(cout << "Jerry" << endl);
    // 谁最后拿完，谁就赢
    // accumulate 是 c++98 的语法
    // 0ll 是 整数字面量，从 c++11开始支持
    cout << (accumulate(a.begin(), a.end(), 0ll) & 1 ? "Tom" : "Jerry") << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF2107C. Maximum Subarray Sum](https://codeforces.com/contest/2107/problem/C)(DP)
可以先看下方的例题，复习一下`Kadane算法`
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int inf = 1e18;
int n, k;
string s;

int get_mx(const vi &a){
    int cur_max = a[0], mx = a[0];
    for(int i = 1; i < n; i++){
        cur_max = max(cur_max + a[i], a[i]);
        mx = max(mx, cur_max);
    }
    return mx;
};

void solve(){
    cin >> n >> k >> s;
    vi a(n);
    for(int i = 0; i < n; i++){
        cin >> a[i];
        if(s[i] == '0') a[i] = -inf;
    }
    int mx = get_mx(a);
    if(mx > k) return void(cout << "No" << endl);
    // 如果已经超过 k，没法“凑小”，直接无解
    int pos = s.find('0');
    if(pos == -1){
        // 考虑全都是'1'的特殊情况：只能看 mx 是否恰好等于 k
        if(mx == k){
            cout << "Yes" << endl;
            for(int i = 0; i < n; i++)
                cout << a[i] << " \n"[i == n - 1];
        }else cout << "No" << endl;
    }else{
        // 找到第一个'0'的坐标 pos
        a[pos] = inf;
        // 求`pos+两侧合法区间`的总区间的区间最大子数组和mx
        // 只需要从 a[pos] 中扣除 `mx 比 k 多的数值`
        // 最终的最大子数组和就恰好为 k
        mx = get_mx(a);
        a[pos] -= (mx - k);
        cout << "Yes" << endl;
        for(int i = 0; i < n; i++)
            cout << a[i] << " \n"[i == n - 1]; 
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
对于**常规的最大子数组的区间和**，我们可以使用 `Kadane算法`以 $\mathcal{O}(n)$的时间复杂度快速求解
**思路如下：**
- 维护两个变量：
    - `cur_max`：以当前位置结尾的“当前最大子段和”，
    - `mx`：到目前为止见过的“全局最大子段和”。
- 遍历 $i = 1...n$：
    1. `cur_max = max(cur_max + a[i], a[i])`  
        —— 要么把当前元素接在前面子段上，要么从当前元素重新开一段。
    2. `mx = max(mx, cur_max)`  
        —— 更新全局最优。
**下面是一道例题：**
### [CF327A. Flipping Game](https://codeforces.com/problemset/problem/327/A)(Kadane)(DP)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n, cnt, cur_max, mx;

// cnt: 原数组中 1 的个数
// cur_max: 以 a[i] 结尾的区间最大值
// mx: a[1] ~ a[i] 的所有区间的最大值

void solve(){
    cin >> n;
    vi a(n);
    cnt = 0;
    for(int &x : a){
        cin >> x;
        if(x == 1){
            cnt++;
            x = -1;
        }else x = 1;
        /*
        如果 a[i] = 1, 翻转之后变成 0, 1的个数-1, 所以对答案的贡献为 -1
        如果 a[i] = 0, 翻转之后变成 1, 1的个数+1, 所以对答案的贡献为 1
        */
    }
    // Kadane算法 求区间最大贡献(最大子段和)mx
    cur_max = mx = a[0];
    for(int i = 1; i < n; i++){
        cur_max = max(cur_max + a[i], a[i]);
        mx = max(mx, cur_max);
    }
    // 答案就是 原来的1的个数 + 区间最大贡献
    cout << cnt + mx << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
`kadane算法`还可以从一维拓展到二维，用于求解**最大子矩阵和**等问题
**思路如下：**
1. **划分子问题**  
    将二维矩阵看作行区间和列区间的组合。我们枚举一个「上边界」`top` 和一个「下边界」`bot`，然后把这两行之间（包括这两行）的每一列上的元素累加，得到一个一维数组 `col_sum[k]`，表示列 `k` 在这两行之间的列和。
2. **一维最优子段和**  
    对于固定的 `top` 和 `bot`，二维子矩阵的最大子矩阵和等价于数组 `col_sum[0...m-1]` 的一维最大子段和（即经典 Kadane 问题）。这样我们只需在枚举完所有 `(top, bot)` 对之后，对每个压缩出来的 `col_sum` 数组应用一次一维 Kadane，就能得到包含这两行的最佳子矩阵。
3. **时间复杂度**
    - 枚举 `top`：$\mathcal{O}(n)$
    - 枚举 `bot`（`bot >= top`）：均摊也是 $\mathcal{O}(n)$
    - 构造 `col_sum`：$\mathcal{O}(m)$
    - 一维 Kadane：$\mathcal{O}(m)$
    - 总体：$\mathcal{O}(n^2 * m)$

$\mathcal{O}(n^2 * m)$是目前已知的，**最大子矩阵和**问题的最优时间复杂度
优于暴力枚举$\mathcal{O}(n^6)$ 和二维前缀和$\mathcal{O}(n^2m^2)$
下面给出求解**最大子矩阵和**的通用代码：
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
const int inf = 0x3f3f3f3f;
int n, m, ans, cur_max, mx;
// ans: 最终的最大子矩阵和

void solve(){
    cin >> n >> m;
    vii a(n, vi(m));
    for(int i = 0; i < n; i++)
        for(int j = 0; j < m; j++)
            cin >> a[i][j];
    
    ans = -inf;
    vi col_sum(m); // col_sum: 临时数组，用来存放压缩后的列和
    for(int i = 0; i < n; i++){ // 枚举上边界 i
        fill(col_sum.begin(), col_sum.end(), 0ll);
        for(int j = i; j < n; j++){ // 枚举下边界 j
            for(int k = 0; k < m; k++){
                // 前缀和的思想
                // col_sum[k]: 从第i行到第j行，第k列上的和
                col_sum[k] += a[j][k];
            }
            // 接下来对当前的 col_sum 数组执行一维 Kadane，求最大子段和
            cur_max = mx = col_sum[0];
            for(int k = 1; k < m; k++){
                cur_max = max(cur_max + col_sum[k], col_sum[k]);
                mx = max(mx, cur_max);
            }
            ans = max(ans, mx); // 更新整体答案
        }
    }
    cout << ans << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
**稍微变形一下：**
### [CF22B. Bargaining Table](https://codeforces.com/contest/22/problem/B)(Kadane)(DP)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
int n, m, ans;

void solve(){
    cin >> n >> m;
    vii a(n, vi(m));
    for(int i = 0; i < n; i++)
        for(int j = 0; j < m; j++){
            char c; cin >> c;
            a[i][j] = (c == '1');
        }
    ans = 0;
    vi good(m);
    // good[j] 表示当前 [i..j] 行区间内，第 k 列是否全为 0
    for(int i = 0; i < n; i++){
        fill(good.begin(), good.end(), 1ll);
        for(int j = i; j < n; j++){
            for(int k = 0; k < m; k++){
            // 只有上一轮也是好列，且当前行也是 0，才继续保持“好列”
                good[k] &= (a[j][k] == 0);
            }
            int h = j - i + 1; // h: 当前高度
            int max_w = 0, cur = 0;
            // max_w: 最长连续的`0`段
            for(int k = 0; k < m; k++){
                if(good[k]) cur++, max_w = max(max_w, cur);
                else cur = 0;
            }
            if(max_w) // 只有当 max_w>0 时才有合法矩形
                ans = max(ans, 2 * (h + max_w));
        }
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