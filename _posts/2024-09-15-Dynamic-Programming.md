---
title: Dynamic Programming
date: 2024-09-15 00:00:00 +0800
categories: [OI]
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