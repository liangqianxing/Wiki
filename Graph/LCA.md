[toc]

# LCA

LCA一般指树上的最近公共祖先







## **倍增**









**code**

```C++
// Virtual Judge - HDU  How far away ？
// https://vjudge.net/problem/HDU-2586 2024-03-05 13:26:28

#include <bits/stdc++.h>
using namespace std;
#define all(a) begin(a), end(a)
#define int long long
int n, m;
vector<vector<int>> v, w;
constexpr int N = 4E4 + 10;
int dep[N];
int fa[N][32], cost[N][32];
void dfs(int u, int fth) {
  fa[u][0] = fth;
  dep[u] = dep[fth] + 1;
  for (int i = 1; i < 31; i++) {
    fa[u][i] = fa[fa[u][i - 1]][i - 1];
    cost[u][i] = cost[fa[u][i - 1]][i - 1] + cost[u][i - 1];
  }
  int sz = v[u].size();
  for (int i = 0; i < sz; i++) {
    if (v[u][i] == fth) continue;
    cost[v[u][i]][0] = w[u][i];
    dfs(v[u][i], u);
  }
}
int lca(int x, int y) {
  if (dep[x] > dep[y]) swap(x, y);
  int tmp = dep[y] - dep[x], ans = 0;
  for (int j = 0; tmp; ++j, tmp >>= 1)
    if (tmp & 1) ans += cost[y][j], y = fa[y][j];
  if (y == x)
    return ans;
  else {
    for (int j = 30; ~j and y != x; --j) {
      if (fa[x][j] != fa[y][j]) {
        ans += cost[x][j] + cost[y][j];
        x = fa[x][j], y = fa[y][j];
      }
    }
    ans += cost[x][0] + cost[y][0];
    return ans;
  }
}
void solve() {
  cin >> n >> m;
  v.resize(n + 1), w.resize(n + 1);
  for (int i = 0; i <= n; i++) dep[i] = 0, v.clear(), w.clear();
  for (int i = 1; i < n; i++) {
    int a, b, c;
    cin >> a >> b >> c;
    v[a].emplace_back(b);
    v[b].emplace_back(a);
    w[a].emplace_back(c);
    w[b].emplace_back(c);
  }
  dfs(1, 0);
  for (int i = 1; i <= m; i++) {
    int x, y;
    cin >> x >> y;
    cout << lca(x, y) << "\n";
  }
}

signed main() {
  ios::sync_with_stdio(false);
  cin.tie(0), cout.tie(0);
  int __;
  cin >> __;
  while (__--) solve();
  return 0;
}
```

