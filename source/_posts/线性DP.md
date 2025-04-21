---
title: 线性DP
date: 2025-04-21 19:29:39
tags: [DP, 线性DP]
categories: [算法笔记]
mathjax: true
---

#### [CF566F. Clique in the Divisibility Graph](https://codeforces.com/problemset/problem/566/F)(最长公共子序列)(线性DP)(1500)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/03/0322/solution/cf566f.md)**
**提示 1：** 一个团表示原图的一个子图，其中点两两连了一条无向边。那么在题中对图的定义下，团中点满足什么性质？
**提示 2：** 如何找到满足性质的最大子集？
我们先看提示 1，一个团中点两两连线，而根据题目的定义，连线的点对应的数应当满足整除关系。因此一个团内所有的点可以两两满足整除关系。
我们从小到大考虑每一个数，则大的数得被所有比它小的数整除。考虑排序后的数组为 $a_1,a_2,\dots,a_n$ ，则 $a_{i+1}$ 能被 $a_1,a_2,\dots,a_i$ 整除。
事实上，上述条件可以简化为对于每一个 $i$ 有 $a_{i+1}$ 被 $a_i$ 整除，因为 $a_i$ 已经可以被 $a_1,a_2,\dots,a_{i-1}$ 整除了， $a_i$ 已经包含了所有必要的整除信息。
综上所述，我们只需要找到一个最大的集合，使其排序后相邻两项满足整出关系。
如何找到最大集合呢？我们考虑使用 DP 。
设某个数 `x` 是集合 A 中的数，那么以它为起点去找所有是 `x` 的倍数的数（如 `2 -> 4,6,8,...`），那么所有这些数都能整除 `x` 的倍数。
假设我们目前找到的以 $x$ 作为最大元素的集合至多有 $k$ 个元素，则其下一个元素可能是 $2x,3x,\dots$ ，我们枚举下一个元素进行状态转移即可。（这么看题目对于数字两两不同的限制还是挺多余的）
为什么这样做是合理的呢？因为我们元素有上界 $10^6$ ，因此对于 $x$ 状态的转移次数为 $\frac{10^6}{x}$ ，于是，我们可以得到总状态转移次数是 $\sum\limits_{x=1}^{10^6}\frac{10^6}{x}=10^6\sum\limits_{x=1}^{10^6}\frac{1}{x}=\mathcal{O}(10^6\log 10^6)$ .
于是时间复杂度为 $\mathcal{O}(N\log N)$ .
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 1e6 + 10, M = 1e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, x, dp[N], maxn;

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++){
        cin >> x;
        dp[x]++;
        maxn = max(maxn, dp[x]);
        for(int j = 2; j * x < N; j++)
            dp[j * x] = max(dp[j * x], dp[x]);
    }
    cout << maxn << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
} 
```
#### [CF605A. Sorting Railway Cars](https://codeforces.com/problemset/problem/605/A)(最长上升子序列)(线性DP)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2025/04/0412/solution/cf605a.md)**
**提示 1：** 考虑那些没有动过的元素，他们应该满足什么条件？
**提示 2：** 如何计算不动的元素的最大数量？
动的元素会放到最前面或者最后面，所以不动的元素一定是中间连续的一段。
也就是说，不动的元素作为原序列的一个子序列，最后得是最终序列中连续的一段。
如果我们找到了这么一个子序列，那么剩余的元素分别只需一次操作就可以排好了。假设选取的子序列的数值为 `[a,a+1,…,b]` ，那么可以先将 a−1,…,1 依次放到前面去，再把 b+1,…,n 依次放到后面去。
这样，我们就要求最长的子序列，该子序列相邻两项差为 1 。
假设到第 i 个位置时，最后一项为 y 的子序列的最大长度为 dp[i][y] ，那么考虑第 i+1 个位置，只需更新 `dp[i+1][nums[i+1]]` 为 `dp[i][nums[i+1]−1]+1` 即可，其余位置无需改变。
因此实际上可以只开一个数组，且每遍历一个元素改变一个位置的对应 DP 结果就行。
最后，对所有 DP 结果取最大值，用 n 减掉它就是答案了。
时间复杂度为 O(n) 。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
#define pii pair<int, int>
#define arr3 array<int, 3>
const int N = 1e5 + 10, M = 2e3 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, dp[N], x;

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++){
        cin >> x;
        dp[x] = dp[x - 1] + 1;
    }
    cout << n - *max_element(dp + 1, dp + n + 1) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

#### [CF788A. Functions again](https://codeforces.com/problemset/problem/788/A)(线性DP)(1600)
 **[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/03/0314/solution/cf788a.md)**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 1e6 + 10, M = 1e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, a[N], d[N], dp[N][2], maxn;
// dp[i][0]:表示第i位是 ‘+’  dp[i][1]:表示第i位是 ‘-’ 

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i];
    for(int i = 1; i < n; i++) d[i] = abs(a[i] - a[i + 1]);
    for(int i = 1; i < n; i++){
        dp[i][0] = max(dp[i - 1][1] + d[i], d[i]);
        dp[i][1] = max(dp[i - 1][0] - d[i], dp[i][1]);
        // 其实dp[i][1] = max(dp[i - 1][0] - d[i], 0ll); 也一样
    }
    for(int i = 1; i < n; i++) maxn = max({maxn, dp[i][0], dp[i][1]});
    cout << maxn << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

#### [CF1498C. Planar Reflections](https://codeforces.com/problemset/problem/1498/C)(线性DP)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/06/0601/solution/cf1498c.md)**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
const int N = 1e3 + 10, M = 1e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, k;
// dp[i][j]: 表示衰变年龄为 i 的粒子，前面有 j 个平面产生的粒子总和

void solve(){
    cin >> n >> k;
    vii dp(k + 1, vi(n + 1, 0));
    for(int i = 1; i <= n; i++) dp[1][i] = 1;
    for(int i = 1; i <= k; i++) dp[i][0] = 1;
    for(int i = 2; i <= k; i++)
        for(int j = 1; j <= n; j++)
            dp[i][j] = (dp[i][j - 1] + dp[i - 1][n - j]) % mod;
    cout << dp[k][n] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

#### [CF1033C. Permutation Game](https://codeforces.com/problemset/problem/1033/C)(博弈论 + 线性DP)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/06/0622/solution/cf1033c.md)**
**提示 1：** 这是个博弈相关的 DP 问题。想想 DP 的状态变量与转移方法。
**提示 2：** DP 需要满足上述转移关系是不会成环的。这里怎么保证这件事？各个状态应该按照什么顺序依次得到结果？
**提示 3：** 如何确定你的做法是不会超时的？
这是单个博弈，考虑从位置 $x$ 开始，如果能赢输出 $1$ ，否则输出 $0$ .
**那么我们看 $x$ 能到达的新位置。如果其能到达 $0$ 的状态，则可以使得对方赢不了，输出 $1$ 即可，否则输出 $0$ .**
这样我们就知道了状态和各个状态的转移关系。
而我们要按照某一个顺序依次确定每个位置的 DP 数值。我们发现更小的数总是需要更大的数的 DP 结果来推出其本身的 DP 数值。因此我们 **按照数值从大到小依次确定每个位置的 DP 数值** 。
接下来对于每个位置 $i$ ，枚举所有 $|i-j|\bmod a_i=0$ 的位置，看是否存在 $j$ 满足 $a_j\gt a_i$ 且 $dp_j=0$ ，如果是，则输出 $1$ ；否则，输出 $0$ 。
上述步骤的时间复杂度如何呢？
如果我们考虑的数值是 $i$ ，则其枚举的位置数量为 $\frac{n}{i}$ 量级的，因此，由于：
$\sum\limits_{i=1}^n\frac{n}{i}=n\sum\limits_{i=1}^n\frac{1}{i}=\mathcal{O}(n\log n)$
因此，整体的时间复杂度是 $\mathcal{O}(n\log n)$ 的。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 1e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, a[N], pos[N], dp[N];

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i], pos[a[i]] = i;
    for(int i = n; i; i--)
        for(int j = pos[i] % i; j < N; j += i)
            if(a[j] > i && !dp[j])
                dp[pos[i]] = 1;
    for(int i = 1; i <= n; i++) cout << (dp[i] ? 'A' : 'B');
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

#### [CF735C. Tennis Championship](https://codeforces.com/problemset/problem/735/C)(线性DP)(有点意思hhh)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/09/0913/solution/cf735c.md)**
**提示 1：** 直接将人拆分上下半区讨论复杂度显然不容易控制。考虑反向解决这个问题。
**提示 2：** 如果当前深度为 x ，能容纳的最多叶子节点数是多少？
需要反向思考问题。
如果直接考虑这个问题，相当于需要枚举前后两个子树的大小，再看深度，由于节点数量过多，显然不合理；同时两棵子树很不一样，无法直接得到分割位置。那怎么办呢？
考虑深度为 h 的二叉树最少容纳多少个节点。**一旦超过了我们的要求，则输出对应的深度。**
考虑深度为 h 的二叉树。则其左右两颗子树中必然有一棵深度为 h−1 ，根据条件，另一棵的深度至少为 h−2 。于是得到递推关系 f(h)=f(h−1)+f(h−2) 。
这个递推关系是呈指数级增长的，因此至多只需要 O(log⁡n) 次即可找到答案。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 1e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, t0 = 1, t1 = 2, t2 = 3, t = 1;

void solve(){
    cin >> n;
    while(t2 <= n){
        t0 = t1;
        t1 = t2;
        t2 = t0 + t1;
        t++;
    }
    cout << t << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

#### [CF429B. Working out](https://codeforces.com/problemset/problem/429/B)(线性DP)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/09/0928/solution/cf429b.md)**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 1e3 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, m, a[N][N], dp1[N][N], dp2[N][N], dp3[N][N], dp4[N][N], ans;

void solve(){
    cin >> n >> m;
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++)
            cin >> a[i][j];
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++)
            dp1[i][j] = max(dp1[i - 1][j], dp1[i][j - 1]) + a[i][j];
    for(int i = n; i; i--)
        for(int j = 1; j <= m; j++)
            dp2[i][j] = max(dp2[i + 1][j], dp2[i][j - 1]) + a[i][j];
    for(int i = 1; i <= n; i++)
        for(int j = m; j; j--)
            dp3[i][j] = max(dp3[i - 1][j], dp3[i][j + 1]) + a[i][j];
    for(int i = n; i; i--)
        for(int j = m; j; j--)
            dp4[i][j] = max(dp4[i + 1][j], dp4[i][j + 1]) + a[i][j];
    for(int i = 2; i < n; i++) // 注意不可能在边界上相遇
        for(int j = 2; j < m; j++)
            ans = max({ans, dp1[i - 1][j] + dp2[i][j - 1] + dp3[i][j + 1] + dp4[i + 1][j], dp1[i][j - 1] + dp2[i + 1][j] + dp3[i - 1][j] + dp4[i][j + 1]});
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

#### [CF407B. Long Path](https://codeforces.com/problemset/problem/407/B)(线性DP)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/10/1025/solution/cf407b.md)**
**提示 1：** 当我们第一次走到 $i$ 时，所有前面的门的状态都是一致的。
**提示 2：** 每次走到 $p_i$ 相当于要再经历一遍 $p_i\to p_i+1,\dots,i\to i+1$ 。
**提示 3：** 上述步骤的和如何快速计算？
我们观察我们进行题目中的过程时，第一次走 $i\to i+1$ 边时，前面所有点的状态。我们发现所有点最后一次进行的操作一定是往后一格。
为什么呢？
可以使用数学归纳法。
我们第一次到达 $i$ 时，只能从 $i$ 走回 $p_i$ 。此时 $p_i$ 到 $i-1$ 的状态与之前刚到达 $p_i$ 时一致。
因此，从 $p_i$ 走到 $i$ 的过程可以使用归纳假设，即可知到达 $i$ 时，前面所有点的状态满足结论。而接下来要做的就是 $i$ 走到 $i+1$ ，因此也满足结论。
而上述过程也提示了我们，从 $i$ 走到 $i+1$ 相当于先从 $i$ 走到 $p_i$ ，再从 $p_i$ 走到 $i$ ，最后走 $i$ 到 $i+1$ 。因为点的状态满足上面的结论，因此这边的计算结果可以复用。
而由于上述结论，从 $0$ 走到 $i$ 等价于从 $0$ 走到 $p_i$ 再从 $p_i$ 走到 $i$ （整个数组中每个点最后一次执行的都是 $i\to i+1$ ），因此可以由此推得 $p_i$ 到 $i$ 的步数。
具体而言，假设从 $0$ 走到 $i$ 所需的总步数是 $dp[i]$ ，则从 $0$ 到 $i+1$ 可以拆分为 $0$ 到 $i$ 再从 $i$ 到 $i+1$ 。
$dp[i+1]=dp[i]+1+(dp[i]-dp[p_i])+1=2dp[i]-dp[p_i]+2$
于是找到线性递推关系。时间复杂度为 $\mathcal{O}(n)$ 。
**所以其实 $n$ 的范围可以开很大**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 1e3 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, p[N], dp[N];

void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> p[i];
    for(int i = 2; i <= n + 1; i++){
        dp[i] = 2 * dp[i - 1] - dp[p[i - 1]] + 2;
        dp[i] = (dp[i] + mod) % mod; // 可能是负数
    }
    cout << dp[n + 1] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```

