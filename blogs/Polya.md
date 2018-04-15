### 一些群论前置知识

-   群：称一个集合$S$和定义在$S$上的一个二元运算构成一个群，当且仅当运算满足：
    -   结合律
    -   单位元存在
    -   逆元存在
    -   运算封闭性
-   子群：保留元素集合$S$的一个子集$T$，使得运算在$T$上对上述性质依然成立，称$T$是$S$的一个子群。

##### 拉格朗日定理

在一个有限群$G$上，设$S$是它的子群，必有$|S|$是$|G|$的约数。

可以考虑用**陪集**证明：

对一个元素$g\in G$，$gS=\{gs|s\in S\}$就是$S$关于$g$的左陪集。因为逆元存在，所以$|gS|=|S|$。

考虑两个元素$u,v\in G(u\ne v)$，如果$uS\cap vS\ne \phi$，则不妨设$us_1=vs_2(s_1,s_2\in S)$。

那么$u=vs_2s_1^{-1}$，则$uS=vs_2s_1^{-1}S$。

因为$s_1^{-1},s_2\in S$，所以$s_2s_1^{-1}S=S$，所以$uS=vS$。

那么所以$S$的不同左陪集无交，设其个数为$w$，则$w|gS|=w|S|=|G|$，得到$|S|$是$|G|$的约数。

同时也得到一个子群$S$的不同陪集数$\times |S|=|G|$。

##### 轨道-稳定化子定理

对一个置换群$M$和一个元素$x$，设$\mathrm{stab}(x)$是$M$中使$x$保持不变的置换构成的集合，$\mathrm{orbit}(x)$是$x$通过$M$中的变换能变换到的位置集合。

则$|\mathrm{orbit}(x)|\times |\mathrm{stab}(x)| = |M|$

首先证明$\mathrm{stab}(x)$是一个子群：

$e(x)=x\Rightarrow e\in\mathrm{stab}(x)$

$\forall s_1,s_2\in\mathrm{stab}(x),s_1(x)=s_2(x)=x$，所以$s_1s_2(x)=x$，即$s_1s_2\in\mathrm{stab}(x)$

则$\mathrm{stab}(x)$是$M$的一个子群。

再考虑所有置换$c\in M$，因为$\forall s\in \mathrm{stab}(x),s(x)=x$，所以$\forall s\in c\mathrm{stab}(x),s(x)=c(x)$。

因为所有不同的$c(x)$组成的集合即为$\mathrm{orbit}(x)$，所以$\mathrm{stab}(x)$所有不同的左陪集数量也为$|\mathrm{orbit}(x)|$。

由拉格朗日定理即得到$|\mathrm{orbit}(x)|\times |\mathrm{stab}(x)| = |M|$

### Burnside 引理

对一个关于染色方案的置换群$M$，对于任意两个元素$x,y$，如果存在一个$a\in M$使得$a(x)=y$，则称$x,y$属于同一个等价类。每个等价类称作一个轨道。

Burnside 引理提供了一种方法求$M$的轨道数：所有置换的不动点个数$c_1(a)$的算术平均。

证明：

对于每一个元素$x$，假设它对答案贡献$\frac{1}{|\mathrm{orbit}(x)|}$，那么每一个轨道累加得到贡献$1$。

由轨道-稳定化子定理$$\sum_x\frac{1}{|\mathrm{orbit}(x)|}=\frac{\sum_x|\mathrm{stab}(x)|}{|M|}=\frac{\sum_{a\in M}|c_1(a)|}{|M|}$$

###Pólya 定理

Pólya 定理是Burnside 引理的具体化。

假设现在有$m$种颜色。

如果一个关于位置的置换$a$有$c(a)$个轮换，因为不动点经过置换后仍是自身，所以每个轮换的元素的颜色相同。对于这个置换来说，有$m^{c(a)}$中染色方法使得存在这样一个不动点。

那么用这个来枚举得到$$\frac{\sum_{a\in M}|c_1(a)|}{|M|}=\frac{\sum_{a\in G}m^{c(a)}}{|G|}$$

值得注意的是，$M$里的置换是关于所有染色方案的置换，$G$里的置换是关于染色位置的置换，$|M|=|G|$。

$M$里的置换是$m^n$阶置换，$G$里的置换是$n$阶置换，$G$中的染色在$m$的幂中体现。