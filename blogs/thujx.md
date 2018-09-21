### Day 1 A. 生成树计数

考虑这棵树的 $\mathrm{prufer}$ 序列，设 $d_i$ 表示 $i$ 在 $\mathrm{prufer}$ 序列中出现的次数，则有 $\sum d_i = n - 2$。

考虑一个序列 $d$ 的贡献，是

$$
\begin{eqnarray}
&&\frac{(n-2)!}{\prod d_i!}\prod(d_i+1)^ma_i^{d_i+1}\sum (d_i+1)^m\\
&=&(n-2)!\prod a_i \sum_j \frac{1}{\prod d_i!} \prod a_i^{d_i}\prod_{i\ne j}(d_i+1)^m (d_j+1)^{2m}
\end{eqnarray}
$$

设后面这个 $\sum$ 关于 $\sum d_i$ 的生成函数是 $F(x)$。再设

$$
\begin{eqnarray}
A(x)&=&\sum_{k} \frac{1}{k!} (k+1)^m x^k\\
B(x)&=&\sum_{k} \frac{1}{k!} (k+1)^{2m} x^k\\
\end{eqnarray}
$$

考虑枚举 $(d_i+1)^{2m}$ 是哪一项被算的，则有

$$
\begin{eqnarray}
F(x)&=&\sum_i B(a_ix) \prod_{j\ne i} A(a_j x)\\
&=&\sum_i \frac{B(a_i x)}{A(a_i x)} \prod_i A(a_ix)\\
&=&\sum_i \frac{B(a_i x)}{A(a_i x)} \cdot \exp {\sum_i \ln A(a_ix)}\\
\end{eqnarray}
$$

求出 $\frac{B(x)}{A(x)}$ 和 $\ln A(x)$ 后，需要代入 $a_ix$ 并求和，于是还要对 $k\in [0,n-1)$ 预处理一个 $\sum_i a_i^k$。

总复杂度 $O(n\log^2 n)$

```
https://paste.ubuntu.com/p/rpKZtz4skz/
```

### Day 1 B. 无限之环

跑费用流，对于度数 $0,1,4$ 的点直接建边，对于度数 $2$ 的点可以拆成上下和左右两部分建边，对于度数 $3$ 的点解个方程。

```
https://paste.ubuntu.com/p/wmGJFWWPTd/
```

### Day 2 A. 小 Y 和地铁

考虑两个线段 $[l_1,r_1]$ 和 $[l_2,r_2](l_1<l_2)$，如果前者包含后者，则后者的端点方向不同造成 $1$ 的代价；如果交叉，则 $r_1$ 和 $l_2$ 的方向相同造成 $1$ 的代价。直接枚举是 $2^n$ 的。

考虑按左端点排序以后，如果知道了前 $i$ 条线段的右端点方向，那么可以直接贪心出 $i$ 的左端点方向，因此是 $2^{\frac{n}{2}}$ 的，加上最优解剪枝即可过。

```
https://paste.ubuntu.com/p/BpVrvCWgH7/
```

### Day 2 B. 小 Y 和二叉树

先求出所有子树/子树外的最小的序列开头。

首先编号最小的度数 $\le 2$ 的点一定是序列开头，在树的最左下方。

如果固定了某个点作为当前子问题的序列开头，可以贪心右儿子和右父亲的放法，递归求解。如果固定了某个点作为当前子问题的根，可以贪心左右儿子的放法，递归求解。

```
https://paste.ubuntu.com/p/wH4cJrTDqM/
```

### Day 2 C. 小 Y 和恐怖的奴隶主

搜一下发现状态最多 $165$ 个，加上记答案一共 $166$ 维，先预处理出 $2$ 的次幂的答案，然后询问的时候用向量乘矩阵，复杂度就是 $S^3\log n + T S^2 \log n$（$S$ 是维数）。

```
https://paste.ubuntu.com/p/26SDwXckck/
```

### Day 3 A. 简单数据结构

这个题由于限制了每个数的插入次数最多为 $10$，所以总复杂度很稳。

给每一个数开一个桶记录一下后面长为 $d$ 的答案数量，每次后端插删的时候更新约数，然后更新约数的约数。前端插删的时候枚举倍数暴力求一下这个数的答案就行了。

```
https://paste.ubuntu.com/p/CXXfVKw5JX/
```

### Day 3 B. 福若格斯

首先手推一下每个数的 surreal number。

```
*:    LL_RR LRL_R L_RLR
up:   L_LRR
down: LLR_R
-1:   _RLLR _RLRL R_RLL
+1:   LRRL_ RLRL_ RRL_L
-1/2: LR_RL
+1/2: RL_LR
0:    Rest All
```

这里规定 $\{0|0\}=*$ 表示先手胜，$\{0|*\}=\uparrow$，$\{*|0\}=\downarrow$

然后一共有 $0,\pm1,\pm \frac{1}{2},*,\uparrow,\downarrow$ 一共 $8$ 种状态，其中前 $5$ 种可以求和，组合数算一下 $>0,=0,<0$ 的方案数分别有多少。

然后 $\sum =0$ 的状态会受到 $*$ 的奇偶性和 $\uparrow,\downarrow$ 哪个比较多影响。需要注意的是 $*$ 产生影响的话 $\uparrow,\downarrow$ 的个数差需要 $>1$ 才会造成对应的影响。

```
https://paste.ubuntu.com/p/bgxYJ6fgf5/
```

### Day 3 C. 避难所

可以发现样例的构造方法是 $\{2\times 3,2\times 3,2\times 3\} \Rightarrow \{3,3,3,2\times 2\times 2\}$。

于是可以构造最优解是 $\{x\times y,x\times y,x\times y\}$，而贪心解是 $\{x,x,x,y\times y\times y\}$ 的东西。$n$ 足够大的时候大致取 $y < n^{1/3},x>n^{1/2}$ 即可。

$n$ 小的话就暴力一下形如 $\{a,b,b\}$ 的答案是否可行即可。

```
https://paste.ubuntu.com/p/YMKMxH4TYP/
```

### Day 4 C. 某位歌姬的故事

首先离散化，求出每一段的最大值，判一下不合法。

然后对于同一个最大值，把这个最大值的区间全部抠出来，再把是这个最大值的限制全部抠出来，做一个 DP 就行了。

```
https://paste.ubuntu.com/p/dWTw4NmFFk/
```