#### [CF319A. Malek Dance Club](https://codeforces.com/problemset/problem/319/A)(二进制 + 线性DP)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2024/11/1130/solution/cf319a.md)**
**提示 1：** 逐位考虑。
**提示 2：** 数组一定是前半段大后半段小 / 前半段小后半段大，段内的大小关系保持一致，因此可以转化为规模更小的问题。
数字大小比较是从最高位开始的，因此我们从最高位开始考虑问题。
如果最高位是 $0$ ，那么最高位没有影响。这一位不产生逆序对。
否则，前面 $2^{n-1}$ 个数中的每一个都比后面 $2^{n-1}$ 个数大，产生 $2^{2n-2}$ 个逆序对。
接下来怎么办呢？发现左右两边分别是 $0\sim 2^{n-1}-1$ 和 $2^{n-1}\sim 2^n - 1$ ，左右两侧之间产生的逆序对都已经解决了，因此可以直接分别求两部分的逆序对数量。
同时，这两侧除了最高位 $2^{n-1}$ 之外都是一致的，因此逆序对情况也一致，直接考虑一侧的结果再乘 $2$ 即可。
于是，**只需解决长度为 $i$ 的后缀的结果，乘 $2$ ，再加上第 $i+1$ 位产生的逆序对，就是长度为 $i+1$ 的后缀的结果**，也就解决了本题。
时间复杂度为 $\mathcal{O}(n)$ ，其中 $n$ 为输入字符串的长度。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 1e3 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, cur = 1, ans;
string s;

