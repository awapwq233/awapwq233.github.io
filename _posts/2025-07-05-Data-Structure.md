---
title: 能力综合提升-数据结构-1
date: 2025-07-05 00:00:00 +0800
categories: [Data Structure]
tags: []
math: true
---

## 0x01 链表

## 0x02 栈

我宣布所有的中序表达式转后缀表达式都是一坨大份。

## 0x03 队列

## 0x04 并查集

```cpp
int fa[maxn];
int find(int x)
{
    return x == fa[x] ? x : (fa[x] = find(fa[x]));
}
void merge(int x, int y)
{
    fa[find(x)] = find(y);
}
```

### 例题

题意：给定一个有权无向简单图，删掉若干条边使之恰好构成两个连通块，最小化连通块内部的边权的最大值。

显然对边权排序然后从大到小删边。删掉的边的两点肯定不在同一个连通块，于是有下面两种解决方法：

- 每次删边标记敌人，本着“敌人的敌人就是朋友”原则，将这俩点合并到一起。

```cpp
int n, m, k, en[maxn];
struct Node
{
    int a, b, c;
    bool operator<(const Node &ano) const
    {
        return c > ano.c;
    }
} a[maxm];
int fa[maxn];
int find(int x)
{
    return fa[x] == x ? x : fa[x] = find(fa[x]);
}
void merge(int x, int y)
{
    fa[find(x)] = find(y); 
}
signed main()
{
    read(n, m);
    F(i, 1, n)
        fa[i] = i;
    F(i, 1, m)
        read(a[i].a, a[i].b, a[i].c);
    sort(a + 1, a + m + 1);
    F(i, 1, m)
    {
        int x = a[i].a, y = a[i].b;
        if(find(x) == find(y))
        {
            write('\n', a[i].c);
            return 0;
        }
        if(!en[x])
            en[x] = y;
        else 
            merge(en[x], y);
        if(!en[y])
            en[y] = x;
        else
            merge(en[y], x);    
    }   
    puts("0");
    return 0;
}
```

- 每回合翻面！每个点有两种状态，要么在第一个连通块要么在第二个，于是让正 A 和反 B 合并，反 A 和正 B 合并，当发现自己正反在同一个连通块里就停止。

至于代码，可以让 `i` 和 `i + n` 来分别表示 `i` 的正面和反面。注意 `fa[]` 开二倍空间。

