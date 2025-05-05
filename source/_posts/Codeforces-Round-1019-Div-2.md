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