void solve(){
    cin >> s;
    n = s.size();
    for(int i = n - 1; i >= 0; i --){
        ans = (ans * 2) % mod;
        if(s[i] == '1')
            ans = (ans + cur * cur) % mod;
        cur = (cur * 2) % mod;
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
#### [CF1452D. Radio Towers](https://codeforces.com/problemset/problem/1452/D)(斐波那契数列 + 逆元)(线性DP)(1600)
**思路：**
设有 $n+2$ 个镇，要求在不覆盖第 $n+2$ 个镇的情况下，覆盖第 1 个镇的所有合法放置信号塔的方案数为 $f(n)$。显然，答案是：
$$
\frac{f(n)}{2^n}
$$
手动推几组样例后发现 $f(n)$ 满足斐波那契数列，因此最终答案为：
$$
\frac{Fib(n)}{2^n}
$$
我们可以显然得到：
- $f(1) = 1$：只有 1 号镇放信号塔才满足要求。
接下来考虑：  
我们只需要考虑在 $[1, \lceil \frac{n}{2} \rceil]$ 区间内放置一个信号塔的情况。因为一旦位置超过这个范围，就无法保证在能覆盖第 1 个镇的同时，不覆盖到第 $n+1$ 个镇。
- $n = 2$：
  - 在 1 号镇放塔，则剩下的区域（2）是 $n = 1$ 的情况。
  - 所以 $f(2) = f(1) = 1$
- $n = 3$：
  - 在 1 号镇放塔，剩下（2,3）是 $f(2)$
  - 在 2 号镇放塔，可以正好覆盖所有镇
  - 所以 $f(3) = f(2) + 1$
- $n = 4$：
  - 在 1 号镇放塔，剩下（2,3,4）是 $f(3)$
  - 在 2 号镇放塔，剩下（4）是 $f(1)$
  - 所以 $f(4) = f(3) + f(1)$
- $n = 5$：
  - 在 1 号镇放塔，剩下（2,3,4,5）是 $f(4)$
  - 在 2 号镇放塔，剩下（4,5）是 $f(2)$
  - 在 3 号镇放塔，可以正好覆盖所有镇
  - 所以 $f(5) = f(4) + f(2) + 1$
- ......
设信号塔建在位置 $i \in [1, \lceil \frac{n}{2} \rceil]$，为了覆盖到 1 号镇，信号范围需为 $i$，可覆盖区间为 $[1, 2i-1]$。
对于剩下的区间 $[2i, n]$，我们可以视为一个子问题：求 $f(n - 2i + 1)$。
所以递推关系为：

$$
f(n) =
\begin{cases}
f(n-1) + f(n-3) + \cdots + f(2) + 1, & \text{n 为偶数} \\
f(n-1) + f(n-3) + \cdots + f(1), & \text{n 为奇数}
\end{cases}
$$
若 $n$ 为偶数：
$$
\begin{aligned}
f[n] &= f[1] + f[3] + f[5] + \cdots + f[n - 1] \\
     &= f[2] + f[3] + f[5] + \cdots + f[n - 1] \\
     &= f[4] + f[5] + \cdots + f[n - 1] \\
     &= f[n - 2] + f[n - 1]
\end{aligned}
$$
若 $n$ 为奇数：

$$
\begin{aligned}
f[n] &= 1 + f[2] + f[4] + \cdots + f[n - 1] \\
     &= f[1] + f[2] + f[4] + \cdots + f[n - 1] \\
     &= f[3] + f[4] + \cdots + f[n - 1] \\
     &= f[n - 2] + f[n - 1]
\end{aligned}
$$
**综上**：`f[n] = f[n - 1] + f[n - 2]`
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 2e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 998244353;
int n, dp[N] = {0, 1, 1}, inv2 = (mod + 1) >> 1;

int qpow(int a, int b){
    int ans = 1;
    while(b){
        if(b & 1) ans = ans * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return ans;
}

void solve(){
    cin >> n;
    for(int i = 3; i <= n; i++) dp[i] = (dp[i - 1] + dp[i - 2]) % mod;
    cout << (dp[n] * qpow(inv2, n)) % mod << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [CF1238C. Standard Free2play](https://codeforces.com/problemset/problem/1238/C)(线性DP)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2025/03/0301/solution/cf1238c.md)**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 2e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 998244353;
int h, n, a[N], dp[N];

void solve(){
    memset(dp, 0, sizeof dp);
    cin >> h >> n;
    for(int i = n; i; i--) cin >> a[i];
    for(int i = 2; i <= n; i++)
        if(a[i - 1] == a[i - 2] + 1) dp[i] = dp[i - 2];
        else dp[i] = dp[i - 1] + 1; 
    cout << dp[n] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [CF1875D. Jellyfish and Mex](https://codeforces.com/problemset/problem/1875/D)(线性DP)(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2025/03/0307/solution/cf1875d.md)** 
**提示 1：** MEX 变不了几次。
**提示 2：** 什么时候 MEX 会变呢？你刚好把比当前 MEX 更小的某个数全部删掉的时候。
考虑 MEX 的变化时刻。
首先 MEX 一定是变小的。假设当前的 MEX 是 $x$ ，那么其下一次变化一定是把比 $x$ 小的某一个数全部删掉了。
因此，假设当前的 MEX 等于 $x$ ，对应的最小成本为 $dp[x]$ ，而新 MEX 等于 $y$ ，则有：
$dp[y]=\min_{x} ( \ dp[x] \ + \ x \ *（cnt[y] - 1） + \ y)$
最后一定到达 MEX 等于 $0$ 的状态，直接输出 $dp[0]$ 即可。
注意， MEX 不会超过 $n$ ，因此只需维护 $n-1$ 内的数的出现频率即可。
时间复杂度为 $\mathcal{O}(n^2)$ 。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 2e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, r;
// dp[i]: 将 mex 变成 i 需要的代价

void solve(){
    cin >> n;
    vi cnt(n , 0);
    for(int i = 1; i <= n; i++){
        int x; cin >> x;
        if(x < n) cnt[x]++;
    }
    for(int i = 0; i < n; i++)
        if(!cnt[i]){
            r = i;
            break;
        }
    vi dp(r + 1, inf);
    dp[r] = 0;
    for(int i = r - 1; i >= 0; i--)
        for(int j = i + 1; j <= r; j++)
            dp[i] = min(dp[i], dp[j] + j * (cnt[i] - 1) + i);
    cout << dp[0] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [CF756B. Travel Card](https://codeforces.com/problemset/problem/756/B)(线性DP + (双指针/ 二分))(1600)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2025/03/0308/solution/cf756b.md)**
**提示 1：** 典型的动态规划问题。考虑到第 $i$ 个位置的最小总成本。
**提示 2：** 三种转移——转移前是什么状态？
首先，题目设定让人联想到动态规划问题。因为本质上是挑选区间对点进行覆盖，这件事是很常见的线性 DP 。
接下来怎么办呢？考虑常见的状态定义：覆盖到第 $i$ 个位置的最小成本为 $dp[i]$ 。
这个 dp 根据前一次买的票有三种不同的转移方式——
- 单程： $dp[i-1]+20$ 。
- $90$ 分钟票：找到上一次跟这次时间间隔不到 $90$ 分钟的下标 $i_1$ ，则最后一张票最多覆盖这么多时间点，于是，前面的时间点只能单独考虑，因此前面部分成本为 $dp[i_1-1]$ ，新买的票是 $50$ 。
- $1440$ 分钟票：与前一种票类似，有 $dp[i_2-1]+120$ 。
三者取最小即可。 $i_1,i_2$ 单调变化，因此无需使用二分，指针移动即可。
时间复杂度为 $\mathcal{O}(n)$ ，因为可以 $\mathcal{O}(n)$ 的总时长维护三种转移的对应指针。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 2e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, a[N], dp[N], r1 = 1, r2 = 1;
// dp[i]: 第 i 趟行程为止的最小花费
void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i];
    for(int i = 1; i <= n; i++){
        dp[i] = dp[i - 1] + 20;
        while(a[i] - a[r1] >= 90) r1++;
        while(a[i] - a[r2] >= 1440) r2++;
        dp[i] = min({dp[i], dp[r1 - 1] + 50, dp[r2 - 1] + 120});
    }
    for(int i = 1; i <= n; i++) cout << dp[i] - dp[i - 1] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```
#### [CF924C. Riverside Curio](https://codeforces.com/problemset/problem/924/C)(线性DP)(1700)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2025/02/0205/solution/cf924c.md)**
**提示 1：** 我们已知上面的线，要求下面的线数量的最小值，其实就是求每天线的数量的最小值。
**提示 2：** 考虑总线条数量需要满足什么不等关系。
先看提示 1，我们将问题转化为每天的线条数的和的最小值。
而这个最小值需要满足什么条件呢？每天的线条数首先不小于上面的线条数量。同时，每天最多新增一条，因此 $nums_{i+1}\leq nums_i+1$ ，同时，线条数不会变少，有 $nums_{i+1}\geq nums_i$ 。
**总结一下：**
- $nums_{i}\geq  m_i + 1$ , 线的数量 大于等于 上面的线 + 1
- $nums_{i+1}\geq nums_i$  , 线的数量单调递增
- $nums_{i - 1} + 1\geq  nums_i$ , 每天只可能比前一天多一条线
我们尽可能贴着条件`1,2`限制的边界也就是尽量把线画重，因为线的数量越少越好，后面我们要扫回来的原因是我们贴着边界画不一定画的出来合法解，我们需要把以前画重的线拆开，以满足后面的需要。
根据上述条件，将原数组往前往后扫描两次使得上述条件满足即可。
时间复杂度为 $\mathcal{O}(n)$ 
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define vii vector<vi>
#define pii pair<int, int>
#define arr3 array<int, 3>
const int N = 1e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7, MOD = 998244353;
int n, ans;

void solve(){
    cin >> n;
    vi nums(n, 0);
    for(auto &x : nums){
        cin >> x;
        x++;
        ans -= x;
    } // 先减去 (上面的线 + 1)
    for(int i = 1; i < n; i++) nums[i] = max(nums[i], nums[i - 1]);
    for(int i = n - 2; i >= 0; i--) nums[i] = max(nums[i], nums[i + 1] - 1);
    for(auto &x : nums) ans += x;
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
#### [CF933A. A Twisty Movement](https://codeforces.com/problemset/problem/933/A)(线性DP)(1800)
**提示 1：** 如果原来的非递减序列进行了一次反转操作，其会变成什么形式？
**提示 2：** 如何统计对应的形式的最长的子序列？
事实上，最后的非递减序列一定是 $1,1,\dots,1,2,\dots,2,2$ 的形式，而如果在前面的 $1,1,\dots,1$ / $2,2,\dots,2$ 中反转，则完全不影响；否则会变成一段 $1$ ，一段 $2$ ，一段 $1$ ，一段 $2$ 的形式。
如果我们使得其中每一段的长度可以为 $0$ ，那么最后子序列在反转前总可以表示为一段 $1$ ，一段 $2$ ，一段 $1$ ，一段 $2$ 。
因此可以直接使用这个性质进行 DP，四种状态是选取到了第几段子序列，分别对应于 $dp_1, dp_{12}, dp_{121}, dp_{1212}$ ，则由于每段子序列可以非空，每一个状态可以直接转移为后一个状态。而如果当前数字是 $1$ ，则第一种和第三种状态长度可以 $+1$ ；否则，第二种和第四种状态长度可以 $+1$ 。
这样我们就用 $\mathcal{O}(n)$ 的动态规划解决了这个问题。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
const int N = 2e3 + 10, M = 6e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7, MOD = 998244353;
int n, a[N], dp[N][4];
/*
| 0  | 在前 i 个数中，选的全是 1 的最长非递减子序列长度（只选 1）          |
| 1  | 在前 i 个数中，选一段 1 后接一段 2 的最长非递减子序列长度           |
| 2  | 在前 i 个数中，选一段 1、再选一段 2，接着反转选一段 1，最长非递减子序列长度 |
| 3  | 在前 i 个数中，选一段 1，接 2，接反转后的 1，再接反转后的 2（完整的一段反转 + 正常部分）|
*/
void solve(){
    cin >> n;
    for(int i = 1; i <= n; i++){
        cin >> a[i];
        dp[i][0] = dp[i - 1][0] + (a[i] == 1);
        dp[i][1] = max(dp[i][0], dp[i - 1][1] + (a[i] == 2));
        dp[i][2] = max(dp[i][1], dp[i - 1][2] + (a[i] == 1));
        dp[i][3] = max(dp[i][2], dp[i - 1][3] + (a[i] == 2));
    }
    cout << dp[n][3] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```