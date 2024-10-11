---
title: Templates
date: 2024-10-11 11:00:00 +0800
categories: [Templates]
tags: []
math: true
---

`Code.cpp`
```cpp
// -std=c++17 -lm -O2 -Wall -Wl,-stack=2147483647
// #pragma GCC optimize(3, "Ofast", "inline")

#include <bits/stdc++.h>
using namespace std;

#define F(i, a, b) for(int i = a, i##end = b; i <= i##end; ++ i)
#define UF(i, a, b) for(int i = a, i##end = b; i >= i##end; -- i)
#define file(x) freopen(x".in", "r", stdin); freopen(x".out", "w", stdout)
typedef long long ll;
typedef double db;

constexpr int inf = 0x3f3f3f3f;
constexpr int intmax = __INT_MAX__;
constexpr ll llmax = __LONG_LONG_MAX__;
constexpr ll mod = 998244353;
constexpr db eps = 1e-7;

template < typename T > inline T qmax(const T &a, const T &b) {return a > b ? a : b;}
template < typename T, typename... Arg > inline T qmax(const T &a, const T &b, const Arg &...arg){return qmax(a, qmax(b, arg...));}
template < typename T > inline void eqmax(T &a, const T &b) {if(b > a) a = b;}
template < typename T, typename... Arg > inline void eqmax(T &a, const Arg &...arg) {int c = qmax(arg...); eqmax(a, c);}
template < typename T > inline T qmin(const T &a, const T &b) {return a < b ? a : b;}
template < typename T, typename... Arg > inline T qmin(const T &a, const T &b, const Arg &...arg) {return qmin(a, qmin(b, arg...));}
template < typename T > inline void eqmin(T &a, const T &b) {if(b < a) a = b;}
template < typename T, typename... Arg > inline void eqmin(T &a, const Arg &...arg){int c = qmin(arg...); eqmin(a, c);}
template < typename T > inline void qswap(T &a, T &b) {T c = a; a = b, b = c;}
template < typename T > inline T qabs(const T &x) {return x > 0 ? x : -x;}
template < typename T > inline void read(T &x) {x = 0; char ch = getchar(); bool f = false; while(!isdigit(ch)) {if(ch == '-') f = true; ch = getchar();} while(isdigit(ch)) x = x * 10 + ch - '0', ch = getchar(); x = f ? -x : x;}
template < typename T, typename... Arg > inline void read(T &x, Arg &...arg) {read(x); read(arg...);}
inline char readchar() {char ch = getchar(); while(!isalpha(ch)) ch = getchar(); return ch;}
inline int readint() {int x; read(x); return x;}
inline ll readll() {ll x; read(x); return x;}
template < typename T > inline void write(const T &x) {if(x > 9) write(x / 10); putchar(x % 10 + '0');}
template < typename T > inline void write(const char &ch, const T &x){if(x < 0) putchar('-'); write(qabs(x)), putchar(ch);}
template < typename T, typename... Arg > inline void write(const char &ch, const T &x, const Arg &...arg) {write(ch, x); write(ch, arg...);}
#define endline putchar('\n')
template < typename T1, typename T2, typename T3 > inline ll fastpow(T1 a, T2 b, const T3 &mod) {ll ans = 1; while(b){if(b & 1) ans = 1ll * ans * a % mod; a = 1ll * a * a % mod; b >>= 1;} return ans;}
inline int popcnt(const int &x) {return __builtin_popcount(x);}
inline int popcnt(const ll &x) {return __builtin_popcountll(x);}
inline bool eq(const db &a, const db &b){return a - b < eps && b - a < eps;}

constexpr int maxk = 1e4 + 10;
constexpr int maxn = 1e5 + 10;
constexpr int maxm = 1e6 + 10;

int n, m, k;



signed main()
{
    
    
    return 0;
}
```

---

`CodeT.cpp`

