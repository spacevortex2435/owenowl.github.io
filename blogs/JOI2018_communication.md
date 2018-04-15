# JOI2018春季合宿communication题简要做法

## Airline Route Map 

#### 简要题意

通信题。

Alice得到一个带标号的$1000$个点的无向图，希望发送给Bob。

Alice只能发送一个不带标号的无向图给Bob，并且这个无向图只能比原图多最多$12$个点。

#### 题目分析

我的初步想法是原图保持不动，用多出来的$12$个点给他们做标记。

发现$12$大约是$\log(1000)$的级别，而且额外多$2$。

可以考虑其中$\log(1000)=10$个点和原图的点的二进制位相连，剩下两个点给这$10$个点打标记。

假设这$10$个点命名$B_0\sim B_9$，剩下两个叫$G$和$H$。

为了直接辨认所有$B$点，我先把$G$和所有$B$点相连，再把$H$和$G$相连。

这个时候$H$的度数只有$1$可以考虑利用度数直接找到它。不过要考虑误判，原图$0$和$2^k$号点的度数可能都是$1$，给他们做一下映射，连二进制位的时候把他们看作$1001\sim 1011$往$B$点连，保证他们度数至少是$2$。

这时候我们可以判断出$H$，紧接着得到$G$和所有$B$，但是并不知道$B$的顺序。

把$B_0$单独拉出来看作一个特殊点，接着把$B_1\sim B_9$顺次连成一条链。为了辨认特殊点$B_0$和链首$B_1$，我把$B_0$和$B_2\sim B_9$连了起来，这样在所有$B$点构成的子图中，拥有度数$8$的点一定是$B_0$，没有和$B_0$相连的是$B_1$，从$B_1$开始dfs得到$B_2\sim B_9$。

最后通过$B$把原编号还原，注意把$1001\sim 1011$映射回去。

#### 代码

Alice:

```cpp
#include "Alicelib.h"
#include <cassert>
#include <cstdio>
#include <vector>

using namespace std;

void Alice( int N, int M, int A[], int B[] ){
	vector < pair<int, int> > G;
	int trans[10010];
	for (int i = 0; i <= 1000; i++) {
		trans[i] = i;
	}
	trans[0] = 1001;
	for (int i = 1, j = 1002; i <= 1000; i <<= 1, j++) {
		trans[i] = j;
	}
	for (int i = 0; i < M; i++) {
		int u = A[i], v = B[i];
		G.push_back(make_pair(u, v));
	}
	for (int i = 0; i < N; i++) {
		int t = trans[i];
		for (int j = 0; j <= 9; j++) {
			if ((t >> j) & 1) {
				G.push_back(make_pair(i, N + j));
			}
		}
	}
	for (int j = 1; j <= 8; j++) {
		G.push_back(make_pair(N, N + 1 + j));
		G.push_back(make_pair(N + j, N + 1 + j));
	}
	for (int j = 0; j <= 9; j++) {
		G.push_back(make_pair(N + j, N + 10));
	}
	G.push_back(make_pair(N + 10, N + 11));
	InitG(N + 12, G.size());
	for (int i = 0; i < G.size(); i++) {
		MakeG(i, G[i].first, G[i].second);
	}
}
```

Bob:

```cpp
#include "Boblib.h"
#include <cassert>
#include <cstdio>

#include <vector>
using namespace std;

#include <cstring>
void Bob( int V, int U, int C[], int D[] ){
	int N = V - 12;
	int trans[10090];
	int id[10090], p[10090];
	memset(id, -1, sizeof id);
	vector < pair<int, int> > ANS;
	vector <int> G[10090];
	int deg[10090];
	for (int i = 0; i <= 1000; i++) {
		trans[i] = i;
	}
	trans[1001] = 0;
	for (int i = 1, j = 1002; i <= 1000; i <<= 1, j++) {
		trans[j] = i;
	}
	for (int i = 0; i < U; i++) {
		deg[C[i]]++;
		deg[D[i]]++;
		G[C[i]].push_back(D[i]);
		G[D[i]].push_back(C[i]);
	}
	for (int i = 0; i < V; i++) {
		if (deg[i] == 1) {
			id[i] = N + 11;
			p[N + 11] = i;
		}
	}
	for (auto i : G[p[N + 11]]) {
		id[i] = N + 10;
		p[N + 10] = i;
	}
	int tag[10090];
	for (auto i : G[p[N + 10]]) {
		if (i != p[N + 11]) {
			tag[i]++;
		}
	}
	for (int i = 0; i < V; i++) {
		if (!tag[i]) {
			continue;
		}
		if (i == p[N + 10] || i == p[N + 11]) {
			continue;
		}
		int sum = 0;
		for (auto t : G[i]) {
			sum += tag[t];
		}
		if (sum == 8) {
			id[i] = N;
			tag[i] = 3;
			p[N] = i;
			for (auto t : G[i]) {
				if (tag[t]) {
					tag[t]++;
					
				}
			}
			break;
		}
	}
	for (int i = 0; i < V; i++) {
		if (tag[i] == 1) {
			int r = i, k = N + 1;
			id[r] = k;
			p[k] = r;
			while (k < N + 9) {
				k++;
				for (auto t : G[r]) {
					if (tag[t] == 2) {
						r = t;
						break;
					}
				}
				tag[r] = 1;
				id[r] = k;
				p[k] = r;
			}
			break;
		}
	}
	for (int i = 0; i < V; i++) {
		if (tag[i]) {
			continue;
		}
		if (i == p[N + 10] || i == p[N + 11]) {
			continue;
		}
		int sum = 0;
		for (auto t : G[i]) {
			if (tag[t]) {
				int bt = id[t] - N;
				sum |= 1 << bt;
			}
		}
		sum = trans[sum];
		id[i] = sum;
		p[sum] = i;
	}
	for (int i = 0; i < U; i++) {
		int u1 = C[i], u2 = D[i];
		u1 = id[u1], u2 = id[u2];
		if (u1 >= N || u2 >= N) {
			continue;
		}
		ANS.push_back(make_pair(u1, u2));
	}
	InitMap(N, ANS.size());
	for (int i = 0; i < ANS.size(); i++) {
		MakeMap(ANS[i].first, ANS[i].second);
	}
}
```

