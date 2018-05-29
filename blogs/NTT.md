存一个多项式板子，写了**模**和**多点查值**了。

用的是 FLOJ #266 的题。

有时间再继续补。

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>

#define FIO ""
#define fx first
#define fy second
#define pb push_back
#define mpar std::make_pair
#define dbug(...) fprintf(stderr, __VA_ARGS__)

typedef double db;
typedef long long ll;
typedef unsigned int uint;
typedef unsigned long long ull;
typedef std::pair<int, int> par;

template <typename Y> inline bool updmin(Y &a, Y b) {if (a > b) {a = b; return 1;} return 0;}
template <typename Y> inline bool updmax(Y &a, Y b) {if (a < b) {a = b; return 1;} return 0;}
template <typename Y> inline Y abs(Y a) {if (a < 0) a = -a; return a;}
template <typename Y> inline Y sqr(Y a) {return a * a;}

int read() {
  int w = 1, q = 0, ch = ' ';
  for (; ch < '0' || ch > '9'; ch = getchar()) if (ch == '-') w = -1;
  for (; ch >= '0' && ch <= '9'; ch = getchar()) q = q * 10 + ch - 48;
  return q * w;
}

inline void FileIO(){freopen(FIO".in", "r", stdin); freopen(FIO".out", "w", stdout);}

using namespace std;

const ll mod = 1811939329;
const ll g0 = 13;
const int N = 262150;
int rnk[N];
inline ll qpow(ll t, ll b) {
  ll r = 1;
  while (b) {
    if (b & 1) r = r * t % mod;
    t = t * t % mod;
    b >>= 1;
  }
  return r;
}

typedef vector <ll> Poly;

ll w[2][N];
void NTTinit() {
  for (int i = 1; i < (1 << 18); i <<= 1) {
    ll wn = qpow(g0, (mod - 1) / (i << 1));
    for (int j = 0; j < i; j++) {
      w[1][i + j] = (j ? wn * w[1][i + j - 1] % mod : 1);
    }
    wn = qpow(g0, mod - 1 - (mod - 1) / (i << 1));
    for (int j = 0; j < i; j++) {
      w[0][i + j] = (j ? wn * w[0][i + j - 1] % mod : 1);
    }
  }
}
inline void NTT(ll *a, int fl, int n) {
  updmax(fl, 0);
  int L = 0;
  for (int _n = n; _n > 1; _n >>= 1, L++);
  for (int i = 0; i < n; i++) {
    rnk[i] = (rnk[i >> 1] >> 1) | ((i & 1) << (L - 1));
  }
  for (int i = 0; i < n; ++i) {
    if (i < rnk[i]) {
      swap(a[i], a[rnk[i]]);
    }
  }
  for (int i = 1; i < n; i <<= 1) {
    for (int j = 0; j < n; j += i << 1) {
      for (int k = 0; k < i; k++) {
        ll x = a[j + k], y = a[j + k + i] * w[fl][i + k] % mod;
        a[j + k] = (x + y) % mod;
        a[j + k + i] = (x - y + mod) % mod;
      }
    }
  }
  if (fl != 1) {
    ll inv = qpow(n, mod - 2);
    for (int i = 0; i < n; i++) {
      (a[i] *= inv) %= mod;
    }
  }
}

