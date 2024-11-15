---
title: Dynamic Programming
date: 2024-09-15 00:00:00 +0800
categories: [Knowledge]
tags: [dp]
math: true
---

### 0x01 [P10287 最长不下降子序列](https://www.luogu.com.cn/problem/P10287)

题意：求出在 DAG 上的最长不下降子序列。$n,m\leq 10^5,1\leq A_i \leq 10$。

首先，最长不下降子序列怎么求？

设计状态：

1. $f_i$ 表示到点 $i$，最长不下降子序列的最长长度。
   不行。每条边转移一次，更新一次的时间复杂度为 $O(n)$，总的时间复杂度为 $O(nm)$，TLE。
2. 注意 $1 \leq A_i \leq 10$ 的条件，可以使 $A_i$ 成为第二维（比较经典的做法），即 $f_{i,j}$ 表示以到点 $i$， 结尾为 $j$ 的最长不下降子序列的长度。

考虑转移：

1. 当前点不参与最长子序列的构建，平移前面的即可。
   $$f_{v,i} \gets \max\left\{f_{v, i}, f_{u, i}\right\} (i = 1,2,\cdots,10) $$
2. 当前点参与最长子序列的构建，可以从前面不超过 $A_v$ 的地方转移。
    $$f_{v,A_v} \gets \max\left\{f_{v,A_v}, \max\limits_{i=1}^{A_v}f_{u,i} + 1\right\}$$

转移顺序：对于一个数列，显然要从数列左边向右边转移，因为后面的节点不能比前面先转移，否则会出问题。

因为这是一个 DAG 图，所以我们可以跑拓扑排序，能够满足“在前面的先转移的条件”。

拓扑排序的过程是，统计所有入度为零的点，把他们和他们连出的边删去，重复这个过程直到所有都删完，删除的顺序即为拓扑序。依次转移即可。

初始状态：自己本身就是序列，$f_{u, A_u} = 1$。

最终答案：

$$ans=\max\limits_{1\leq i \leq n, 1\leq j \leq 10}f_{i, j}$$

Code

```cpp
signed main()
{
    read(n, m);
    F(i, 1, n) read(a[i]);
    F(i, 1, m)
    {
        int u, v; read(u, v);
        deg[v] ++;
        addedge(u, v);
    }
    F(i, 1, n) 
        f[i][a[i]] = 1;
    queue<int> q;
    F(i, 1, n) 
        if(deg[i] == 0) 
            q.push(i);
    while(!q.empty())
    {
        int u = q.front(); q.pop();
        for(int i = head[u]; i; i = e[i].nxt)
        {
            int v = e[i].v;
            F(j, 1, a[v]) eqmax(f[v][a[v]], f[u][j] + 1);
            F(j, 1, 10)   eqmax(f[v][j], f[u][j]);
            if(-- deg[v] == 0)  
                q.push(v);
        }
    }
    int ans = 0;
    F(i, 1, n)
        F(j, 1, 10)
            eqmax(ans, f[i][j]);
    write('\n', ans);
    return 0;
}
```

### 0x02 [P7928 Kamenčići](https://www.luogu.com.cn/problem/P7928) 

题意：$n$ 块石头排成一行，两人轮流取走两端中的一端的石头，谁先取出 $k$ 块红色石头谁输，两人绝顶聪明，求先手的输赢。$1\leq k < n \leq 350$。

乱搞思路：贪心可以吗？如果一边红一边蓝，必然取蓝色；但如果相同，试着找到能让对方碰到红色的方案——不过这个思路是假的，因为忽视了**后续的影响**。

设计状态：发现可以拆解子问题，设 $f_{l,r,q}$ 为当前进行到 $[l,r]$ 的区间，当前的人已经拿了 $q$ 个红色石头，能否获胜。（为什么不用分先手和后手两个人转移？）

考虑转移：博弈经典转移，**先手必胜就是有方法使后手必败**。有了区间大小，有了先手取了 $q$ 的红色石子，立即得到后手取了 $p$ 的红色石子。

$$f_{l,r,q} = \lnot f_{l+1,r,p} \lor \lnot f_{l,r-1,p}$$

$p$ 可以用前缀和加速计算。转移要用记忆化搜索。

边界条件：$f_{l,r,k}=0$。

最终答案：$f_{1, n,0}$。

Code

