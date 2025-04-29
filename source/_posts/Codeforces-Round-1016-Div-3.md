---
title: Codeforces Round 1016 (Div. 3)
date: 2025-04-29 18:12:22
tags:
  - Codeforces
categories:
  - 比赛
---
## [A. Ideal Generator](https://codeforces.com/contest/2093/problem/A)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 1e3 + 10, M = 1e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int x;

void solve(){
    cin >> x;
    cout << (x & 1 ? "YES" : "NO") << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [B. Expensive Number](https://codeforces.com/contest/2093/problem/B)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 2e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int x, y;
string s;

void solve(){
    cin >> s;
    bool flag = false;
    x = y = 0;
	// 最后一个非0数之前非0数的数量 + 非0数之后0的数量
    for(int i = s.size() - 1; i >= 0; i--){
        if(!flag && s[i] == '0') y++;
        if(s[i] != '0'){
            flag = true;
            x++;
        }
    }
    cout << x + y - 1 << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [C. Simple Repetition](https://codeforces.com/contest/2093/problem/C)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 2e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int x, k;

void solve(){
    cin >> x >> k;
    if(k > 1 && x != 1) return void(cout << "No" << endl);
    if(x == 1) for(int i = 1; i < k; i++) x = 10 * x + 1;
    if(x <= 1) return void(cout << "No" << endl);
    for(int i = 2; i <= x / i; i++)
        if(x % i == 0) return void(cout << "No" << endl);
    cout << "Yes" << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [D. Skibidi Table](https://codeforces.com/contest/2093/problem/D)(递归，模拟)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 2e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, q, x, y, d;
string op;

int solve(int n, int x, int y){
    if(n == 1){
        if(x == 1 && y == 1) return 1;
        else if(x == 2 && y == 2) return 2;
        else if(x == 2 && y == 1) return 3;
        else return 4;
    }
    int half = 1ll << (n - 1), block = half * half;
    if(x <= half && y <= half) return solve(n - 1, x, y);
    else if(x > half && y > half) return block + solve(n - 1, x - half, y - half);
    else if(x > half && y <= half) return 2 * block + solve(n - 1, x - half, y);
    else return 3 * block + solve(n - 1, x, y - half);
}

pii solve(int n, int d){
    if(n == 1){
        if(d == 1) return {1, 1};
        else if (d == 2) return {2, 2};
        else if(d == 3) return {2, 1};
        else return {1, 2};
    }
    int half = 1ll << (n - 1), block = half * half;
    if(d <= block){
        auto[x, y] = solve(n - 1, d);
        return {x, y};
    }else if(d <= 2 * block){
        auto[x, y] = solve(n - 1, d - block);
        return {x + half, y + half};
    }else if(d <= 3 * block){
        auto[x, y] = solve(n - 1, d - 2 * block);
        return {x + half, y};
    }else{
        auto[x, y] = solve(n - 1, d - 3 * block);
        return {x, y + half};
    }
}

void solve(){
    cin >> n >> q;
    while(q--){
        cin >> op;
        if(op == "->"){
            cin >> x >> y;
            cout << solve(n, x, y) << endl;
        }else if(op == "<-"){
            cin >> d;
            auto[x, y] = solve(n, d);
            cout << x << ' ' << y << endl;
        }
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
## [E. Min Max MEX](https://codeforces.com/contest/2093/problem/E)(二分答案)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
const int N = 2e5 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, k, a[N];

bool check(int mid){
    int mex = 0, cnt = 0;
    priority_queue<int, vi, greater<int>> pq;
    for(int i = 1; i <= n; i++){
        if(a[i] < mex) continue;
        pq.push(a[i]);
        while(!pq.empty()){
            int tmp = pq.top();
            if(tmp > mex) break;
            if(tmp == mex) mex++;
            pq.pop();
        }
        if(mex >= mid){
            cnt++;
            mex = 0;
            while(!pq.empty()) pq.pop();
        }
    }
    return cnt >= k;
}

void solve(){
    cin >> n >> k;
    for(int i = 1; i <= n; i++) cin >> a[i];
    int l = 0, r = n + 1;
    while(l < r){
        int mid = (l + r + 1) >> 1;
        if(check(mid)) l = mid;
        else r = mid - 1;
    }
    cout << l << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [F. Hackers and Neural Networks](https://codeforces.com/problemset/problem/2093/F)(贪心)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define pii pair<int, int>
const int N = 500 + 10, M = 2e5 + 10, inf = 0x3f3f3f3f, mod = 1e9 + 7;
int n, m, cnt[N], mx;
string s, a[N];
bool flag[N];

void solve(){
    cin >> m >> n;
    mx = 0;
    memset(flag, 0, sizeof flag);
    memset(cnt, 0, sizeof cnt);
    for(int i = 1; i <= m; i++) cin >> a[i];
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++){
            cin >> s;
            if(a[j] == s) flag[j] = 1, cnt[i]++;
        }
        mx = max(mx, cnt[i]);
    }
    for(int i = 1; i <= m; i++)
        if(!flag[i]) return void(cout << -1 << endl);
    cout << m + (m - mx) * 2 << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
## [G. Shorten the Array](https://codeforces.com/contest/2093/problem/g)(01Trie)(需要复习！)
**看到𝑏𝑖 ⊕ 𝑏𝑗 >= k 就要想到 01Trie**
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long
#define vi vector<int>
#define rep(i, a, b) for (int i = (a); i <= (b); i++)
#define per(i, a, b) for (int i = (a); i >= (b); i--)
const int LOG_X = 29; // 用于将整数看成 30 位二进制数处理
int n, k, x;

// Trie 树的节点结构，每个节点有两个孩子表示二进制的 0 和 1，以及记录最大索引的 last
struct node{
    int child[2] = {-1, -1}; // 左右孩子，分别表示 0 和 1
    int last = -1; // 记录这个节点子树中插入过的最大下标
};
vector<node> trie; // Trie 树

// 查找当前值 value 是否存在之前插入的值 y，使得 value^y >= k
int find(int value, int border){
    int res = -1, cur = 0; // 当前节点位置
    bool ok = true;
    for(int pos = LOG_X; ok && pos >= 0; pos--){ // 从高位往低位匹配
        int x_bit = (value >> pos) & 1; // 当前位 value 的值
        int k_bit = (border >> pos) & 1; // 当前位 k 的值
        const int* child = trie[cur].child;
        if(k_bit == 1){
            // 想让 x^y 在这一位为 1（即大于等于 k），就必须往相反的边走
            if(child[x_bit ^ 1] != -1) cur = child[x_bit ^ 1];
            else ok = false;
        }else{
            // k_bit == 0，可以记录另一个方向是否满足大于等于要求
            if(child[x_bit ^ 1] != -1)
                res = max(res, trie[child[x_bit ^ 1]].last);
            if (child[x_bit] != -1) cur = child[x_bit]; // 优先继续匹配相同位
            else ok = false;
        }
    }
    if(ok) res = max(res, trie[cur].last); // 最后节点也可能有合法结果
    return res;
}

// 向 Trie 中插入一个值，并更新路径上所有节点的 last 值
void add(int value, int index) {
    int cur = 0;
    trie[cur].last = max(trie[cur].last, index);
    for(int pos = LOG_X; pos >= 0; pos--){
        int x_bit = (value >> pos) & 1;
        if(trie[cur].child[x_bit] == -1){
            trie[cur].child[x_bit] = trie.size(); // 创建新节点
            trie.push_back(node()); // 新节点默认初始化
        }
        cur = trie[cur].child[x_bit]; // 往下走
        trie[cur].last = max(trie[cur].last, index); // 更新最大下标
    }
}

void solve(){
    cin >> n >> k;
    int ans = n + 1;
    trie.clear(); // 清空上一次测试用例构建的 trie
    trie.push_back(node()); // 插入根节点
    for(int i = 0; i < n; i++){
        cin >> x;
        add(x, i); // 插入当前值
        int ret = find(x, k); // 查找是否存在 j < i 满足 a[i] ^ a[j] >= k
        if (ret != -1)
            ans = min(ans, (int)(i - ret + 1)); // 更新最短长度
    }
    cout << (ans == n + 1 ? -1 : ans) << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    cin >> T;
    while(T--) solve();
    return 0;
}
```
