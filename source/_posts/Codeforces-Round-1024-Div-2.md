---
title: Codeforces Round 1024 (Div. 2)
date: 2025-05-12 08:34:10
tags:
  - Codeforces
categories:
  - 比赛
---

## [CF2102B. The Picky Cat](https://codeforces.com/contest/2102/problem/B)
给定一个整数数组 $a_1, a_2, \ldots, a_n$，你可以**任意次将某个元素乘以 -1**（包括 $a_1$），目的是让**位置 1 上的元素 $a_1$** 变成**数组的中位数**。
**中位数定义**：是排序后数组中第 $\displaystyle \left\lceil \frac{n}{2} \right\rceil$ 小的元素（下标从 1 开始）。
* 例：长度为 5，排序后：$[1, 3, 5, 7, 9]$，中位数是第 3 个 → `5`
* 例：长度为 4，排序后：$[1, 2, 3, 4]$，中位数是第 2 个 → `2`

1. **若 $|a_1|$ 为中位数**，我们必须安排数组中：
	* 恰好有 $\displaystyle \left\lceil \frac{n}{2} \right\rceil - 1$ 个元素 **小于它**（即排在它前面）
	* 其他都排在它后面。
	* 对于每个 $|a_i| < |a_1|$ 的元素，不管你怎么取符号，它的数值一定落在 $(-|a_1|, |a_1|)$ 之间。
	* 也就是说：这些元素的**绝对值比 $|a_1|$ 小**，你没法让它们排在 $|a_1|$ 后面，它们肯定排在 $|a_1|$ 前面。
	
	如果绝对值小于 $|a_1|$ 的元素数量 $\displaystyle \le \left\lfloor \frac{n}{2} \right\rfloor$，那我们就可以让 $|a_1|$ 成为中位数
2. 同理，**考虑 $-|a_1|$ 为中位数**，我们必须安排数组中：
	* 恰好有 $\displaystyle \left\lfloor \frac{n}{2} \right\rfloor$ 个元素 **大于它**（即排在它后面），因为 $\displaystyle n - \left\lceil \frac{n}{2} \right\rceil = \left\lfloor \frac{n}{2} \right\rfloor$
	* 其他都排在它前面。
	* 对于每个 $|a_i| < |a_1|$ 的元素，不管你怎么取符号，它的数值一定落在 $(-|a_1|, |a_1|)$ 之间。
	* 也就是说：这些元素的**绝对值比 $|a_1|$ 小**，你没法让它们排在 $-|a_1|$ 前面，它们肯定排在 $-|a_1|$ 后面。
	
	如果绝对值小于 $|a_1|$ 的元素数量 $\displaystyle \le \left\lfloor \frac{n}{2} \right\rfloor$，那我们就可以让 $-|a_1|$ 成为中位数

**综上：** 如果绝对值小于 $|a_1|$ 的元素数量 $\displaystyle \le \left\lfloor \frac{n}{2} \right\rfloor$，那我们就可以让 $a_1$ 成为中位数
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

void solve(){
    int n; cin>> n;
    int l = 0, r = 0, cur;
    cin >> cur;
    cur = abs(cur);
    for(int i = 2; i <= n; i++){
        int x; cin >> x;
        abs(x) < cur ? l++ : r++;
    }
    cout << (l <= n / 2 ? "YES" : "NO") << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```

## [CF2102D. Quartet Swapping](https://codeforces.com/contest/2102/problem/D)(树状数组)(逆序对)
对于**交换数组中的元素，使得字典序最小的问题(交换的方式有特殊限制)**，一定要考虑与正常的交换型排序有什么关系和差别。
对于本题，奇数位与奇数位交换，偶数位与偶数位交换，**即奇数位与偶数位互不影响**。
**因为**，对于一个长度 $\ge 4$ 的子数组，我们总可以通过交换，将第一位转化成奇数位数组最小的值。然后就可以将第一位固定，后面的 长度-1 的数组也依次确定该数组的第一位，直到长度 $= 4$。
**所以**，对于长度为 $len$ 的数组，前 $len - 3$ 位数与 第 $len - 1$位数一定是按奇偶位升序排序的（下标从1开始）。
**考虑每一次交换的四个数：$a, b, c, d$**。**因为**奇偶位分成两个数组，**所以** $a, c$ 在同一个数组，且 $a, c$ 相邻；$b, d$ 在同一个数组，且 $b,d$ 相邻。**因为**相邻的两个数交换，对于逆序对的影响是 $+1$ 或者$-1$，**所以**，这四个数交换，对于逆序对的总影响是 $+ 2$ 或 $0$ 或 $-2$，即影响是偶数的，不影响总逆序对的奇偶性。
综上：我们只需要求出奇数位数组与偶数位数组的逆序对之和，**如果**逆序对之和为偶数，**则**可以实现奇数位和偶数位分别升序，即总逆序对为 0；**如果**逆序对之和为奇数，**则**需要将第 $len - 2$ 位数与第 $len$ 位数交换，构造一个逆序对为 1 的 排序。
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define lowbit(x) (x & -x)
const int N = 2e5 + 10;
int n, tr[N];

void add(int x, int k){
    for(int i = x; i <= n; i += lowbit(i)) tr[i] += k;
}

int sum(int x){
    int ans = 0;
    for(int i = x; i; i -= lowbit(i)) ans += tr[i];
    return ans;
}

void solve(){
    cin >> n;
    vi a(n), odd, even;
    for(int i = 0; i < n; i++){
        cin >> a[i];
        if(i & 1) even.emplace_back(a[i]);
        else odd.emplace_back(a[i]);
    }
    
    auto acc = [&](vi vec){
        int sz = vec.size();
        if(sz <= 1) return 0ll;
        vi tmp = vec;
        sort(tmp.begin(), tmp.end());
        for(int &x : vec)
            x = lower_bound(tmp.begin(), tmp.end(), x) - tmp.begin() + 1;
        for(int i = 1; i <= sz; i++) tr[i] = 0;
        int ans = 0;
        for(int i = sz - 1; i >= 0; i--){
            ans += sum(vec[i] - 1);
            add(vec[i], 1);
        }
        return ans;
    };
    
    int ans1 = acc(odd), ans2 = acc(even);
    sort(odd.begin(), odd.end());
    sort(even.begin(), even.end());
    vi res(n);
    for(int i = 0; i < odd.size(); i++)
        res[2 * i] = odd[i];
    for(int i = 0; i < even.size(); i++)
        res[2 * i + 1] = even[i];
    if((ans1 + ans2) & 1) swap(res[n - 3], res[n - 1]);
    for(int i = 0; i < n; i++)
        cout << res[i] << " \n"[i == n - 1];
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