#### 其他分析

注意到如果按照题目给出的方式对你发送的无向图进行随机打乱（随机一个排列替换掉所有入点和出点），并没有改变任何一条边的方向，那么这道题可以视为你在发送一个有向图。

有向图使用$12$个点（或更少）比用无向图解决这个问题更加简单。

Codeforces上的一位用户`azneyes`提供了一种非常简单的只添加一个点的有向图做法。

```
I solved with only 1 extra vertex ...

For all given edges make sure A_i < B_i and swap if not. Send all edges to Bob. Add extra vertex N. If edge (v, v+1) does not exist send it and also send (v, N). Also send (N - 1, N)

On Bob's side, run topological sort to recover the permutation that shuffled the vertices. The order is unique due to extra edges sent. If (v, N) is an edge than (v, v+1) was an extra edge so we don't include these in output.

对所有边保证A_i < B_i。
加入一个新点N，对于每个(v, v+1)，如果该边原来不存在，就加入这条边，再加入(v, N)。同时加入(N - 1, N)
在Bob的角度跑一个拓扑序，则拓扑序一定是1-N，进行编号。检查每一个(v, v+1)，如果(v, N)存在则删去这条边。
```



## Library

#### 简要题意

交互题。

给一个长度$1000$的排列$p$，你可以做$20000$次询问，每次可以询问$1\sim N$中一些数字在排列上的所在位置构成的连续段个数。要求确定这个排列，正序或逆序都算对。

#### 题目分析

首先确定这个排列等价于确定每个数左右两侧的数。

对于一个数$p$和一个不包含$p$的集合$S$：

-   如果$\mathrm{Query}(S)-\mathrm{Query}(p+S)=-1$，则表明$S$中没有数与$p$相邻；
-   如果$\mathrm{Query}(S)-\mathrm{Query}(p+S)=0$，则表明$S$中有一个数与$p$相邻；
-   如果$\mathrm{Query}(S)-\mathrm{Query}(p+S)=1$，则表明$S$中有两个数与$p$相邻；

即每两次询问可以得到$S$中与$p$相邻的数的个数。

如果我们对于每一个$p$，二分有数与$p$相邻的区间，那么可以在$2\log n$的询问里找到一个与$p$相邻的数。

从$1$向$n$枚举$p$的时候，由于前面的邻接关系已经被确定，可以直接从$[p+1, n]$开始二分。

因为有$n$个邻接关系，所以总的次数是$n\times2\log n$，大约就是$20000$。

经过测试，我的代码极限询问次数是$19932$（好险！）。

注意特判$n=1$。

#### 代码

```cpp
#include <cstdio>
#include <vector>
#include "library.h"
using namespace std;

vector <int> M, res;
int N;
vector <int> adj[1010];
int vis[1010];

void reset() {
	for (int i = 0; i < N; i++) {
		M[i] = 0;
	}
}

void fill(int l, int r) {
	for (int i = l; i <= r; i++) {
		M[i - 1] = 1;
	}
}

void divide(int p, int l, int r, int d) {
	if (!d) {
		return;
	}
	if (l == r) {
		adj[p].push_back(r);
		adj[r].push_back(p);
		return;
	}
	int mid = (l + r) >> 1;
	reset();
	fill(p, p); fill(l, mid);
	int A1 = Query(M);
	reset();
	fill(l, mid);
	int A2 = Query(M);
	int dl = A2 - A1 + 1, dr = d - dl;
	if (dl) {
		divide(p, l, mid, dl);
	}
	if (dr) {
		divide(p, mid + 1, r, dr);
	}
}

inline void sol(int p) {
	if (adj[p].size() >= 2) {
		return;
	}
	reset();
	fill(p, N);
	int A1 = Query(M);
	reset();
	fill(p + 1, N);
	int A2 = Query(M);
	int d = A2 - A1 + 1;
	if (!d) {
		return;
	}
	divide(p, p + 1, N, d);
}

void Solve(int _N) {
	N = _N;
	if (N == 1) {
		res.push_back(1);
		Answer(res);
		return;
	}
	M.resize(N);

	for (int i = 1; i < N; i++) {
		sol(i);
	}

	int pos = -1;
	for (int i = 1; i < N; i++) {
		if (adj[i].size() == 1) {
			pos = i;
			break;
		}
	}

	do {
		res.push_back(pos);
		vis[pos] = 1;
		for (auto w : adj[pos]) {
			if (!vis[w]) {
				pos = w;
				break;
			}
		}
	} while (adj[pos].size() == 2);
	res.push_back(pos);

	Answer(res);
}
```

