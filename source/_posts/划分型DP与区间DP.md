---
title: 划分型DP与区间DP 
date: 2025-04-21 20:39:41
tags:
    - DP
    - 划分型DP
    - 区间DP
categories:
    - 算法笔记
mathjax: true
---



## 1. 划分型 DP（字符串分割 DP）

### 特点

- **状态定义**：  
    常用的做法是定义一个状态数组，如 `dp[i]` 表示字符串前 `i` 个字符的最优状态或统计信息（例如分割方案数、最少子串数、最大子串长度等）。
    
- **转移思路**：  
    我们枚举一个位置 `i` 作为上一个分割终点，然后枚举下一个合法子串的结束位置 `j`，这样就将前`i` 个字符的解转移给前 `j` 个字符的解。  
    核心在于对合法子串的判定（本题中需要保证子串中每个字符出现的长度不超过对应限制）。

### 处理方式

1. **初始化**：  
    通常把空串设为初始状态，例如 `dp[0] = 1`（方案数）、`dp[0] = 0`（最小段数）等。
    
2. **转移**：  
    对于每个分割位置 `i`，在`i+1` 到 `n` 内枚举能否构成一个合法子串；如果可以，则更新 `dp[j+1]`（或其他状态数组），累加或更新状态值。  
    判断合法子串时往往会枚举区间内的字符，维护一个约束条件（例如本题中，维护当前区间所有字符允许的最大子串长度的最小值）。
    
3. **时间复杂度**：  
    一般会是 $\mathcal{O}(n^2)$，适用于 n 较小的问题（例如 $n \le 10^3$）。
    
---

## 2. 区间 DP

### 特点

- **状态定义**：  
    区间 DP 通常会定义状态 `dp[l][r]` 来表示字符串中区间 `[l,r]` 的某种解，如最小代价、最大值或是否满足某种性质等。
    
- **转移思路**：  
    常见的做法是将一个区间分成两部分，比如枚举一个中间位置 `k` ，然后利用 `dp[l][k]` 和 `dp[k+1][r]` 的状态来更新 `dp[l][r]`。  
    典型题目有矩阵链乘法、回文划分、二叉搜索树的最优二叉树等问题。
    
- **处理方式**：  
    往往需要双重甚至三重循环，遍历各个区间的长度，再枚举区间内的分割点。
    

### 区别

- **状态维度**：  
    划分型 DP 通常是一维的（只关注前缀），而区间 DP 是二维的（每个状态涉及区间的左、右界）。
    
- **组合方式**：  
    划分 DP 主要是对前缀进行划分，然后“连接”到后面的子串；而区间 DP 则强调区间内子问题的合并，通常需要枚举分割点来组合两个子区间的结果。
    

---

## 如何判断用哪种 DP？

1. **看状态定义**
    
    - 如果问题问的是“前 i 个元素的最优解”、“方案数”等（即从头到尾顺序处理），通常使用划分型 DP（也叫线性 DP）。
    - 如果问题问的是“区间 `[i,j]` 内的性质”、“区间合并”、“括号匹配”、“翻转字符串”等，需要同时考虑区间的两个端点，那么更可能用区间 DP。
    - 判断是否是**区间 DP**，关键在于看状态是否**定义在区间上**（比如`dp[l][r]`），以及是否需要**枚举区间内的分割点合并左右子区间的答案**。
	- **划分型 DP**则通常只需要**关注前缀的状态，用一个一维 dp 数组即可完成求解**。
2. **看转移方式**
    
    - 划分型 DP：状态一般只依赖于前面已经处理好的位置。例如 `dp[i]` 往后转移 `dp[j]` `（j>i）`。
        
    - 区间 DP：状态依赖于区间内部的划分，通常需要枚举一个中间位置 k ，状态为 `dp[l][r] = combine(dp[l][k], dp[k+1][r])`。
        
3. **问题描述**
    
    - 对字符串的“分割”、“划分”、“切割”问题（比如分割成若干子串满足一定条件）通常用划分型 DP。
        
    - 如要求“括号匹配”、“区间最优值”这类问题则偏向区间 DP。
        

---

## 区间DP

#### [CF149D. Coloring Brackets](https://codeforces.com/problemset/problem/149/d)(区间DP)(1900)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
const int N = 710, mod = 1e9 + 7;
int n, dp[N][N][3][3], match[N], ans;
string s;
stack<int> st;

