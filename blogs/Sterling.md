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

### 通常幂与下降幂转换公式

$$\begin{eqnarray}x^n&=&\sum_{k=0}^n\left\{\begin{array}{c}n\\k\end{array}\right\}x^{\underline k}\\&=&\sum_{k=0}^n\left\{\begin{array}{c}n\\k\end{array}\right\}\left(\begin{array}{c}x\\k\end{array}\right)k!\\x^{\underline n}&=&\sum_{k=0}^n(-1)^{n-k}\left[\begin{array}{c}n\\k\end{array}\right]x^k\end{eqnarray}$$

### 斯特林数反演

更广泛地：

$$\begin{eqnarray}f(n)&=&\sum_{k=0}^n\left\{\begin{array}{c}n\\k\end{array}\right\}g(k)\\&\Big\Updownarrow&\\g(n)&=&\sum_{k=0}^n(-1)^{n-k}\left[\begin{array}{c}n\\k\end{array}\right]f(k)\end{eqnarray}$$

当取$f(n)=x^n,g(n)=x^{\underline n}$时即为通常幂与下降幂转换公式

### 栗子

#####丢一个CF 932 E

这一场当场A了这题，但是因为一些不可告人的原因被ban了。

求：

$$\sum_{i=1}^n\left(\begin{array}{c}n\\i\end{array}\right)i^k$$

范围是$n\le 10^9,k\le 5000$，初步猜测复杂度是$O(k^2)$。

推一推：

$$\begin{eqnarray}&&\sum_{i=1}^n\left(\begin{array}{c}n\\i\end{array}\right)i^k\\&=&\sum_{i=1}^n\left(\begin{array}{c}n\\i\end{array}\right)\sum_{j=0}^k\left\{\begin{array}{c}k\\j\end{array}\right\}i^{\underline j}\\&=&\sum_{i=1}^n\sum_{j=0}^k\left\{\begin{array}{c}k\\j\end{array}\right\}\frac{n!}{(n-i)!\cdot (i-j)!}\\&=&\sum_{i=1}^n\sum_{j=0}^k\left\{\begin{array}{c}k\\j\end{array}\right\}n^{\underline j}\left(\begin{array}{c}n-j\\i-j\end{array}\right)\\&=&\sum_{j=0}^k\left\{\begin{array}{c}k\\j\end{array}\right\}n^{\underline j}\sum_{i=1}^n\left(\begin{array}{c}n-j\\i-j\end{array}\right)\\&=&\sum_{j=0}^k\left\{\begin{array}{c}k\\j\end{array}\right\}n^{\underline j}\cdot2^{n-j}\end{eqnarray}$$

发现斯特林数预处理是$O(k^2)$，下降幂预处理是$O(k)$，后面的计算是$O(k\log k)$。

##### 再丢一个BZOJ 2159

题意是给一棵树，求：

$$\mathrm{ans}(i)=\sum_{j=1}^n\mathrm{dis}(i,j)^k$$

范围是$n\le 5\times 10^4,k\le 150$。

看到这个结构就先通常幂转下降幂（组合数）

$$\begin{eqnarray}\mathrm{ans}(i)&=&\sum_{j=1}^n\mathrm{dis}(i,j)^k\\&=&\sum_{j=1}^n\sum_{l=0}^k\left\{\begin{array}{c}k\\l\end{array}\right\}\left(\begin{array}{c}\mathrm{dis}(i,j)\\l\end{array}\right)l!\\&=&\sum_{l=0}^k\left\{\begin{array}{c}k\\l\end{array}\right\}l!\sum_{j=1}^n\left(\begin{array}{c}\mathrm{dis}(i,j)\\l\end{array}\right)\end{eqnarray}$$

记$w_{i,l}=\sum_{j=1}^n\left(\begin{array}{c}\mathrm{dis}(i,j)\\l\end{array}\right)$。

$$\mathrm{ans}(i)=\sum_{l=0}^k\left\{\begin{array}{c}k\\l\end{array}\right\}l!\cdot w_{i,l}$$

把$w_{u,l}$拆成两部分$f_{u,l}+g_{u,l}$，其中$f_{u,l}$表示$u$子树对$w_{u,l}$的贡献，$g_{u,l}$表示非$u$子树的贡献。

$$\begin{eqnarray}f_{u,0}&=&\mathrm{size}(u)\\f_{u,l}&=&\sum_{x\in u}\left(\begin{array}{c}\mathrm{dis}(u,x)\\l\end{array}\right)\\&=&\sum_{u\to v,x\in v}\left(\begin{array}{c}\mathrm{dis}(v,x)+1\\l\end{array}\right)\\&=&\sum_{u\to v,x\in v}\left(\begin{array}{c}\mathrm{dis}(v,x)\\l\end{array}\right)+\left(\begin{array}{c}\mathrm{dis}(v,x)\\l-1\end{array}\right)\\&=&\sum_{u\to v}f_{v,l}+f_{v,l-1}\end{eqnarray}$$

同理，

$$\begin{eqnarray}g_{u,0}&=&n-f_{u,0}\\g_{u,l}&=&g_{fa,l}+g_{fa,l-1}+f_{fa,l}+f_{fa,l-1}-f_{u,l}-f_{u,l-1}-f_{u,l-1}-f_{u,l-2}\end{eqnarray}$$

把$f$自下至上DP，把$g$自上至下DP，然后统计答案即可。