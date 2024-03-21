# AC自动机

我们可以将AC自动机看作 `trie`上的 `KMP`

**前置芝士**

**border**

> 在单串中
>
> 如果字符串 *S* 的同长度的前缀和后缀完全相同，即 *Prefix*[*i*] = *Suffix*[*i*]
>
> 则称此前缀（后缀）为一个 Border（根据语境，有时 Border 也指长度）。
>
> **特殊地**，字符串本身也可以是它的 Border，具体是不是根据语境判断。

> 广义border
>
> **推广到两个串**：对于两个串 *S* 和 *T*，相等的 *p* 长度的 *S* 的后缀和 *T* 的
>
> 前缀称为一个 border。
>
> **推广到一个字典**：对于串 *S* 和一个字典 *D*，相等的 *p* 长度的 *S* 的后缀，
>
> 和任意一个字典串 *T* 的前缀称为一个 border。
>
> **失配（Fail）指针：** 对于 Trie 中的每一个节点（即某个字典串的前缀），
>
> 它与 Trie 中所有串的最大 Border 即为失配指针



**正文**

我们需要知道`AC自动机` 是用来干什么的

他是用来 把很多的模式串 在文本串中匹配的



如何构造一个`AC自动机`

1. 构建一个`trie`树
   将所有的模式串都插在树中,并标记末尾

2. 构造`trie`树上的`KMP`
   我们用 `Fail` 指针来实现
   一开始所有的位置的`Fail `指针都是 空的
   我们对于树上的位置一层层的来求
   这样就需要我们使用`BFS`来控制深度

   对于一个节点,如果他父亲的`Fail` 下面有相同的节点,那么 `Fail` 就可以指向 父亲`Fail`的儿子

   如果没有的话,那就让`Fail`置空

3. 查询的时候从跟`root=0`开始查询, 对于查询到的每一个位置 不断跳`Fail` 使得统计所有 广义`border` 



**习题**

