# 启发式合并

**dsu on tree 对于某些树上离线问题可以速度大于等于大部分算法且更易于理解和实现的算法。**

**从一道简单一些的题目引入**

**[E-多重映射_牛客小白月赛88](https://ac.nowcoder.com/acm/contest/75771/E)**

已知一个由数字构成的序列 $A$ ，不妨定义一个函数 $M(x)=y$ 表示将序列 $A$ 中全部的数字 $x$ 都替换为 $y$ 。例如 $A=\{2,2,4,5,1,4\}$ ，执行一次 $M(2)=1$ 操作后，序列变为 $A'=\{1,1,4,5,1,4\}$ ，很神奇吧！

这样的替换显然是能够进行反复叠加的，举例说明，对于上方操作过后的 $A'$ 再执行一次 $M(1)=2$ 操作，序列会变为 $A''=\{2,2,4,5,2,4\}$ 。  
现在，给出若干个这样的操作，请你输出修改过后的序列。

首先给出这道题的**正解(std)**

我们对操作进行倒着分析，然后就是维护所有数所在的并查集，然后就是并查集模板题了

```C++
void solve() {
  cin >> n >> m;
  vector<int> a(n), l(m), r(m);
  for (auto &i : a) cin >> i;
  map<int, int> mp;
  for (auto it : a) mp[it] = it;
  for (int i = 0; i < m; i++) {
    cin >> l[i] >> r[i];
    if (not mp.count(l[i])) mp[l[i]] = l[i];
    if (not mp.count(r[i])) mp[r[i]] = r[i];
  }
  for (int i = m - 1; ~i; i--) mp[l[i]] = mp[r[i]];
  for (auto it : a) cout << mp[it] << " ";
  cout << "\n";
}
```

但我们主要讲解另一种正着做的做法

我们设想每次修改都按照题意对 每个数所在的集合进行合并 是不是很好维护

但是 我们每次暴力合并时间复杂度会卡到$O(nm)$

这显然是不可接受的

为什么呢，这个时间复杂度怎么计算呢

假设 所有的 位置都是相同的 数字

然后 每次对 所有的数字进行操作 ，那么 每次操作的时间复杂度是 $O(n)$的

一共有 $m$ 次 操作 ，那么总的时间复杂度就是 $O(nm)$的

但是有个东西叫做启发式合并

他的道理就是 每次都把 个数较小的集合  暴力合并到  个数较大 的集合中

 这样每次进行操作的 数字 所在的集合大小 至少会 扩增到 $2$ 倍

也就是每个数 最多进行 $logn$次 操作 

那么总的所有元素 进行操作的次数 就是 $nlogn$ 次

那么 总的 时间复杂度 就规约到 $O(nlogn)$ 了

然后就可以优雅的暴力通过这道题了

```C++
constexpr int M = 1E6 + 10;
vector<int> id[M];
void solve() {
  cin >> n >> m;
  set<int> S;
  vector<int> a(n);
  for (auto &i : a) cin >> i;
  for (int i = 0; i < n; i++) {
    id[a[i]].emplace_back(i);
    S.emplace(a[i]);
  }
  for (int i = 0; i < m; i++) {
    int l, r;
    cin >> l >> r;
    if (l != r) {
      if (id[l].size() > id[r].size()) swap(id[l], id[r]);
      id[r].insert(end(id[r]), all(id[l]));
      id[l].clear();
      S.emplace(r);
    }
  }
  for (auto it : S) {
    for (auto i : id[it]) a[i] = it;
    id[it].clear();
  }
  for (int i = 0; i < n; i++) cout << a[i] << " ";
  cout << "\n";
}
```

**dsu on  tree**

[U41492 树上数颜色](https://www.luogu.com.cn/problem/U41492)

[Problem - E - Codeforces](https://codeforces.com/contest/600/problem/E)

[Problem - D - Codeforces](https://codeforces.com/gym/105013/problem/D)