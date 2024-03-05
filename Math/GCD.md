[toc]

# GCD

## **辗转相除法**



整除 符号 $|$

例如 $3|6$



对于 $a,b$

我们用$(a,b)$表示 $a,b$ 的最大公因数

我们让 $a\ge b$

那么
$$
a=bp+r
$$

$$
(a,b)=(bp+r,b)=(r,b)   \qquad  r <b
$$

$$
(a,b)=(b,r)
$$

可以继续迭代,两次可以让 $a$ 迭代到原先的一半以下
$$
O(log(min\{a,b\}))
$$

```C++
int gcd(int a, int b) { return b == 0 ? a : gcd(b, a % b); }
```

## **exgcd**

$$
\begin{align}
a&=bq_1+r_1\\
b&=r_1q_2+r_2\\
r_1&=r_2q_3+r_3\\
&\dots\\
r_{k-1}&=r_{k}q_{k+1}+r_{k+1}\\
r_k&=r_{k+1}q_{k+2}+r_{k+2}\\

\end{align}
$$

$$
r_{k+2}=0  \qquad  r_{k+1}=(a,b)
$$

```C++
int exgcd(int a, int b, int &x, int &y) {
  if (b == 0) return x = 1, y = 0, a;
  int x1, y1, gcd;
  gcd = exgcd(b, a % b, x1, y1);
  x = y1, y = x1 - a / b * y1;
}
```

