---
title: Codeforces Round 1019 (Div. 2)
date: 2025-05-05 16:47:22
tags:
  - Codeforces
categories:
  - 比赛
---
## [CF2103A. Common Multiple](https://codeforces.com/contest/2103/problem/A)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
int n;

void solve(){
    cin >> n;
    set<int> st;
    for(int i = 1; i <= n; i++){
        int x; cin >> x;
        st.insert(x);
    }
    cout << st.size() << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF2103B. Binary Typewriter](https://codeforces.com/contest/2103/problem/B)(贪心)
**只有手指的移动次数(cnt)** 可以通过反转来**减少**
显然, 在最理想的情况下, 反转一次 对于答案的贡献为 `-2`
**分类讨论**, 考虑手指移动次数对于答案的影响:
- `cnt = 0` 形如 `0000000`，`n`即为答案
- `cnt = 1` 形如 `0011111`，翻转不会减少操作次数，所以答案为 `n + cnt`
- `cnt = 2` 形如 `00111000`，可以通过翻转形成` 00000111`，`手指移动次数 - 1`, 所以答案为 `n + cnt - 1`
- `cnt > 2` 形如 `00111000111`，可以通过翻转形成` 00000111111`，`手指移动次数 - 2`, 所以答案为 `n + cnt - 2`
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
int n, cnt;
string s;

void solve(){
    cin >> n >> s;
    s = "0" + s;
    cnt = 0;
    for(int i = 1; i < s.size(); i++)
        if(s[i] != s[i - 1]) cnt++;
    if(cnt == 2) cnt--;
    else if(cnt > 2) cnt -= 2;
    cout << n + cnt << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [CF2103C. Median Splits](https://codeforces.com/contest/2103/problem/C)
**题意转化**: 把`数组a`分成 左、中、右 三部分，只需其中两部分的`median <= k`即可
对于数组中的元素，进行映射:
- 如果`a[i] <= k` ，则`a[i] = 1`
- 如果`a[i] > k` ，则`a[i] = -1`
那么可以知道，`数组的区间和 >= 0` 等价于 `这段区间的 median <= k`
只需要分三种情况讨论：
- 左、中区间满足 `median <= k`
- 左、右区间满足 `median <= k`
- 中、右区间满足 `median <= k`
其中 第一种情况 和 第三种情况 类似，只需反转整个数组再求一次即可
第二种情况讨论也比较简单
对于第一种情况和第三种情况的判断，可以观看[视频](https://www.bilibili.com/video/BV1oe5dzqEbp?spm_id_from=333.788.videopod.sections&vd_source=f0489718ccab992000c983a006bde4a5&p=3)理解数形结合的方法
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
int n, k, l, r, cur;

void solve(){
    cin >> n >> k;
    vi a(n);
    for(auto &x : a) cin >> x, x = (x <= k ? 1 : -1);
    
	// 判断左、右区间
    l = 1, cur = a[0];
    while(l < n && cur < 0) cur += a[l++];
    r = n - 2, cur = a[n - 1];
    while(r >= 0 && cur < 0) cur += a[r--];
    // 如果两个切点 l 和 r 没碰头，说明：
	// [0..l−1]、[l..r]、[r+1..n−1] 三段里：
	// 第一段和第三段 区间和 ≥ 0（即 median ≤ k），
	// 中间剩下一段至少长度 1
    if(l <= r) return void(cout << "YES" << endl);

	// 判断其他两种情况
    auto check = [&](vi &a) -> bool{
        cur = 0;
        set<int> st;
        for(int i = 0; i < n - 1; i++){
            cur += a[i];
            if(cur < 0) continue;
            if(cur > 1 ||
                cur == 0 && st.count(0) ||
                cur && !st.empty()) return true;
            st.insert(cur);
        }
        return false;
    };

    vi b(a.rbegin(), a.rend());
    cout << (check(a) || check(b) ? "YES" : "NO") << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```