```cpp
// -std=c++17 -lm -O2 -Wall -Wl,-stack=2147483647
// #pragma GCC optimize(3, "Ofast", "inline")

#include <bits/stdc++.h>
using namespace std;

#define F(i, a, b) for(int i = a, i##end = b; i <= i##end; ++ i)
#define UF(i, a, b) for(int i = a, i##end = b; i >= i##end; -- i)
#define file(x) freopen(x".in", "r", stdin); freopen(x".out", "w", stdout)
typedef long long ll;
typedef double db;

constexpr int inf = 0x3f3f3f3f;
constexpr int intmax = __INT_MAX__;
constexpr ll llmax = __LONG_LONG_MAX__;
constexpr ll mod = 998244353;
constexpr db eps = 1e-7;

template < typename T > inline T qmax(const T &a, const T &b) {return a > b ? a : b;}
template < typename T, typename... Arg > inline T qmax(const T &a, const T &b, const Arg &...arg){return qmax(a, qmax(b, arg...));}
template < typename T > inline void eqmax(T &a, const T &b) {if(b > a) a = b;}
template < typename T, typename... Arg > inline void eqmax(T &a, const Arg &...arg) {int c = qmax(arg...); eqmax(a, c);}
template < typename T > inline T qmin(const T &a, const T &b) {return a < b ? a : b;}
template < typename T, typename... Arg > inline T qmin(const T &a, const T &b, const Arg &...arg) {return qmin(a, qmin(b, arg...));}
template < typename T > inline void eqmin(T &a, const T &b) {if(b < a) a = b;}
template < typename T, typename... Arg > inline void eqmin(T &a, const Arg &...arg){int c = qmin(arg...); eqmin(a, c);}
template < typename T > inline void qswap(T &a, T &b) {T c = a; a = b, b = c;}
template < typename T > inline T qabs(const T &x) {return x > 0 ? x : -x;}
template < typename T > inline void read(T &x) {x = 0; char ch = getchar(); bool f = false; while(!isdigit(ch)) {if(ch == '-') f = true; ch = getchar();} while(isdigit(ch)) x = x * 10 + ch - '0', ch = getchar(); x = f ? -x : x;}
template < typename T, typename... Arg > inline void read(T &x, Arg &...arg) {read(x); read(arg...);}
inline char readchar() {char ch = getchar(); while(!isalpha(ch)) ch = getchar(); return ch;}
inline int readint() {int x; read(x); return x;}
inline ll readll() {ll x; read(x); return x;}
template < typename T > inline void write(const T &x) {if(x > 9) write(x / 10); putchar(x % 10 + '0');}
template < typename T > inline void write(const char &ch, const T &x){if(x < 0) putchar('-'); write(qabs(x)), putchar(ch);}
template < typename T, typename... Arg > inline void write(const char &ch, const T &x, const Arg &...arg) {write(ch, x); write(ch, arg...);}
#define endline putchar('\n')
template < typename T1, typename T2, typename T3 > inline ll fastpow(T1 a, T2 b, const T3 &mod) {ll ans = 1; while(b){if(b & 1) ans = 1ll * ans * a % mod; a = 1ll * a * a % mod; b >>= 1;} return ans;}
inline int popcnt(const int &x) {return __builtin_popcount(x);}
inline int popcnt(const ll &x) {return __builtin_popcountll(x);}
inline bool eq(const db &a, const db &b){return a - b < eps && b - a < eps;}

constexpr int maxk = 1e4 + 10;
constexpr int maxn = 1e5 + 10;
constexpr int maxm = 1e6 + 10;

int n, m, k;



void mian()
{
    
    
    return ;
}

signed main()
{
    for(int TaskNums = readint(); TaskNums --;) mian();

    return 0;
}
```

---

`SPJ.cpp`

```cpp
#include "testlib.h"
#include <bits/stdc++.h>
using namespace std;


int main(int argc, char *argv[])
{
    registerTestlibCmd(argc, argv);

    // inf, ouf, ans

    // .readInt()
    // .readLong()
    // .readDouble()
    // .readChar()
    // .readLine()
    


    // _ok, _wa, _pc(score)
    
    if(true)
        quitf(_ok, "Accepted.");
    else
        quitf(_wa, "Wrong Answer.");
}
```

---

`Codeforces.md`

``````
## Codeforces Round  (Div. )

[比赛传送门](https://codeforces.com/contest/)

### A. 

题意：



Code

```cpp

```

### B. 

题意：



Code

```cpp

```

### C. 

题意：




Code

```cpp

```

### D. 

题意：



Code

```cpp

```

### E. 

题意：




Code

```cpp

```

### F. 

题意：



Code

```cpp

```
``````