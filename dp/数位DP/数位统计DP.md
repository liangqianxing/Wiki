# 数位统计DP

数位统计DP 用于数字的数位统计

一般区间统计我们会拆成两个前缀区间的差值来简化计算

比如答案求 $[a,b]$ 之间的计数，我们会拆成$[1,b]$ - $[1,a-1]$

数位统计DP 一般有两种实现

1. 递推
2. 记忆化搜索

这里不对第一种方法进行 学习， 我更喜欢第二种方式

**记忆化搜索实现**

数位统计在实现的时候比较难的地方就是前导零的处理与临界位的判断

如果设置正确的状态则会事半功倍

# [ZJOI2010 数字计数](https://www.luogu.com.cn/problem/P2602)



给定两个正整数 $a$ 和 $b$，求在 $[a,b]$ 中的所有整数中，每个数码(digit)各出现了多少次。

仅包含一行两个整数 $a,b$，含义如上所述。

包含一行十个整数，分别表示 $0\sim 9$ 在 $[a,b]$ 中出现了多少次。

- 对于 $30\%$ 的数据，保证 $a\le b\le10^6$；
- 对于 $100\%$ 的数据，保证 $1\le a\le b\le 10^{12}$。

我们设置 $dp[pos][sum][lead][limit]$ 为状态

其中 
$$
\begin{align}
pos&:范围是[0\dots0,99\dots9] (pos个0，pos个9)\\
sum&:前面已经确定的位置有sum 个2\\
lead&:是否有前导零\\
limit&:最高位数位限制
\end{align}
$$


由此便可表示出所有情况

**code**

```C++
// Luogu P2602 [ZJOI2010] 数字计数
// https://www.luogu.com.cn/problem/P2602 2024-03-19 00:33:51

#include <bits/stdc++.h>
using namespace std;
#define all(a) begin(a), end(a)
#define int long long
int n, m;
constexpr int N = 15;
int dp[N][N][2][2], num[N], now;
int dfs(int pos, int sum, bool lead, bool limit) {
  int ans = 0;
  if (not pos) return sum;
  if (dp[pos][sum][lead][limit] != -1) return dp[pos][sum][lead][limit];
  int up = (limit ? num[pos] : 9);
  for (int i = 0; i <= up; i++) {
    if (i == 0 and lead)
      ans += dfs(pos - 1, sum, true, limit and i == up);
    else if (i == now)
      ans += dfs(pos - 1, sum + 1, false, limit and i == up);
    else if (i != now)
      ans += dfs(pos - 1, sum, false, limit and i == up);
  }
  dp[pos][sum][lead][limit] = ans;
  return ans;
}
int sol(int x) {
  int len = 0;
  while (x) {
    num[++len] = x % 10;
    x /= 10;
  }
  memset(dp, -1, sizeof dp);
  return dfs(len, 0, true, true);
}
void solve() {
  cin >> n >> m;
  for (int i = 0; i < 10; i++) {
    now = i;
    cout << sol(m) - sol(n - 1) << " ";
  }
}

signed main() {
  ios::sync_with_stdio(false);
  cin.tie(0), cout.tie(0);
  solve();
  return 0;
}
```

## [2282 -- The Counting Problem (poj.org)](http://poj.org/problem?id=2282)

```c++
// POJ - Shanghai 2004 The Counting Problem
// http://poj.org/problem?id=2282 2024-03-19 00:42:35

#include <cstring>
#include <iostream>
using namespace std;
#define int long long
int n, m;
const int N = 15;
int dp[N][N][2][2], num[N], now;
int dfs(int pos, int sum, bool lead, bool limit) {
  int ans = 0;
  if (not pos) return sum;
  if (dp[pos][sum][lead][limit] != -1) return dp[pos][sum][lead][limit];
  int up = (limit ? num[pos] : 9);
  for (int i = 0; i <= up; i++) {
    if (i == 0 and lead)
      ans += dfs(pos - 1, sum, true, limit and i == up);
    else if (i == now)
      ans += dfs(pos - 1, sum + 1, false, limit and i == up);
    else if (i != now)
      ans += dfs(pos - 1, sum, false, limit and i == up);
  }
  dp[pos][sum][lead][limit] = ans;
  return ans;
}
int sol(int x) {
  int len = 0;
  while (x) {
    num[++len] = x % 10;
    x /= 10;
  }
  memset(dp, -1, sizeof dp);
  return dfs(len, 0, true, true);
}
void solve() {
  while (cin >> n >> m, n and m) {
    if (n > m) swap(n, m);
    for (int i = 0; i < 10; i++) {
      now = i;
      cout << sol(m) - sol(n - 1) << " ";
    }
    cout << "\n";
  }
}

signed main() {
  ios::sync_with_stdio(false);
  cin.tie(0), cout.tie(0);
  solve();
  return 0;
}
```

## [L-L2-4(250pts)_新疆大学线下训练赛&程序设计天梯赛选拔赛 (nowcoder.com)](https://ac.nowcoder.com/acm/contest/76743/L)

BLUESKY007有很多关系很好的朋友,他们无一例外,名字均由数字组成(首字符不为0)且含有"007"(例如“10007”,“10707”就是她的好朋友,而“97037”,“70709”不是),即对于字符串s存在i,j,k(i< j< k)满足$\overline{s_is_js_k}=\overline{007}$.  
虽然BLUESKY007眼力极佳,一眼就能看出一个人是不是自己的好朋友,但BLUESKY007是个蒟蒻,她并不擅长数数,但她又想知道在\[li,ri\]内有多少人是自己的好朋友,所以就找到了你来帮忙.  
她会向你询问t次,由于询问次数可能很多,所以你只需要告诉她t次询问答案的异或和即可.
$$
\begin{align}
&对于20\%的数据,l_i≤r_i≤10^3  \\
&对于40\%的数据,t≤50,l_i≤r_i≤5·10^4  \\
&对于100\%的数据,1≤t≤10^5,0≤l_i≤r_i≤10^18\\
\end{align}
$$
**code**

```C++
// NowCoder L2-4(250pts)
// https://ac.nowcoder.com/acm/contest/76743/L 2024-03-19 00:53:37

#include <bits/stdc++.h>
using namespace std;
#define all(a) begin(a), end(a)
#define int long long
int n, m;
constexpr int N = 20;
int dp[N][N][2][2][2];
int num[N];
int dfs(int pos, int num0, bool ack, bool lead, bool limit) {
  int ans = 0;
  if (not pos) return ack;
  if (dp[pos][num0][ack][lead][limit] != -1)
    return dp[pos][num0][ack][lead][limit];
  int up = (limit ? num[pos] : 9);
  for (int i = 0; i <= up; i++) {
    ans += dfs(pos - 1, num0 + (!i and !lead), ack or (num0 >= 2 and i == 7),
               lead and (!i), limit and i == up);
  }
  return dp[pos][num0][ack][lead][limit] = ans;
}
int sol(int x) {
  int len = 0;
  while (x) {
    num[++len] = x % 10;
    x /= 10;
  }
  memset(dp, -1, sizeof dp);
  return dfs(len, 0, 0, true, true);
}
int solve() {
  cin >> n >> m;
  if (n > m) swap(n, m);
  return sol(m) - sol(n - 1);
}

signed main() {
  ios::sync_with_stdio(false);
  cin.tie(0), cout.tie(0);
  int ans = 0;
  int __;
  cin >> __;
  while (__--) ans ^= solve();
  cout << ans << "\n";
  return 0;
}
```