void Mul(const ll A[], int lenA, const ll B[], int lenB, ll C[], int lenC) {
  static ll E[N], F[N];
  int x = min(lenA, lenC) + min(lenB, lenC), n = 1;
  while (n < x) {
    n <<= 1;
  }
  memset(E, 0, n << 3);
  memset(F, 0, n << 3);
  memcpy(E, A, min(lenA, lenC) << 3);
  memcpy(F, B, min(lenB, lenC) << 3);
  NTT(E, 1, n);
  NTT(F, 1, n);
  for (int i = 0; i < n; i++) {
    E[i] = E[i] * F[i] % mod;
  }
  NTT(E, -1, n);
  for (int i = 0; i < lenC && i < n; i++) {
    C[i] = E[i];
  }
  for (int i = n; i < lenC; i++) {
    C[i] = 0;
  }
}
void Inv(int n, const ll a[], ll C[]) {
  if (n == 1) {
    C[0] = qpow(a[0], mod - 2);
    return;
  }
  int L = 1, m = (n + 1) >> 1;
  while (L < n << 1) {
    L <<= 1;
  }
  Inv(m, a, C);
  static ll A[N];
  memcpy(A, a, n << 3);
  memset(A + n, 0, (L - n) << 3);
  memset(C + m, 0, (L - m) << 3);
  NTT(A, 1, L);
  NTT(C, 1, L);
  for (int i = 0; i < L; i++) {
    C[i] = (2 - A[i] * C[i] % mod + mod) * C[i] % mod;
  }
  NTT(C, -1, L);
}
void Div(const ll A[], int lenA, const ll B[], int lenB, ll C[], int &lenC) {
  static ll E[N], F[N], G[N];
  if (lenA < lenB) {
    lenC = 1;
    C[0] = 0;
    return;
  }
  lenC = lenA - lenB + 1;
  memcpy(E, A, lenA << 3);
  memcpy(F, B, lenB << 3);
  reverse(E, E + lenA);
  reverse(F, F + lenB);
  Inv(lenC, F, G);
  Mul(E, lenA, G, lenC, C, lenC);
  reverse(C, C + lenC);
}

void upd(Poly &a) {
  while (a.size() > 1 && !a.back()) {
    a.pop_back();
  }
}

Poly operator * (const Poly &a, const Poly &b) {
  Poly c(a.size() + b.size() - 1);
  Mul(a.data(), a.size(), b.data(), b.size(), c.data(), c.size());
  upd(c);
  return c;
}
Poly operator + (const Poly &a, const Poly &b) {
  Poly c(max(a.size(), b.size()));
  for (size_t i = 0; i < max(a.size(), b.size()); i++) {
    c[i] = ((i < a.size() ? a[i] : 0) + (i < b.size() ? b[i] : 0)) % mod;
  }
  upd(c);
  return c;
}
Poly operator - (const Poly &a, const Poly &b) {
  Poly c(max(a.size(), b.size()));
  for (size_t i = 0; i < max(a.size(), b.size()); i++) {
    c[i] = ((i < a.size() ? a[i] : 0) - (i < b.size() ? b[i] : 0) + mod) % mod;
  }
  upd(c);
  return c;
}
Poly operator / (const Poly &a, const Poly &b) {
  static ll tmp[N];
  int tmplen;
  Div(a.data(), a.size(), b.data(), b.size(), tmp, tmplen);
  Poly c = Poly(tmp, tmp + tmplen);
  upd(c);
  return c;
}
Poly operator % (const Poly &a, const Poly &b) {
  static ll tmp[N];
  int tmplen;
  Div(a.data(), a.size(), b.data(), b.size(), tmp, tmplen);
  Mul(b.data(), b.size(), tmp, tmplen, tmp, a.size());
  Poly c = Poly(tmp, tmp + a.size());
  c = a - c;
  upd(c);
  return c;
}
Poly Derivative(const Poly &a) {
  Poly b;
  for (size_t i = 1; i < a.size(); i++) {
    b.pb(a[i] * i % mod);
  }
  if (!b.size()) b.pb(0);
  return b;
}

Poly s[N << 2];
ll ans[N];
ll a[N];

inline void Pre(int p, int l, int r) {
  if (l == r) {
    s[p] = Poly{mod - a[l], 1};
    return;
  }
  int mid = (l + r) >> 1;
  Pre(p << 1, l, mid);
  Pre(p << 1 | 1, mid + 1, r);
  s[p] = s[p << 1] * s[p << 1 | 1];
}

inline void Solve(const Poly &f, int p, int l, int r) {
  if (l == r) {
    ans[l] = f[0];
    return;
  }
  int mid = (l + r) >> 1;
  Solve(f % s[p << 1], p << 1, l, mid);
  Solve(f % s[p << 1 | 1], p << 1 | 1, mid + 1, r);
}

int n;

int main() {
  NTTinit();
  n = read() + 1;
  for (int i = 0; i < n; i++) {
    a[i] = read();
  }
  Pre(1, 0, n - 1);
  Poly f = Derivative(s[1]);
  Solve(f, 1, 0, n - 1);
  ans[n - 1] = (mod - ans[n - 1]) % mod;
  for (int i = 0; i < n - 1; i++) {
    printf("%lld ", ans[n - 1] * qpow(ans[i], mod - 2) % mod);
  }
}
```