```cpp
bool f(int l, int r, int q)
{
    if(rem[l][r][q] != -1)
        return rem[l][r][q];
    if(q == k)
        return (rem[l][r][q] = false);
    return (rem[l][r][q] = (!f(l + 1, r, sum[n] - sum[r] + sum[l - 1] - q) || !f(l, r - 1, sum[n] - sum[r] + sum[l - 1] - q))); 
}
signed main()
{
    read(n, k);
    scanf("%s", a + 1);
    memset(rem, -1, sizeof(rem));
    F(i, 1, n)
        sum[i] = sum[i - 1] + (a[i] == 'C');
    puts(f(1, n, 0) ? "DA" : "NE");
    return 0;
}
```

### 0x03 [P1826 猴子选大王数据再加强版](https://www.luogu.com.cn/problem/P1826) 

题意：约瑟夫问题，每次数到 $m$ 淘汰猴子，求 $n=a,a+1, \cdots ,b$ 时哪只猴子成为获胜者的次数多。

dp 的前提是最优子问题，那么 $n$ 的情况能否从 $n-1$ 转移过来？

注意到把第一个人杀了就是 $n-1$ 的情况了，那么答案由此转移。也就是说，第 $m+1$ 个猴子会成为 $n-1$ 情况中的第一个猴子。

边界条件：$f_0 = 1$。这里把第一个人看做第 $0$ 个人，方便取模。

转移方程：

$$f_i = (m + f_{i-1}) \bmod n$$

最后用一个桶把结果装起来即可。

Code

```cpp
int f[maxm], t[maxm];
signed main()
{
    int a, b, m;
    read(a, b, m);
    f[1] = 0;
    F(i, 2, b)
        f[i] = (m + f[i - 1]) % i;
    F(i, a, b)
        t[f[i] + 1] ++;
    int maxx = 0;
    vector<int> x;
    F(i, 1, b)
    {
        if(t[i] > maxx)
            maxx = t[i],
            x.clear(),
            x.push_back(i);
        else if(t[i] == maxx)
            x.push_back(i);
    }
    write('\n', maxx);
    for(auto i : x)
        write(' ', i);
    return 0;
}
```

### 0x04 [CF2005C Lazy Narek](https://codeforces.com/contest/2005/problem/C) 

