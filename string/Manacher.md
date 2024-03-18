#! https://zhuanlan.zhihu.com/p/687703509
# Manacher

常用于 求 字符串最长 回文串

**中心拓展法**

给出一段 暴力代码

其中 `p` 数组 代表的是   以当前位置作为 回文串中心 所能拓展的 最长长度

```C++
int n, m;
void solve() {
  string s;
  cin >> s;
  n = s.size();
  vector<int> p(n);
  for (int i = 0; i < n; i++)
    for (int l = i, r = i; l >= 0 and r < n and s[l] == s[r]; l--, r++)
      p[i] = (r - l + 1) / 2;

  for (int i = 0; i < n; i++) cout << p[i] << " ";
  cout << "\n";
  for (int i = 0; i < n; i++) cout << s[i] << " ";
}
```

顾名思义就是选中 回文中心位置 暴力向两边拓展

但我们发现是 不对的

因为有一类回文串 的长度是 偶数 ，他的 回文中心 落在两个字母之间

那么我们可以在 两个字母之间插入一个 无关字符 进行  中心拓展

```C++
int n, m;
void solve() {
  string s;
  cin >> s;
  string tmp;
  for (auto it : s) {
    tmp += '$';
    tmp += it;
  }
  swap(tmp, s);
  n = s.size();
  vector<int> p(n);
  for (int i = 0; i < n; i++)
    for (int l = i, r = i; l >= 0 and r < n and s[l] == s[r]; l--, r++)
      p[i] = (r - l + 1) / 2;

  for (int i = 0; i < n; i++) cout << p[i] << " ";
  cout << "\n";
  for (int i = 0; i < n; i++) cout << s[i] << " ";
}
```

现在就是挺对的 中心拓展法了

但是这是 $O(n^2)$  的  算法 

​                                                                                                                                                                                                                                                                                                                            **Manacher ** 提供了 一种 $O(n)$ 的算法

事实上 回文串有 一种性质

回文串的镜像串 也是 回文串**(性质 1)**

我们假设现在计算到 $i$ ，$[0,i-1]$ 以内的 `p`  都已经计算完毕

设 $R$ 为 之前的回文串 中最大的 右端点

对于 $P[C]$ $=C+R$



我们继续计算 $P$ 数组
$$
\begin{align}
&设j 是 i 关于 C的 对称点\\                       
&i\ge R\quad  暴力计算并 拓展R\\     
&i<R  \quad\\
&如果 回文串 j 落在 C 中,那么根据性质1,回文串 i可以立即确定长度\\
&P[i]=P[j]=P[2C-i]\\
&如果 回文串 j 为落在 C 中,那么我们暴力拓展 R\\
&如果 回文串j 的长度 是超出 C的,那么我们先取到端点再继续暴力拓展
\end{align}
$$
$R$最多只会拓展 $n$ 次 所以这个算法的时间复杂度是 $O(n)$ 的

**code**

```C++
void solve() {
  string s;
  cin >> s;
  string tmp;
  tmp += '$';
  tmp += '#';
  for (auto it : s) {
    tmp += it;
    tmp += '#';
  }
  tmp += '&';
  swap(tmp, s);
  n = s.size();
  vector<int> p(n);
  int R = 0, C;
  for (int i = 1; i < n; i++) {
    if (i < R) {
      p[i] = min(p[(C << 1) - i], R - i + 1);
    } else
      p[i] = 1;
    while (s[i + p[i]] == s[i - p[i]]) p[i]++;
    if (p[i] + i - 1 > R) {
      C = i;
      R = p[i] + i - 1;
    }
  }
  cout << *max_element(all(p)) - 1LL << "\n";
}
```

