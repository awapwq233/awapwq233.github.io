---
title: Codeforces Round 973 (Div. 2) 
date: 2024-09-30 00:00:00 +0800
categories: [OI]
tags: [Codeforces]
math: true
---


[比赛传送门](https://codeforces.com/contest/2013)

CDE 写不出来真是给他烂完了。更多的蔬菜。

### A. Zhan's Blender

题意：一个栈每秒最多可以弹出 $x$ 个数，我每秒可以最多往栈里放 $y$ 个数，求栈弹出 $n$ 个数最短时间。

显然我使劲往里面放就行了，栈使劲弹出就行了。

$$ans=\left\lceil\dfrac{n}{\min(x,y)}\right\rceil$$

Code

```cpp
ans = n / qmin(x, y) + !!(n % qmin(x, y));
```

### B. Battle for Survive

题意：长为 $n$ 的数字序列，进行 $n-1$ 次操作，每次操作选取 $1\leq i < j\leq n$，使 $a_j \gets a_j - a_i$，并删除 $a_i$，求最后剩余的数的最大值。

显然的，最后剩余的数一定是 $a_n$。

考虑其他所有数对 $a_n$ 的贡献。我们一定希望他们的值能够做正贡献，但一次选取是负贡献，所以他们的值需要两次才能变成正的，这样就考虑牺牲 $a_{n-1}$，把 $a_1 \sim a_{n-2}$ 的值都减到它身上，再用 $a_n$ 去减它，这样我们可以得到最大化贡献的值。

$$ans=\sum\limits_{i=1}^{n}(-1)^{[i=n-1]}a_i$$

Code

```cpp
void mian()
{
    ll x = 0;
    F(i, 1, readint())
        x += readll() * (i == iend - 1 ? -1 : 1);
    write('\n', x);   
    return ;
}
```

### C. Password Cracking

题意：交互题。长度为 $n$ 的 `0`/`1` 串，$2n$ 次询问某 `0`/`1` 串是否是它的子串，求出这个 `0`/`1` 串。

$\texttt{Sunzh}$：$2n=2(n-1)+1+1$，做完了。

考虑从任意点开始，暴力往右检查，无法向右时向左。

第一个点代价 $1$，右边的 $k$ 个点代价 $2$，碰到头 $2$，左边的点代价 $1$。

$k\neq n-1$ 时，

$$f=1+2k+2+(n-k-1)=n+k+2\leq n+n-2+2=2n$$

$k=n-1$ 时，无需碰到头的代价 $2$，  

$$f=1+2k=2n-1$$

无论如何可以通过。

Code

```cpp
void mian()
{
    memset(a, 0, sizeof(a));
    read(n);
    int l = n + 1, r = n + 1;
    a[l] = 1;
    if(!qry(l, r)) 
    {
        l = 1, r = n;
        goto end;
    }
    while(r - l + 1 < n)
    {
        if(qry(l, r + 1)) r ++;
        else
        {
            a[r + 1] = 1;
            if(qry(l, r + 1)) r ++;
            else if(r - l + 1 == n) goto end;
            else break;
        }
    }
    while(r - l + 1 < n)
        if(!qry(-- l, r))
            a[l] = 1;
    end:;
    out(l, r);
    return ;
}
```