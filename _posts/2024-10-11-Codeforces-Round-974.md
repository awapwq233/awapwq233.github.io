---
title: Codeforces Round 974 (Div. 3) 
date: 2024-10-11 12:00:00 +0800
categories: [OI]
tags: [Codeforces, dp, Xor-Harshing]
math: true
---
[比赛传送门](https://codeforces.com/contest/2014)

### E. Rendez-vous de Marian et Robin 

题意：有边权无向图，两人从两个起点出发，在某些节点有使自己速度加倍的道具（永久生效），求两人碰头的最短时间。$n,m\leq 2\times 10^5$。保证边权为偶数。

显然是分层图，在这些特殊节点向下层连一条有向边，下层的边权减半。

跑两遍 `Dijkstra`，枚举节点计算答案最小值。

$w_i\leq 10^6$，因此记得用 `long long`。

Code

```cpp
#define int ll
int n, m, h;
struct Edge
{
    int v, w, nxt;
} e[maxm << 1];
int head[maxm << 1], cnt;
void addedge(int u, int v, int w)
{
    e[++ cnt] = {v, w, head[u]};
    head[u] = cnt;
}
bool vis[maxn << 2];
int d1[maxn << 2], d2[maxn << 2];
struct Node
{
    int v, dis;
    Node(int V, int D){v = V, dis = D;}
    bool operator<(const Node &ano) const {return dis > ano.dis;}
};
priority_queue<Node> q;
void mian()
{
    read(n, m, h);
    cnt = 0;
    F(i, 1, n << 1)
        head[i] = 0;
    F(i, 1, h)
    {
        int u = readint();
        addedge(u, u + n, 0);
    }
    F(i, 1, m)
    {
        int u, v, w;
        read(u, v, w);
        addedge(u, v, w);
        addedge(v, u, w);
        addedge(u + n, v + n, w >> 1);
        addedge(v + n, u + n, w >> 1);
    }
    while(!q.empty()) q.pop();
    F(i, 1, n << 1) vis[i] = 0, d1[i] = llmax;
    d1[1] = 0;
    q.push(Node(1, 0));
    while(!q.empty())
    {
        auto x = q.top(); q.pop();
        int u = x.v;
        if(vis[u]) continue;
        vis[u] = true;
        for(int i = head[u]; i; i = e[i].nxt)
        {
            int v = e[i].v, w = e[i].w;
            if(d1[v] > d1[u] + w)
            {
                d1[v] = d1[u] + w;
                q.push(Node(v, d1[v]));
            }
        }
    }
    if(d1[n] == llmax)
    {
        puts("-1");
        return ;
    }
    while(!q.empty()) q.pop();
    F(i, 1, n << 1) vis[i] = 0, d2[i] = llmax;
    d2[n] = 0;
    q.push(Node(n, 0));
    while(!q.empty())
    {
        auto x = q.top(); q.pop();
        int u = x.v;
        if(vis[u]) continue;
        vis[u] = true;
        for(int i = head[u]; i; i = e[i].nxt)
        {
            int v = e[i].v, w = e[i].w;
            if(d2[v] > d2[u] + w)
            {
                d2[v] = d2[u] + w;
                q.push(Node(v, d2[v]));
            }
        }
    }
    ll ans = llmax;
    F(i, 1, n)
        eqmin(ans, qmax(qmin(d1[i], d1[i + n]), qmin(d2[i], d2[i + n])));
    write('\n', ans);    
    return ;
}
```

### F. Sheriff's Defense

题意：一个数，每个点有一个权值，选择树上的若干个点，代价是这个点直接相连的点的权值 $v_i\gets v_i-c$（可多次），最大化选出的点的权值之和。$n\leq 2\times 10^5,-10^9\leq w_i\leq 10^9$。

考虑树上 dp。最优子问题是显然的，重点看选择的代价如何处理，即无后效性。假设下面已经处理好了，到当前节点，我要不要-c取决于下面的字节点有没有选择；而我上面的节点等到后面再处理就可以了。

设 $f_u$ 为当前节点不选的答案，$g_u$ 为当前节点选的答案，因为对当前状态来说，只有子节点选没选是重要的，因此需要状态的第二维。

$$f_u=\sum\limits_{u\to v} \max (f_v,g_v)$$

$$g_u=w_u+\sum\limits_{u \to v}\max (f_v,g_v-2c)$$

$-10^9\leq w_i\leq 10^9$，因此记得用 `long long`。

Code

```cpp
#define int ll
int n, m, c;
struct Edge
{
    int v, nxt;
} e[maxn << 2];
int head[maxn << 1], cnt;
void addedge(int u, int v)
{
    e[++ cnt] = {v, head[u]};
    head[u] = cnt;
}
int a[maxn << 1], f[maxn << 1][2];
void init()
{
    read(n, c);
    cnt = 0;
    F(i, 1, n)
        read(a[i]), head[i] = f[i][0] = f[i][1] = 0;
    F(i, 1, n - 1)
    {
        int u, v; read(u, v);
        addedge(u, v), addedge(v, u);
    }
}
void dfs(int u, int fa)
{
    f[u][0] = 0;
    f[u][1] = a[u];
    for(int i = head[u]; i; i = e[i].nxt)
    {
        int v = e[i].v;
        if(v == fa) continue;
        dfs(v, u);
        f[u][0] += qmax(f[v][0], f[v][1]);
        f[u][1] += qmax(f[v][0], f[v][1] - c * 2) ;
    }
}
void mian()
{
    init();
    dfs(1, 0);
    write('\n', qmax(f[1][0], f[1][1]));    
    return ;
}
```

### F. 

题意：给定一个序列，$q$ 次询问，每次给定 $l,r$，问 $[l,r]$ 的元素是否都出现了偶数次。$n,q\leq 2\times 10^5$。

一个很显然的想法是暴力 $l$ 到 $r$ 统计次数。$O(nq)$。

太慢了，用 `bitset`，答案直接异或。$O(\dfrac{nq}{\omega})$。

还是过不了。所以新芝士：`Xor Harshing`，**异或哈希**。

> 利用了异或操作的特殊性和哈希降低冲突的原理，可以用于快速找到一个组合是否出现、序列中的数是否出现了偶数次。

考虑映射 $a_i\to rd_i \in[0, 2^{64}-1]$。然后进行异或前缀和，由于 `long long` 有 $64$ 位，我们可以认为如果 $rd_{l-1}=rd_{r}$，那么序列中的数确实出现了偶数次。

[`mt19937` 的用法](https://awapwq233.github.io/posts/mt19937/)。


Code

```cpp
int n, q;
ll a[maxn << 1], rd[maxn << 1];
ll rnd() {return (1ll * rand() << 47) | (1ll * rand() << 31) | (1ll * rand() << 15) | (rand());}
map<int, ll> mp;
set<ll> used;
void mian()
{
    read(n, q);
    mp.clear();
    used.clear();
    F(i, 1, n)
        read(a[i]);
    F(i, 1, n)
    {
        if(mp[a[i]])
            a[i] = mp[a[i]];
        else
        {
            ll r = rnd();
            while(used.find(r) != used.end())
                r = rnd();
            used.insert(r);
            mp[a[i]] = r;
            a[i] = r;
        }        
    }
    F(i, 1, n)
        rd[i] = rd[i - 1] ^ a[i];
    F(i, 1, q)
    {
        int l, r; read(l, r);
        puts(rd[r] == rd[l - 1] ? "YES" : "NO");
    }
}
```

另解：莫队算法，我不太会喵。时间复杂度 $O((n+q)\sqrt n)$。