> 事实上，更高倍的**拓展域并查集**也是可以以此类推的。
>
> 例题 [P2024 [NOI2001] 食物链](https://www.luogu.com.cn/problem/P2024) A 吃 B，B 吃 C，C 吃 A，三倍拓展的并查集。

### 例题

题意：给定一颗树，树有边权，定义两个点之间的相关性为两点的简单路径上的边权最小值。每次询问一个点的价值，定义价值为所有与该点相关性不小于 $k_i$ 的节点个数，不包括本身。$Q\leq 10^5$。

**StudyingFather** 写的太好了我直接贺了。

题目并不要求**强制在线**，我们可以将询问按 $k$ 的值降序排序后**离线**解决本题。

我们用并查集维护连通块，对于每个询问，我们将边权大于等于给定的边权 $k$ 的边加入图中，对于一个点 $v$，满足条件的点数为其所在连通块大小 $−1$（不包括这个点本身）。

```cpp
int n, q;
struct Node
{
    int u, v, w;
    bool operator<(const Node &ano) const
    {
        return w > ano.w;
    }
} edge[maxn];
struct Query
{
    int id, k, v, ans;
    Query() : id(0), k(0), v(0), ans(0) {}
} qry[maxn];
bool cmp1(const Query &q1, const Query &q2)
{
    return q1.k > q2.k;
}
bool cmp2(const Query &q1, const Query &q2)
{
    return q1.id < q2.id;
}
int fa[maxn];
int siz[maxn];
int find(int x)
{
    return fa[x] == x ? x : fa[x] = find(fa[x]);
}
void merge(int x, int y)
{
    int u = find(x);
    int v = find(y);
    fa[u] = v;
    siz[v] += siz[u];
}
int size(int x)
{
    return siz[find(x)] - 1;
}
signed main()
{
    read(n, q);
    F(i, 1, n)
        fa[i] = i, siz[i] = 1;
    F(i, 1, n - 1)
        read(edge[i].u, edge[i].v, edge[i].w);
    F(i, 1, q)
    {
        read(qry[i].k, qry[i].v);
        qry[i].id = i;
    }
    sort(edge + 1, edge + n);
    sort(qry + 1, qry + q + 1, cmp1);
    int j = 1;
    F(i, 1, q)
    {
        while(j < n && edge[j].w >= qry[i].k)
        {
            merge(edge[j].u, edge[j].v);
            j ++;
        }
        qry[i].ans = size(qry[i].v);
    }
    sort(qry + 1, qry + q + 1, cmp2);
    F(i, 1, q)
        write('\n', qry[i].ans);
    return 0;
}
```

一遍过很开心！

### 例题

题意：给定图，$k$ 次询问，每次询问删除一个点，查询删除后的连通块个数。

很容易想到每次删除判断是困难的。正难则反，考虑倒过来，从最后一个受影响的点开始生成并连边，每次用并查集合并判断连通块（注意没有生成的点也是单独的连通块，需减去）。这样的做法要求必须是离线的。

```cpp
int n, m, k;
int tar[maxn], idx[maxn], fa[maxn], ans[maxn];
struct Edge
{
    int u, v;
    int appear() const
    {
        return qmin(idx[u], idx[v]);
    }
    bool operator<(const Edge &ano) const
    {
        return appear() > ano.appear();
    }
} e[maxn << 1];
int find(int x)
{
    return fa[x] == x ? x : fa[x] = find(fa[x]);
}
signed main()
{
    read(n, m);
    F(i, 1, n)
        fa[i] = i;
    int cnt = n;
    F(i, 1, m)
    {
        e[i].u = readint() + 1;
        e[i].v = readint() + 1;
    }
    read(k);
    F(i, 1, k)
        tar[i] = readint() + 1;
    F(i, 1, k)
        idx[tar[i]] = i;
    F(i, 1, n)
        if(!idx[i])
            idx[i] = k + 1;
    idx[tar[k + 1]] = k + 1;
    sort(e + 1, e + m + 1);
    int j = 1;
    UF(i, k + 1, 1)
    {
        while(j <= m && e[j].appear() >= idx[tar[i]])
        {
            int x = find(e[j].u), y = find(e[j].v);
            if(x != y)
            {
                cnt --;
                fa[x] = y;
            }
            j ++;
        }
        ans[i] = cnt - (i - 1);
    }
    F(i, 1, k + 1)
        write('\n', ans[i]);
    return 0;
}
```


## 0x05 二叉堆

这是什么？请支持 `priority_queue`。

## 0x06 ST表

直接贺了 OI-Wiki。

ST 表（Sparse Table，稀疏表）是用于解决 **可重复贡献问题** 的数据结构。

### 什么是可重复贡献问题？

**可重复贡献问题** 是指对于运算 $\circ$，满足 $x\circ x=x$，则对应的区间询问就是一个可重复贡献问题。例如，最大值有 $\max(x,x)=x$，$\gcd$ 有 $\operatorname{gcd}(x,x)=x$，所以 RMQ 和区间 GCD 就是一个可重复贡献问题。像区间和就不具有这个性质，如果求区间和的时候采用的预处理区间重叠了，则会导致重叠部分被计算两次，这是我们所不愿意看到的。另外，$\circ$ 还必须满足结合律才能使用 ST 表求解。

### 原理

ST 表基于 **倍增** 思想，可以做到 $O(n\log n)$ 预处理，$O(1)$ 回答每个询问。但是不支持修改操作。

基于倍增思想，我们考虑如何求出区间最大值。可以发现，如果按照一般的倍增流程，每次跳 $2^i$ 步的话，询问时的复杂度仍旧是 $O(\log n)$，并没有比线段树更优，反而预处理一步还比线段树慢。

我们发现 $\max(x,x)=x$，也就是说，区间最大值是一个具有「可重复贡献」性质的问题。即使用来求解的预处理区间有重叠部分，只要这些区间的并是所求的区间，最终计算出的答案就是正确的。

如果手动模拟一下，可以发现我们能使用至多两个预处理过的区间来覆盖询问区间，也就是说询问时的时间复杂度可以被降至 $O(1)$，在处理有大量询问的题目时十分有效。

### 预处理

令 $f(i,j)$ 表示区间 $[i,i+2^j-1]$ 的最大值。

显然 $f(i,0)=a_i$。

根据定义式，第二维就相当于倍增的时候「跳了 $2^j-1$ 步」，依据倍增的思路，写出状态转移方程：$f(i,j)=\max(f(i,j-1),f(i+2^{j-1},j-1))$。

### 查询

对于每个询问 $[l,r]$，我们把它分成两部分：$[l,l+2^s-1]$ 与 $[r-2^s+1,r]$，其中 $s=\left\lfloor\log_2(r-l+1)\right\rfloor$。两部分的结果的最大值就是回答。


根据上面对于「可重复贡献问题」的论证，由于最大值是「可重复贡献问题」，**重叠并不会对区间最大值产生影响**。又因为这两个区间完全覆盖了 $[l,r]$，可以保证答案的正确性。


```cpp
constexpr int logn = 16;
constexpr int maxn = 1e5 + 10;
int n, m, k;
int st[maxn][logn + 1];
int Log2(int x)
{
    return 31 - __builtin_clz(x);
}
void build()
{
    F(i, 1, n)
        read(st[i][0]);
    F(j, 1, logn)
        F(i, 1, n - (1 << j) + 1)
            st[i][j] = qmin(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
}
int query(int l, int r)
{
    int k = Log2(r - l + 1);
    return qmin(st[l][k], st[r - (1 << k) + 1][k]);
}
signed main()
{
    read(n, m);
    build();
    F(i, 1, m)
    {
        int l = readint(), r = readint();
        write('\n', query(l, r));
    }
    return 0;
}
```

ST 表虽然不可修改，但是每次在最后插入数字的做法不会影响之前的处理，因此这种情况下也是可以的。

***来自后期的 awa***：注意！上面的 st 表是左端点优先的，也就是 $f(i,j)$ 表示区间 $[i,i+2^j-1]$ 的最大值。但是每次向右插入的时候，$i+2^j-1$ 是固定的，每次需要变化 $i$，这样就造成了困难。不如我们让它变成右端点优先，也就是 $f(i,j)$ 表示区间 $[i-2^j+1,i]$，于是每次更新固定 $i$，变化 $j$ 即可。

判断终止条件就是 $i-2^j+1\geq 1$。

合并就是 $f(i,j)=\max(f(i,j-1),f(i-2^{j-1},j-1))$。

查询就是 $ans=\max(f(r, k), f(l + 2^k - 1,k))$

```cpp
constexpr int logn = 20;
constexpr int maxn = 2e5 + 10;
int n, m, k, p, cnt;
int st[maxn][logn + 1];
inline int Log2(int x)
{
    return 31 - __builtin_clz(x);
}
inline int query(int l, int r)
{
    int k = Log2(r - l + 1);
    return qmax(st[r][k], st[l + (1 << k) - 1][k]);
}
inline void add(int x, int i)
{
    st[i][0] = x;
    for(int j = 1; i - (1 << j) >= 0; j ++)
        st[i][j] = qmax(st[i][j - 1], st[i - (1 << (j - 1))][j - 1]);
}
signed main()
{
    int cnt = 0;
    read(n, p);
    while(n --)
    {
        if(readchar() == 'Q')
            write('\n', m = query(cnt - readint() + 1, cnt));
        else
            add((1ll * m + readint() + p) % p, ++ cnt);
    }
    return 0;
}
```

## 0x07 树状数组


树状数组是一种支持 **单点修改** 和 **区间查询** 的，代码量小的数据结构。

普通树状数组维护的信息及运算要满足 **结合律** 且 **可差分**，如加法、乘法、异或等。

结合律：$(x \circ y) \circ z = x \circ (y \circ z)$。

可差分：具有逆运算的运算，即已知 $x \circ y$ 和 $x$ 可以求出 $y$。

需要注意的是，**模意义下的乘法**若要可差分，需保证每个数都存在逆元（模数为质数时一定存在）。例如 $\gcd$，$\max$ 这些信息不可差分，所以不能用普通树状数组处理。


事实上，树状数组能解决的问题是线段树能解决的问题的子集：树状数组能做的，线段树一定能做；线段树能做的，树状数组不一定可以。然而，树状数组的代码要远比线段树短，时间效率常数也更小，因此仍有学习价值。

```cpp
int lowbit(int x)
{
    return x & -x;
}
void add(int x, int v)
{
    for (; x <= n; x += lowbit(x))
        c[x] += v;
}
int query(int x)
{
    for (; x; x -= lowbit(x))
        ans += c[x];
    return ans;
}
```
