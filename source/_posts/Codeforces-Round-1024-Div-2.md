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
#define vi vector<int>

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
