#! https://zhuanlan.zhihu.com/p/687702616
$$
\begin{align}
(a+b)^n=\sum\limits^n_{r=0}C^r_na^rb^{n-r}=\sum\limits^n_{r=0}C^r_nb^ra^{n-r}
\end {align}
$$

**卢卡斯定理**
$$
\begin{align}

\forall n,r\ge0 ,m\in 素数\qquad C^r_n\equiv  \prod_{i=0}^k
C^{r_i}_{n_i}\pmod{m}\\
n=n_km^k+\dots+n_1m+n_0\\
r=r_km^k+\dots+r_1m+r_0\\
n_i<r_i,C_{n_i}^{r_i}=0
\end{align}
$$

$$
C^r_n\equiv C^{r\%m}_{n\%m}\times C^{r/m}_{n/m} \pmod{m}
$$

```C++
int n, m, q;
int qmi(int a, int b) {
  int res = 1 % q;
  while (b) {
    if (b & 1) res = res * a % q;
    a = a * a % q;
    b >>= 1;
  }
  return res;
}
int C(int a, int b) {
  if (b > a) return 0;
  int res = 1;
  for (int i = 1, j = a; i <= b; i++, j--) {
    res = res * j % q;
    res = res * qmi(i, q - 2) % q;
  }
  return res;
}
int lucas(int a, int b) {
  if (a < q && b < q) return C(a, b);
  return C(a % q, b % q) * lucas(a / q, b / q) % q;
}
void solve() {
  cin >> n >> m >> q;
  cout << lucas(n + m, n) << endl;
}
```