见 [Codeforces Round 972 (Div. 2)](https://awapwq233.github.io/posts/Codeforces-Round-972/#c-lazy-narek)。

### 0x05 [P2519 Problem A](https://www.luogu.com.cn/problem/P2519) 

题意：一次考试有 $n$ 个人，第 $i$ 个人说有 $a_i$ 个人的分数**严格**大于我，$b_i$ 个人的分数**严格**小于我，求最少有几个人没说真话。

注意到和我分数相等的人有 $n-a_i-b_i$ 个。**将这些排名放到数轴上**，令 $l_i = a_i + 1, r_i = n - b_i$，则我的名次在 $[l_i,r_i]$ 这个区间中。

试着从一些必假的话开始呢？

1. $l_i>r_i$。这等价于 $n-a_i-b_i < 1$，显然必假。
2. 对于 $[l_i,r_i]$ 相同的人，最多只有 $r_i - l_i + 1$ 个人说了真话。

顺着「排名数轴」这个思路继续往下想，把 $l,r$ 相同的人合并到一起，定义这个区间的价值 $v_i$ 为这样的人数，那么问题就转化为了，对于 $m$ 个区间 $[l_i,r_i]$，价值为 $v_i$，取出若干个没有交集的区间最大化价值之和。

这样就可以背包 dp 了。转移到第 $i$ 个区间时，要么不选，要么选，但要找到使得 $r_k$ 最大的 $k$ 使得 $r_k < l_i$，保证了没有交集。于是对于 $r$ 为第一关键字排序，保证 $r_i$ 单调递增就可以二分查找了；而 $f_i$ 是不减的，找到这样最大的 $k$ 就保证了 $f_i$ 的最优。

转移方程：

$$f_i=\max\left\{f_{i-1}, f_k + v_i\right\}$$

注意边界：$f_1=v_1$。$i=1$ 时肯定选了最优嘛。

注意这样求出来的是说真话的人数，最终答案是 $n-f_m$。

Code

```cpp
int n, m, f[maxn];
struct Node
{
    int l, r, v;
} s[maxn], t[maxn];
bool cmp1(Node &x, Node &y) {return x.l == y.l ? x.r < y.r : x.l < y.l;}
bool cmp2(Node &x, Node &y) {return x.r == y.r ? x.l < y.l : x.r < y.r;}
int findk(int l, int r, int L)
{
    while(l < r)
    {
        int mid = l + r >> 1;
        if(t[mid].r < L) l = mid + 1;
        else             r = mid - 1;
    }
    return r;
}
int main()
{
    read(n);
    F(i, 1, n)
    {
        s[i].l = readint() + 1;
        s[i].r = n - readint();
    }
    sort(s + 1, s + n + 1, cmp1);
    F(i, 1, n)
    {
        if(s[i].l > s[i].r)
            continue;
        if(s[i].l != t[m].l || s[i].r != t[m].r)
            t[++ m] = s[i], t[m].v = 1;
        else if(t[m].v < s[i].r - s[i].l + 1)
            t[m].v ++;
    }
    sort(t + 1, t + m + 1, cmp2);
    f[1] = t[1].v;
    F(i, 2, m)
        f[i] = qmax(f[i - 1], f[findk(1, i - 1, t[i].l)] + t[i].v);
    write('\n', n - f[m]);
    return 0;
}
```
### 0x06 [P6820 Two Cakes](https://www.luogu.com.cn/problem/P6820)

题意：两个长为 $n$ 的排列 A 和 B，同时从左往右写，一个时间内不能同时写相同的数，最小化总用时并输出。$n \leq 10^6$。

不可能贪心，直接朴素 dp：设 $f_{i,j}$ 为 A 写完第 $i\sim n$ 位，B 写完第 $j\sim n$ 位的最小用时，显然有转移方程

$$f_{i,j} = \begin{cases}f_{i+1,j+1}+1\quad a_i \neq b_j\\\min\left\{f_{i+1,j}, f_{i,j+1}\right\}+1\quad a_i=b_j\end{cases}$$

显然的，这样的时间复杂度是 $O(n^2)$ 的，必然超时；所以考虑优化。

注意到，占时间复杂度的大头是第一种情况，第二种情况只会出现 $n$ 次（记忆化搜索即可），不是主要优化对象，那么我们考虑能否优化第一种情况？

具体的，我们发现可以找到这样的 $k$，使得 $\forall p \in [i, i + k], q\in [j, j + k]$，有 $ a_p \neq b_q$，这样便可以连续按第一种情况转移 $k$ 次，大大优化了时间复杂度。

于是我们可以记录 $a_i=b_j$ 时下标的差，即 $i-j$，放进 `vector` 后排序序号，转移时二分查找即可找到满足条件的 $k$，总时间复杂度 $O(n\log n)$。

Code
```cpp
int n, a[maxm], b[maxm], pos[maxm], dp[maxm];
vector<int> v[maxm << 1];
int f(int x, int y)
{
    if(x > n) return n - y + 1;
    if(y > n) return n - x + 1;
    if(a[x] == b[y])
        return dp[x] != -1 ? dp[x] : dp[x] = qmin(f(x + 1, y), f(x, y + 1)) + 1;
    int t = n + x - y;
    int pos = lower_bound(v[t].begin(), v[t].end(), x) - v[t].begin();
    if(pos > int(v[t].size()) - 1)
        return qmax(n - x + 1, n - y + 1);
    int k = v[t][pos];
    return f(k, y - x + k) + k - x;
}
signed main()
{
    memset(dp, -1, sizeof(dp));
    F(i, 1, n = readint()) 
        pos[a[i] = readint()] = i;
    F(i, 1, n) 
        v[n + pos[b[i] = readint()] - i].push_back(pos[b[i]]);
    F(i, 1, n << 1)
        sort(v[i].begin(), v[i].end());
    write(f(1, 1));
    return 0;
}
```

### 0x07 [P3478 STA-Station](https://www.luogu.com.cn/problem/P3478) 

题意：给定一个 $n$ 个点的树，请求出一个结点，使得以这个结点为根时，所有结点的深度之和最大。$n\leq 10^6$。

考虑树状 dp。对于 $1$ 为根节点的树，求它的节点深度和是 $O(n)$ 的。考虑向其他节点转移。

设当前节点为 $v$，父亲为 $u$，则 $v$ 子树内（包含 $u$）的部分的深度 $-1$，其他部分的深度 $+1$ 。总的来说$\Delta f=-\operatorname{size} u+(n-\operatorname{size} u)$

那么转移方程就是

$$f_v=f_u +n- 2\operatorname{size} u$$

$\operatorname{size} u$ 可以 $O(n)$ 预处理出来。转移也是 $O(n)$ 的，因而总时间复杂度为 $O(n)$。

Code

```cpp
int fa[maxm], dep[maxm], sz[maxm], f[maxm];
void dfs1(int u, int fat)
{
    f[1] += dep[u] = dep[fat] + 1; sz[u] = 1; fa[u] = fat;
    for(int i = head[u]; i; i = e[i].nxt)
    {
        int v = e[i].v;
        if(v == fat) continue;
        dfs1(v, u);
        sz[u] += sz[v];
    }
}
void dfs2(int u, int fat)
{
    if(u != 1) f[u] = f[fat] - sz[u] + n - sz[u];
    for(int i = head[u]; i; i = e[i].nxt)
    {
        int v = e[i].v;
        if(v == fat) continue;
        dfs2(v, u);
    }
}
struct Ans
{
    int i, f;
    bool operator>(const Ans &ano) const {return f > ano.f;}
} ans;
signed main()
{
    read(n);
    F(i, 1, n - 1)
    {
        int u, v; read(u, v);
        addedge(u, v); addedge(v, u);
    }
    dfs1(1, 0); dfs2(1, 0);
    ans = {1, f[1]};
    F(i, 2, n)
        eqmax(ans, {i, f[i]});
    write('\n', ans.i);
    return 0;
}
```
### 0x08 [P6820 Two Cakes](https://www.luogu.com.cn/problem/P6820)

题意：一个以 $1$ 为根节点、大小为 $n$ 的树，断开若干条边使得分割后存在一个大小为 $p$ 的子树，最小化断边的次数。$p\leq n\leq 150$。

最暴力的想法是对于一个节点，记录每条向叶子结点的边切不切，这样显然太麻烦了。

考虑抽象化这个过程，我要得到 $k$ 的子树，要找到一个构造方案使 $\sum k_v=k$，最小化 $\sum f$。经验告诉我们，这就是背包 dp。

复习一下，背包 dp 是，对于前 $i$ 个东西，容量为 $v$ 时背包的最大价值；换算到这道题就是，到 $u$ 这个节点有 $u \to v_i$，对于前 $i$ 个叶节点，剩下的节点数为 $j$ 时断边的最小数目。于是可以设 $f_{u,i,j}$，显然有转移方程

1. 我不用 $v$ 的节点，要断掉 $u\to v$ 这条边，答案 $+1$。

$$ f_{u,i,j} = f_{u, i - 1, j} + 1 $$

2. 我用 $v$ 内 $w$ 的节点，需要断掉的边取最小值。

$$ f_{u,i,j} = \min\limits_{1\leq w \leq \operatorname{size}v}\left\{f_{u, i - 1, j-w} + f_{v, \operatorname{leafsize}v, w}\right\} $$

仿照背包 dp 的优化，我们可以把第二维滚掉，这是因为

1. $i-1$ 向 $i$ 的转移，后面不会再用到了；

2. 只要我们像背包 dp 倒叙枚举 $v$，访问到的 $v-w<v$ 就一定是之前 $i-1$ 的结果，于是就优化了第二维。

但是，现在还有一些问题。对于滚动数组后的 $f_{u, j}$，我们发现这个 $u$ 点是强制选择的——也就是需要这一个 $u$ 点来链接下面的子节点。所以答案不一定就是 $f_{1, p}$，而应对所有的 $f_{u, p}$ 取 $\max$。但是这样又出现了问题，如果我们选择了一个子节点，我还需要断掉 $fa\to u$ 这条边，所以 $f_{u,p}$ 要 $+1$。所以最终答案就是 

$$\max \left\{f_{u,p} + [u\neq 1]\right\}$$

Code

```cpp
int n, p;
struct Edge
{
    int v, nxt;
} e[maxk];
int head[maxk], cnt;
void addedge(int u, int v)
{
    e[++ cnt] = {v, head[u]};
    head[u] = cnt;
}
int sz[maxk], f[155][155];
void dfs(int u)
{
    f[u][sz[u] = 1] = 0;
    for(int i = head[u]; i; i = e[i].nxt)
    {
        int v = e[i].v;
        dfs(v);
        sz[u] += sz[v];
        UF(s, qmin(sz[u], p), 0)
        {
            f[u][s] ++;
            F(sv, 0, qmin(s - 1, sz[v]))
                eqmin(f[u][s], f[u][s - sv] + f[v][sv]);
        }
    }
}
signed main()
{
    read(n, p);
    F(i, 1, n - 1)
    {
        int u, v; read(u, v);
        addedge(u, v);
    }
    memset(f, 0x3f, sizeof(f));
    dfs(1);
    int ans = inf;
    F(i, 1, n)
        eqmin(ans, f[i][p] + (i != 1));
    write('\n', ans);
    return 0;
}
```