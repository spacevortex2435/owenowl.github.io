以前接触了好多次行列式的题，然后口胡了好多行列式的做法，但到现在我也没写过行列式的题。准备恶补一下。

### 行列式定义

对一个$N\times N$矩阵$\mathbf{A}=\left[\begin{array}{cccc}a_{11}&a_{12}&\cdots&a_{1n}\\a_{21}&a_{22}&\cdots&a_{2n}\\\vdots&\vdots&\ddots&\vdots\\a_{n1}&a_{n2}&\cdots&a_{nn}\\\end{array}\right]$，

定义它的行列式$\det{\mathbf{A}}=|\mathbf{A}|=\sum_{P}(-1)^{\tau(P)}\prod_i a_{iP_i}$。

其中$\tau(P)$是排列$P$的逆序对数。

这个定义了解一下就可以了，计算是阶乘复杂度，顶多用于二阶或三阶行列式。

###行列式基本性质

最基本的性质几条：

-   交换行列式两行（列），行列式取反。
-   同一行（列）乘数$K$，行列式乘数$K$。
-   如果两行（列）成比例，行列式为$0$。
-   一行（列）加上另一行（列）的$K$倍，行列式不变。
-   $\det \mathbf{A}=\det \mathbf{A}^T$
-   $\det \mathbf{A}\cdot \det \mathbf{B}=\det \mathbf{AB}$
-   $\det\mathbf{A}\ne 0\Leftrightarrow \mathrm{rank}(\mathbf{A})=n\Leftrightarrow\exists \mathbf{B},\mathbf{A}\times \mathbf{B}=\mathbf{I}\Leftrightarrow$所有行（列）向量线性无关。
-   行列式的组合意义是$N$维空间下$N$个向量组成的一个$N$维体的有向体积。

### 行列式计算

二阶或者三阶行列式可以阶乘算。

二阶：$\det\mathbf{A} = v_1\times v_2=ad-bc$

三阶：$\det\mathbf{A}=a_1b_2c_3+a_2b_3c_1+a_3b_1c_2-a_1b_3c_2-a_2b_1c_3-a_3b_2c_1$

高阶的消成上三角矩阵。

有这么个定理：

-   上三角矩阵的行列式$\det \mathbf{A}=\prod_ia_{ii}​$。直接暴力带入原计算式可以证得。

正好可以发现如果$\exists a_{ii}=0$，说明矩阵无逆，则$\det\mathbf{A}=0$。

利用行对换和一行乘另一行$K$倍，使用辗转相除来实现类似高斯消元的过程。要注意行对换的时候行列式取反。这个复杂度是$O(N^3\log N)$。

### 代数余子式

选定一个$(i,j)$，删去行列式的第$i$行和第$j$列，留下的部分的行列式就是元素$a_{ij}$的余子式，记为$M_{ij}$。

$a_{ij}$的代数余子式$A_{ij}=(-1)^{i+j}M_{ij}$

$a_{ij}$的代数余子式$A_{ij}$与$a_{ij}$的值无关，仅与其位置有关。

如果一个行列式非零，那么这个行列式取逆，转置，然后全部除以行列式值就得到代数余子式构成的矩阵。

### 拉普拉斯展开定理

-   $\sum_ia_{ki}A_{ki}=\det\mathbf{A}$
-   $\sum_ia_{ki}A_{li}=0\;(k\ne l)$

### 某个计数定理

从平面上$N$个点依次连到另$N$个点，不相交路径的方案数是一个行列式，其中$a_{ij}$表示一侧第$i$个点连到另一侧第$j$个点的方案数。

考虑两个相交的路径，在第一个交点对换后续路径的话相当于交换排列中两个数，容斥一下即可。

### 矩阵树定理

先讨论无向图。

定义度数矩阵$\mathbf{D}$，其中$d_{ii}=\mathrm{degree}(i)$。

再定义邻接矩阵$\mathbf{A}​$，其中$a_{ij}=-[\mathrm{link}(i,j)]​$。

则这个无向图的基尔霍夫矩阵$\mathbf{C}=\mathbf{D}-\mathbf{A}$。

$\mathbf{C}$的每行每列和均为$0$，所以$\det \mathbf{C}=0$。

生成树个数是$M_{ii}$，即主对角线上的任意一个元素的余子式。

如果图是有向图，考虑求内向树还是外向树，度数和邻接变得有向，再枚举根，求这个根在主对角线上的元素的余子式。

另外，对于完全图的基尔霍夫矩阵，对角线元素的余子式$M_{ii}=n^{n-2}$。（和Prufer序列结果相同）