[Problem - 2222 (hdu.edu.cn)](https://acm.hdu.edu.cn/showproblem.php?pid=2222)

```C++
// HDOJ Keywords Search
// https://acm.hdu.edu.cn/showproblem.php?pid=2222 2024-03-21 20:34:24

#include <cstring>
#include <iostream>
#include <queue>
#include <vector>
using namespace std;
#define all(a) begin(a), end(a)
int n, m;
const int N = 1E6 + 10;
struct node {
  int son[26];
  int end;
  int fail;
} t[N];
char s[N];
int cnt;
void insert(char *s) {
  int now = 0;
  int _n = strlen(s);
  for (int i = 0; i < _n; i++) {
    int ch = s[i] - 'a';
    if (t[now].son[ch] == 0) t[now].son[ch] = ++cnt;
    now = t[now].son[ch];
  }
  t[now].end++;
}
void getFail() {
  queue<int> q;
  for (int i = 0; i < 26; i++) {
    if (t[0].son[i]) q.emplace(t[0].son[i]);
  }
  while (q.size()) {
    auto now = q.front();
    q.pop();
    for (int i = 0; i < 26; i++) {
      if (t[now].son[i]) {
        t[t[now].son[i]].fail = t[t[now].fail].son[i];
        q.emplace(t[now].son[i]);
      } else {
        t[now].son[i] = t[t[now].fail].son[i];
      }
    }
  }
}
int query(char *s) {
  int ans = 0, now = 0;
  int _n = strlen(s);
  for (int i = 0; i < _n; i++) {
    int ch = s[i] - 'a';
    now = t[now].son[ch];
    int tmp = now;
    while (tmp && t[tmp].end != -1) {
      ans += t[tmp].end;
      t[tmp].end = -1;
      tmp = t[tmp].fail;
    }
  }
  return ans;
}
void solve() {
  memset(t, 0, sizeof t);
  cin >> n;
  for (int i = 0; i < n; i++) {
    cin >> s;
    insert(s);
  }
  cin >> s;
  getFail();
  cout << query(s) << "\n";
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

[P3808 AC 自动机（简单版）](https://www.luogu.com.cn/problem/P3808)

**code**

```C++
// Luogu P3808 AC 自动机（简单版）
// https://www.luogu.com.cn/problem/P3808 2024-03-21 20:54:58
#include <cstring>
#include <iostream>
#include <queue>
#include <vector>
using namespace std;
#define all(a) begin(a), end(a)
int n, m;
const int N = 1E6 + 10;
struct node {
  int son[26];
  int end;
  int fail;
} t[N];
char s[N];
int cnt;
void insert(char *s) {
  int now = 0;
  int _n = strlen(s);
  for (int i = 0; i < _n; i++) {
    int ch = s[i] - 'a';
    if (t[now].son[ch] == 0) t[now].son[ch] = ++cnt;
    now = t[now].son[ch];
  }
  t[now].end++;
}
void getFail() {
  queue<int> q;
  for (int i = 0; i < 26; i++) {
    if (t[0].son[i]) q.emplace(t[0].son[i]);
  }
  while (q.size()) {
    auto now = q.front();
    q.pop();
    for (int i = 0; i < 26; i++) {
      if (t[now].son[i]) {
        t[t[now].son[i]].fail = t[t[now].fail].son[i];
        q.emplace(t[now].son[i]);
      } else {
        t[now].son[i] = t[t[now].fail].son[i];
      }
    }
  }
}
int query(char *s) {
  int ans = 0, now = 0;
  int _n = strlen(s);
  for (int i = 0; i < _n; i++) {
    int ch = s[i] - 'a';
    now = t[now].son[ch];
    int tmp = now;
    while (tmp && t[tmp].end != -1) {
      ans += t[tmp].end;
      t[tmp].end = -1;
      tmp = t[tmp].fail;
    }
  }
  return ans;
}
void solve() {
  memset(t, 0, sizeof t);
  cin >> n;
  for (int i = 0; i < n; i++) {
    cin >> s;
    insert(s);
  }
  cin >> s;
  getFail();
  cout << query(s) << "\n";
}

signed main() {
  ios::sync_with_stdio(false);
  cin.tie(0), cout.tie(0);
  solve();
  return 0;
}
```

[P3796 AC 自动机（简单版 II）](https://www.luogu.com.cn/problem/P3796)

**code**

```C++
// Luogu P3796 AC 自动机（简单版 II）
// https://www.luogu.com.cn/problem/P3796 2024-03-22 00:35:25

#include <bits/stdc++.h>
using namespace std;
#define all(a) begin(a), end(a)
// #define int long long
const int N = 1E6 + 10;
int n, m;
struct node {
  int son[26];
  int end;
  int fail;
  int id;
} t[N];
char s[N];
int id[N];
int cnt;
vector<int> out;
void insert(char *s, int idx) {
  int now = 0;
  int _n = strlen(s);
  for (int i = 0; i < _n; i++) {
    int ch = s[i] - 'a';
    if (t[now].son[ch] == 0) t[now].son[ch] = ++cnt;
    now = t[now].son[ch];
  }
  t[now].end++;
  t[now].id = idx;
}
void getFail() {
  queue<int> q;
  for (int i = 0; i < 26; i++) {
    if (t[0].son[i]) q.emplace(t[0].son[i]);
  }
  while (q.size()) {
    auto now = q.front();
    q.pop();
    for (int i = 0; i < 26; i++) {
      if (t[now].son[i]) {
        t[t[now].son[i]].fail = t[t[now].fail].son[i];
        q.emplace(t[now].son[i]);
      } else {
        t[now].son[i] = t[t[now].fail].son[i];
      }
    }
  }
}
int query(char *s) {
  int ans = 0, now = 0;
  int _n = strlen(s);
  for (int i = 0; i < _n; i++) {
    int ch = s[i] - 'a';
    now = t[now].son[ch];
    int tmp = now;
    while (tmp && t[tmp].end != -1) {
      id[tmp] += t[tmp].end;
      if (id[tmp] > ans) {
        ans = id[tmp];
        out.clear();
        out.emplace_back(t[tmp].id);
      } else if (id[tmp] == ans) {
        out.emplace_back(t[tmp].id);
      }
      ans = max(ans, id[tmp]);
      tmp = t[tmp].fail;
    }
  }
  return ans;
}
void solve(int n) {
  memset(t, 0, sizeof t);
  memset(id, 0, sizeof id);
  out.clear();
  vector<string> last(n);
  for (int i = 0; i < n; i++) {
    cin >> s;
    insert(s, i);
    last[i] = s;
  }
  cin >> s;
  getFail();
  cout << query(s) << "\n";
  sort(all(out));
  for (auto it : out) cout << last[it] << "\n";
}

signed main() {
  ios::sync_with_stdio(false);
  cin.tie(0), cout.tie(0);
  while (cin >> n, n) solve(n);
  return 0;
}
```

