---
title: Codeforces Round 969 (Div. 1, Div. 2) 
date: 2024-09-06 18:30:00 +0800
categories: [OI]
tags: [Codeforces]
math: true
---


### A. Dora's Set

题意：在 $[l,r]$ 的整数中选择三个两两互质的数删掉，要求尽可能多删，求最多的删除次数。

**和质数相关，考虑奇偶性。** 显然三个数至多有一个偶数（否则gcd不小于2，矛盾）。

为了尽可能利用 $[l,r]$ 中更多的数字，考虑使用连着的数字：$ a=2n - 1, b=2n, c=2n + 1$，设 $d = \max \{\gcd(a,b), \gcd(a,c), \gcd(b,c) \}$。首先 $2 \nmid d$；其次，$d$ 必不可能存在一个因数 $q \geq 3$，因为连着的三个数的任意两个必不可能是一个 $q \geq 3$ 的倍数，这样就保证了 $d=1$。

这样就找到了方案，以四个一组。注意到如果一开始是奇数，那么第一组只需要三个一组。

时间复杂度 $O(1)$。

Code

```cpp
ans = (r - l + 1 + (l & 1)) / 4;
```

### B. Index and Maximum Value

题意：长度为 $n$ 的数组，给 $m$ 次操作，每次操作对 **值** 在 $[l,r]$ 的数 +1 或 -1，求每次操作后数组最大值。

注意到初始是最大值的位置一直会是最大值。一次操作不会使最大值变得次大，因为每次只能 +1 或 -1。

时间复杂度 $O(n+m)$。

Code

```cpp
void mian()
{
    int n, m, mx = 0; read(n, m);
    F(i, 1, n)
        eqmax(mx, readint());
    F(i, 1, m)
    {
        char op = getchar(); int l, r; read(l, r);
        if(l <= mx && mx <= r)
            mx += (op == '+') ? 1 : -1;
        write(' ', mx);
    }
    endline;
}
```

### C. Dora and C++

题意：给定一个序列 $C_i$，你可以任意次单点 $+A$ 或 $+B$，最小化极差。

考虑 $A=B$ 的情况。以 $C_1$ 为基准，可以通过 $\pm A$ 来使得 $\forall i, C_i \in [C_1, C_1 + A)$。对操作后的序列排序，枚举 $+A$ 的位置，具体的讲，设该点为 $j$，对 $i < j$ 的位置单点 $+A$ ，其余不变，计算极差，枚举 $j$ 的位置，也就是最小化 $C_{j - 1} + A - C_j$。可以在 $O(n)$ 的时间复杂度内完成。

对于 $A \neq B$ 的情况，我们设 $d =\gcd(A,B)$，由裴蜀定理，一定有 $x,y$ $\text{s.t.}$ $Ax+By=d$，也就相当于完成了单点 $+d$ 的操作。由于 $d\mid A,B$，因此将 $d$ 视为上述情形的 $A$ 是可行的。

时间复杂度 $O(n \log n + \log V)$。

Code

```cpp
void mian()
{
    int n, a, b; read(n, a, b);
    F(i, 1, n)  
        read(c[i]);
    a = gcd(a, b);
    F(i, 2, n)
    {
        int k = (c[1] - c[i]) / a;
        c[i] += (k + 1) * a;
        if(c[i] >= c[1] + a)
            c[i] -= a;
    }
    sort(c + 1, c + n + 1);
    int ans = c[n] - c[1];
    F(i, 2, n)
    {
        int mx = qmax(c[i - 1] + a, c[n]);
        int mn = c[i];
        eqmin(ans, mx - mn);
    }
    write('\n', ans);
}
```

### D. Iris and Game on the Tree

题意：给定一棵树，点权为 `0` 或 `1`。定义一个叶子的权值为：考虑从根到叶子的这条路径的点权组成的字符串，权值为其中 `01` 作为连续子串出现次数减去 `10` 作为连续子串出现次数。定义树的价值为：权值非零的叶子个数。现在一些点权变成 `?`，博弈的两人分别填充，分别最大化（先手）和最小化（后手）树的价值。求最终树的价值。

一条路径计入答案当且仅当叶子和根权值不同，所以只和根和叶子的权值有关。（`101100001` 和 `10101` 和 `11` 等价）

如果根节点确定，那么策略是显然的，交替填 `0` 和 `1`。

如果根节点不确定，看已经填过的叶子节点 `0` 和 `1` 哪个多，先手有优势。

但是，在 `0` 和 `1` 个数相等时会出错：先填根的人在面对奇数个不确定的叶子节点时会吃亏，所以不会有人先填根节点和叶子结点，考虑剩下的不确定节点的奇偶性即可。

时间复杂度 $O(n)$。

Code

```cpp
int deg[maxn];
void mian()
{
    int n = readint();
    int a0 = 0, a1 = 0, unk_leaf = 0, unk_else = 0;
    F(i, 1, n)
        deg[i] = 0;
    F(i, 1, n - 1)
    {
        int u, v; read(u, v);
        deg[u] ++; deg[v] ++;
    }
    int gc = getchar();
    int s;
    if(gc == '?')
        s = -1;
    else
        s = gc - '0';
    F(i, 2, n)
    {
        int gc = getchar();
        if(deg[i] == 1)
        {
            if(gc == '0')
                a0 ++;
            else if(gc == '1')
                a1 ++;
            else
                unk_leaf ++;
        }
        else if(gc == '?')
            unk_else ++;
    }
    if(s == 0)
        write('\n', a1 + (unk_leaf + 1) / 2);
    else if(s == 1)
        write('\n', a0 + (unk_leaf + 1) / 2);
    else
        write('\n', qmax(a1, a0) + (unk_leaf + (unk_else % 2 && a1 == a0) / 2));
}

```