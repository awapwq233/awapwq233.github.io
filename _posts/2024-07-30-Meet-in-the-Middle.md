---
title: 折半搜索(Meet in the Middle)
date: 2023-07-30 01:00:00 +0800
categories: [OI]
tags: [搜索]     # TAG names should always be lowercase
math: true
---

当 $n$ 不太大，又不能直接爆搜的情况下，我们使用 meet in the middle 的搜索技巧。

过程是，取一个 $mid$，对前后两端分别搜索，如果两端的结果拼接后满足题意，则可以计入答案。

这个方法可以将 $O(2^n)$ 的复杂度降为 $O(2^{n/2})$。

### [P2962 [USACO09NOV] Lights G](https://www.luogu.com.cn/problem/P2962)

题意： $n$ 点 $m$ 边无向图，每个点初始状态为  $0$，每次操作一个点使该点和相邻的点状态改变，求最少的所有点变为 $1$ 的操作次数，保证有解。

$n \leq 35$

爆搜 $2^{35} \approx 3e10$ 爆炸

mim $2\times 2^{18} \approx 5e5$ 拿下

取 $mid\gets n/2$，从两边枚举状态（位运算）

用 map 存到达该状态所用的最少步数

合并时按位合并即可

[Submission](https://www.luogu.com.cn/record/168796938)

__[另解] 高斯消元__ 

我不会。

### [P1466 [USACO2.2] 集合 Subset Sums](https://www.luogu.com.cn/problem/P1466)

题意：$1\sim n$整数集合，划分为两个子集，且总和相等。

$n \leq 39$

爆搜 $2^{39}\approx 5e11$ 爆炸

mim $2\times 2^{20}\approx 2e6$ 拿下

取 $mid\gets n/2$ ，从两边枚举拿/不拿，存总和为 $k$ 时能够达到的方案数


注意，可以通过剪枝优化很大一部分。

注意到 $\dfrac{n(n+1)}{4}$ 非整数时无解。

注意，前后算了两次，即 $[1\;2]\;3$ 和 $1\;2\; [3]$，最后答案要右移一位。

[Submission](https://www.luogu.com.cn/record/169378232)

__[另解] 简单背包__

我没写。