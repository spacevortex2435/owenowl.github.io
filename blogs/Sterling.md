### 第二类斯特林数

$\left\{\begin{array}{c}n\\k\end{array}\right\}$表示$n$不同元素分为$k$个非空集合的方案数。

递推：

$$\begin{eqnarray}\left\{\begin{array}{c}1\\1\end{array}\right\}&=&1\\\left\{\begin{array}{c}n\\k\end{array}\right\}&=&\left\{\begin{array}{c}n-1\\k-1\end{array}\right\}+k\times \left\{\begin{array}{c}n-1\\k\end{array}\right\}\end{eqnarray}$$

容斥：

$$\left\{\begin{array}{c}n\\m\end{array}\right\}=\frac{1}{m!}\sum_{k=0}^m(-1)^k\left(\begin{array}{c}m\\k\end{array}\right)(m-k)^n$$

###第一类斯特林数

$\left[\begin{array}{c}n\\k\end{array}\right]$表示$n$不同元素分为$k$个非空环排列的方案数。

递推：

$$\begin{eqnarray}\left[\begin{array}{c}1\\1\end{array}\right]&=&1\\\left[\begin{array}{c}n\\k\end{array}\right]&=&\left[\begin{array}{c}n-1\\k-1\end{array}\right]+(n-1)\times \left[\begin{array}{c}n-1\\k\end{array}\right]\end{eqnarray}$$

### 贝尔数

$B_n$表示$n$个不同元素划分成若干非空集合的方案数。

$$B_0=1$$

根据定义：

$$B_n=\sum_k\left\{\begin{array}{c}n\\k\end{array}\right\}$$

枚举新增集合的元素个数：

$$B_{n+1}=\sum_{k=0}^{n}\left(\begin{array}{c}n\\k\end{array}\right)B_k$$

贝尔数的指数型生成函数：

$$\sum_{n=0}^\infty\frac{B_n}{n!}x^n=e^{e^x-1}$$

$O(n\log n)$ 快速求贝尔数某一项：

$\begin{eqnarray}B_n&=&\sum_k\left\{\begin{array}{c}n\\k\end{array}\right\}\\&=&\sum_m\frac{1}{m!}\sum_{k=0}^m(-1)^k\binom{m}{k}(m-k)^n\\&=&\sum_k(-1)^k\frac{1}{k!} \sum_{m=0}^{n-k}m^n\frac{1}{m!}\end{eqnarray}$

预处理后面的前缀和

### 通常幂与下降幂转换公式

$$\begin{eqnarray}x^n&=&\sum_{k=0}^n\left\{\begin{array}{c}n\\k\end{array}\right\}x^{\underline k}\\&=&\sum_{k=0}^n\left\{\begin{array}{c}n\\k\end{array}\right\}\left(\begin{array}{c}x\\k\end{array}\right)k!\\x^{\underline n}&=&\sum_{k=0}^n(-1)^{n-k}\left[\begin{array}{c}n\\k\end{array}\right]x^k\end{eqnarray}$$

### 斯特林数反演

更广泛地：

$$\begin{eqnarray}f(n)&=&\sum_{k=0}^n\left\{\begin{array}{c}n\\k\end{array}\right\}g(k)\\&\Big\Updownarrow&\\g(n)&=&\sum_{k=0}^n(-1)^{n-k}\left[\begin{array}{c}n\\k\end{array}\right]f(k)\end{eqnarray}$$

当取$f(n)=x^n,g(n)=x^{\underline n}$时即为通常幂与下降幂转换公式
