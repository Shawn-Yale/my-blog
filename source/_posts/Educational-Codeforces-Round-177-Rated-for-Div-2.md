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

下面给出这个视频：[排列构成环](https://www.bilibili.com/video/BV1w2421N75o/?vd_source=f0489718ccab992000c983a006bde4a5) 中的例题：
### [CF1768D. Lucky Permutation](https://codeforces.com/contest/1768/problem/D)(图论)(Permutation)(1800)
如果一个排列有 $cnt$ 个环，且第 $i$ 个环的大小为 $L_i$，满足： $\displaystyle \sum_{i=1}^{cnt} L_i = n \quad L_i \ge 1$
那么把这个排序复位(逆序对数为0)，需要的最少交换总次数：
$\displaystyle \sum_{i=1}^{cnt} (L_i - 1)=\Bigl(\sum_{i=1}^{cnt} L_i\Bigr)-cnt= n - cnt.$
接下来只需要判断，在原排序中是否有一对相邻的位置属于同一个环
- 如果有，那么可以在复位时，不将这两个数复位，交换次数 $- 1$
- 如果没有，就必须再交换一次，交换次数$+ 1$

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
    int cnt = 0; // 统计环的个数
    vi bel(n + 1, -1);
    for(int i = 1; i <= n; i++){
        if(bel[i] != -1) continue;
        for(int j = i; bel[j] == -1; j = a[j]) bel[j] = i;
        cnt++;
    }
    bool flag = false; // 判断是否有相邻位置属于用一个环
    for(int i = 1; i < n; i++)
        if(bel[i] == bel[i + 1]) flag = true;
    cout << n - cnt - (flag ? 1 : -1) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

##### [CF1677C. Tokitsukaze and Two Colorful Tapes](https://codeforces.com/contest/1677/problem/C)(图论)(Permutation)(贪心)(1900)
**我们发现**，如果将上下两种颜色连接，就会形成一个环。比如样例 1，其中 $1,5,3,4$ 和 $2,6$ 形成了两个环。题意相当于给定若干个环，然后要给点重编号，使得所有边的端点编号差之和最大。每个环是独立的，所以我们可以先只考虑一个环。
**我们发现**，$∣a−b∣$ 只有 $a−b$ 或者 $b−a$ 两种情况。所以说，对于环中的一个数 $x$，它要么是都是被减去，要么只被减去一次，要么都是它减去其它数。所以他的贡献只有 $-2x \ , \ 0, \ 2x$ 三种。
**我们总是希望**：大的数 $x$ 的贡献为 $2x$，小的数 $x$ 的贡献为 $-2x$。
同时，在一个长度为 $len$ 的环中，一共有 $\displaystyle \left\lfloor \frac{len}{2} \right\rfloor$ 个 $2x$ 贡献，$\displaystyle \left\lfloor \frac{len}{2} \right\rfloor$ 个 $-2x$ 贡献，$len \ mod \ 2$个$0$， $\displaystyle \left\lfloor \frac{len}{2} \right\rfloor$就是每个环中可以极端配对的数量。
统计整个排列中极端配对的数量和为 $cnt$，依次为这些配对赋值为：$(1, n), \ (2, n - 1), \ ..., \ (cnt, n + 1 - cnt)$，因此答案就是：
$\displaystyle 2\sum_{i = 1}^{cnt}((n + 1 - i) - i) = 2\sum_{i = 1}^{cnt}(n + 1 - 2i) = 2\Bigl(cnt * (n + 1) - 2\sum_{i = 1}^{cnt}i \Bigr)$ 
$= 2\Bigl(cnt * (n + 1) - 2 \frac{cnt * (cnt + 1)}{2}\Bigr)= 2\Bigl(cnt * (n - cnt)\Bigr)$ 
当然，也可以直接 for 循环求和，不必推导最后的公式。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n;

void solve(){
    cin >> n;
    vi a(n + 1), b(n + 1), p(n + 1);
    for(int i = 1; i <= n; i++) cin >> a[i];
    for(int i = 1; i <= n; i++){
        cin >> b[i];
        p[a[i]] = b[i]; // 转化成映射的排列
    }
    int cnt = 0; // cnt为可以极端配对的数量
    vi bel(n + 1, -1);
    for(int i = 1; i <= n; i++){
        int len = 0; // x 为环的长度
        if(bel[i] != -1) continue;
        for(int j = i; bel[j] == -1; j = p[j])
            bel[j] = i, len++;
        cnt += len / 2;
    }
    cout << 2 * cnt * (n - cnt) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```