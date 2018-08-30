## 啖光之花加强版

### 题意

已知 $h_k = \sum_{i=0}^{n-1} a_ib_i^k \;(0\le k<n)$

现给出 $n$，$h_0,h_1,\ldots,h_{n-1}$，$b_0,b_1,\ldots,b_{n-1}$

求 $a_0,a_1,\ldots,a_{n-1}$

### 解法1

设 $H(x)$ 是序列 $h_0,h_1,\ldots,h_{n-1}$ 的母函数，那么
$$
\begin{eqnarray}
H(x)&=&\sum_{i=0}^{n-1}\frac{a_i}{1-b_ix}\\&=&\frac{\sum_{i=0}^{n-1} a_i \prod_{j\ne i }(1-b_jx)}{\prod_{i=0}^{n-1}(1-b_ix)}
\end{eqnarray}
$$
再设
$$
\begin{eqnarray}
R(x)&=&\prod_{i=0}^{n-1}(1-b_ix)\\
P(x)&=&H(x)R(x)\\
&=&\sum_{i=0}^{n-1}a_i\prod_{j\ne i}(1-b_jx)
\end{eqnarray}
$$
由于后者是一个 $n-1$ 次多项式，那么卷积 $H(x)$ 和 $\prod_{i=0}^{n-1}(1-b_ix)$ 求 $P(x)$ 的时候可以在 $\bmod x^n$ 的意义下进行。

将 $x=\frac{1}{b_i}$ 代入 $P(x)$ 可以得到
$$
P(\frac{1}{b_i})=a_i\prod_{i\ne j}(1-\frac{b_j}{b_i})
$$
现在的问题是如何快速对于所有 $i$ 计算 $\prod_{i\ne j}(1-\frac{b_j}{b_i})$

构造
$$
Q(x)=\sum_{i=0}^{n-1}\prod_{i\ne j}(1-b_jx)
$$
将 $x=\frac{1}{b_i}$ 代入 $Q(x)$ 可以得到
$$
Q(\frac{1}{b_i})=\prod_{i\ne j}(1-\frac{b_j}{b_i})
$$

则
$$
a_i=\frac{P(\frac{1}{b_i})}{Q(\frac{1}{b_i})}
$$


注意到（这里的 $\operatorname{reverse}$ 指在 $\bmod x^n$ 意义下翻转多项式的前 $n$ 项系数）
$$
\begin{eqnarray}
Q(x)&=&\operatorname{reverse}(\sum_{i=0}^{n-1}\prod_{i\ne j}(x-b_j))\\
&=&\operatorname{reverse}(\frac{\mathrm{d}}{\mathrm{d}x}\prod_{i=0}^{n-1}(x-b_i))\\
R(x)&=&\operatorname{reverse}(\prod_{i=0}^{n-1}(x-b_i))
\end{eqnarray}
$$
对比 $Q(x)$ 和 $R(x)$ 的系数可以得到
$$
Q(x)=\sum_{i=0}^{n-1}x^i(n-i)[x^i]R(x)
$$

那么只需要先分治 FFT 求出 $R(x)$，然后求出 $P(x)$ 和 $Q(x)$，对 $P(x)$ 和 $Q(x)$ 分别进行多点求值即可

复杂度 $O(n\log^2n)$