void dfs(int l, int r){
    if(l >= r) return;
    if(l + 1 == r) dp[l][r][0][1] = dp[l][r][0][2] = dp[l][r][1][0] = dp[l][r][2][0] = 1;
    else if(match[l] == r){
        dfs(l + 1, r - 1);
        for(int i = 0; i <= 2; i++){
            for(int j =0; j <= 2; j++){
                if(j != 1) dp[l][r][0][1] = (dp[l][r][0][1] + dp[l + 1][r - 1][i][j]) % mod;
                if(j != 2) dp[l][r][0][2] = (dp[l][r][0][2] + dp[l + 1][r - 1][i][j]) % mod;
                if(i != 1) dp[l][r][1][0] = (dp[l][r][1][0] + dp[l + 1][r - 1][i][j]) % mod;
                if(i != 2) dp[l][r][2][0] = (dp[l][r][2][0] + dp[l + 1][r - 1][i][j]) % mod;
            }
        }
    }else{
        dfs(l, match[l]), dfs(match[l] + 1, r);
        for(int i = 0; i <= 2; i++)
            for(int j = 0; j <= 2; j++)
                for(int p = 0; p <= 2; p++)
                    for(int q = 0; q <= 2; q++){
                        if((j == 1 && p == 1) || (j == 2 && p == 2)) continue;
                        dp[l][r][i][q] = (dp[l][r][i][q] + (dp[l][match[l]][i][j] * dp[match[l] + 1][r][p][q]) % mod) % mod;
                    }
    }
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    cin >> s;
    n = s.size();
    s = ' ' + s;
    for(int i = 1; i <= n; i++){
        if(s[i] == '(') st.push(i);
        else match[st.top()] = i, match[i] = st.top(), st.pop();
    }
    dfs(1, n);
    for(int i = 0; i <= 2; i++)
        for(int j = 0; j <= 2; j++)
            ans = (ans + dp[1][n][i][j]) % mod;
    cout << ans << '\n';
    return 0;
}
```


## 划分型DP

#### [CF766C. Mahmoud and a Message](https://codeforces.com/problemset/problem/766/C)(划分型DP)(字符串分割DP)(1700)
**[解析](https://github.com/Yawn-Sean/Daily_CF_Problems/blob/main/daily_problems/2025/04/0402/solution/cf766c.md)**
**提示 1：** 识别出这个问题到底是什么类别的题目。
**提示 2：** 题目中的三小问其实就只是三种不同的初值和三种不同的转移。
将一个序列顺序拆分为若干个，这类问题常被称为 **划分型DP**。
简单来说，假设第 $i$ 个位置分了一段，我们其实不在乎前面的段落具体是怎么分的，我们只在乎前面的段落的方案数 / 最大段的长度的最大值 / 分割段落数的最小值。
因此可以设计 $dp[i]$ 表示上面的变量。
接下来从 $i$ 状态开始转移。相当于枚举 $j$ 看哪些 $j$ 是合法的。我们可以从左到右遍历 $i+1$ 开始的各个位置，在遍历的过程中可以顺便根据新遍历的字符，更新当前段落的最大长度不能超过多少。而一旦超过了这个上限，直接退出 $j$ 的循环即可。
我们知道了合法的转移位置就只需再知道转移的方程就行。
对于计算方案数，转移方程就是直接相加。
对于最大段长度的最大值，考虑从 $i$ 位置转移到 $j$ 位置的。则 $i$ 位置之前的段落最大长度为 $dp[i]$ ， $i+1\to j$ 又产生了一个长度为 $j-i$ 的，所以从 $i$ 转移过来的最大长度为 $\max(dp[i],j-i)$ ，再和 $j$ 的当前结果取最大值即可。(下方代码和这里的状态转移有略微不同)
对于分割段落数的最小值，从 $i$ 位置转移过来的是 $dp[i]+1$ ，与 $j$ 当前结果取最小值即可。
时间复杂度为 $\mathcal{O}(n^2)$ 。
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
int n, dp1[N], dp2[N], dp3[N], mx[30];
string s;
// dp1[i]: 到第 i 个字符为止的所有子串数量
// dp2[i]: 到第 i 个字符为止的最长子串长度
// dp3[i]: 到第 i 个字符为止的最小子串数

void solve(){
    cin >> n >> s;
    s = " " + s;
    for(int i = 1; i <= 26; i++) cin >> mx[i];
    dp1[0] = 1; // 空子串数量为 1 
    for(int i = 0; i <= n; i++) dp3[i] = i;
    for(int i = 1; i <= n; i++){
        int ma = inf;
        for(int j = i; j <= n; j++){
            ma = min(ma, mx[s[j] - 'a' + 1]);
            if(j - i + 1 > ma) break;
            dp1[j] = (dp1[j] + dp1[i - 1]) % mod;
            dp2[j] = max({dp2[j], dp2[i - 1], j - i + 1});
            dp3[j] = min(dp3[j], dp3[i - 1] + 1);
        }
    }
    cout << dp1[n] << endl << dp2[n] << endl << dp3[n] << endl;
}

signed main(){
    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int T = 1;
    //cin >> T;
    while(T--) solve();
    return 0;
}
```