---
title: Codeforces Round 972 (Div. 2) 
date: 2024-09-18 00:00:00 +0800
categories: [OI]
tags: [Codeforces, dp]
math: true
---

[比赛传送门](https://codeforces.com/contest/2005)

### A. Simple Palindrome

题意：构造一个长度为 $n$ 仅含 `aeiou` 的字符串，最小化回文子串的数量。

显然尽可能让 `aeiou` 均摊 $n$。

那么显然接下来要么扎堆要么挨个排。

如果挨个排，那么扎堆自己组成的回文串依然存在，并且会新增其他的回文串；而自己扎堆只会产生关于自己的回文串。所以我们有理由断言，应该扎堆排。

Code

```cpp
int n, s[] = {'a', 'e', 'i', 'o', 'u'};
void mian()
{
    read(n);
    F(i, 1, 5)
        F(j, 0, n / 5 + (i < n % 5))
            putchar(s[i]);
    endline;
    return ;
}
```

### B. The Strict Teacher

题意：$1 \sim n$ 的位置，$m$ 个老师分别在 $a_i$ 的各自不同的位置上，$q$ 次询问，$awa$ 依次在 $q_i$ 的位置上，每次 $awa$ 和老师都能动一格或不动，求老师抓住 $awa$ 的最短时间。$n\leq 10^9,m,q\leq10^5$。

显然是 $awa$ 两边（或一边）的老师来抓，排序后二分查找并简单模拟即可。$O((m+q)\log m)$。

注意 `lower_bound` 是大于等于，`upper_bound` 是严格大于（本题无关紧要，$q_i\neq a_j$）；且返回值是地址，需要减去数组地址得到下标。

Code

```cpp
int n, m, q, pos[maxn];
int qry(int p)
{
    int r = upper_bound(pos + 1, pos + 1 + m, p) - pos;
    int l = r - 1;
    if(l < 1)       return pos[r] - 1;
    else if(r > m)  return n - pos[l];
    else            return pos[r] - pos[l] >> 1;
}
void mian()
{
    read(n, m, q);
    F(i, 1, m)
        read(pos[i]);
    sort(pos + 1, pos + 1 + m);
    F(i, 1, q)
        write('\n', qry(readint()));
    return ;
}
```

### C. Lazy Narek

题意：$n$ 个长为 $m$ 的字符串，取若干个（可为零）字符串拼到一起，设其含有 $a$ 个不相交子串 `narek`，不包含在前者的字符 `n` `a` `r` `e` `k` 分别出现次数之和为 $b$ ，最大化 $5a-b$ 并输出。$n,m\leq 10^3, \sum n\times m \leq 10^6$。

贪心显然寄，这是因为两个分开的串可以通过前后相连得到更大的价值。

那么、来 dp 吧！

令 `a[] = {'n', 'a', 'r', 'e', 'k'}`，设 $f_{i,j}$ 表示到第 $i$ 行，匹配到 $a_j$ 的最大价值。

转移呢，就是从上一个已经匹配的点转移过来去取最大值。不过注意，我们一旦匹配上就算加分，但是如果最后没有拼成功呢，就要减去 $2j$ 的分数（这是因为本来应该扣分的算成加分了）。

$$f_{i, j} = \max\left\{f_{i, j}, f_{i - 1, from} + \operatorname{calc}(i, k)\right\}$$

其中，$i$ 是行数，$j$ 是当前转移的下标，$from$ 是从哪里开始转移，$k$ 是列数，$\operatorname{calc}(i,k)$ 是计算第 $i$ 行前 $k$ 个字符的贡献。

转移是 $O(1)$ 的，所以时间复杂度是 $O(nm)$，空间复杂度是 $O(n)$ 的。

不过——真的有必要开二维的数组吗？注意到行与行之间只是单向转移的，那我们直接滚动数组喵。

这样又出现了问题，我不会重复转移吗？从 $i$ 到同一个 $j$，$j$ 加了两次，那么 $\operatorname{calc}(i,k)$ 的“前 $k$ 个”是保证不了的。

所以我们考虑用另一个数组来存结果是什么，修改结果仅在这个数组上修改，这就保证了不会先转移一次再用转移后的结果转移。

这次用一下 `vector` 的构造函数喵，因为好覆盖。

Code

```cpp
int n, m;
char s[1005][1005];
const string t = "narek";
int fnd(char k)
{
    F(i, 0, 4)
        if(t[i] == k)   
            return i;
    return -1;
}
void mian()
{
    read(n, m);
    F(i, 1, n)
        scanf("%s", s[i] + 1);
    vector<int> dp(5, -inf), cpy;
    dp[0] = 0;
    F(i, 1, n)
    {
        cpy = dp;
        F(j, 0, 4)
        {
            if(dp[j] == -inf)
                continue;
            int cnt = 0, idx = j;
            F(k, 1, m)
            {
                int p = fnd(s[i][k]);
                if(p == -1)
                    continue;
                if(p == idx)
                    cnt ++,                  
                    idx = (idx + 1) % 5;
                else
                    cnt --;
            }
            eqmax(cpy[idx], dp[j] + cnt);
        }
        dp = cpy;
    }
    int ans = 0;
    F(i, 0, 4)
        eqmax(ans, dp[i] - 2 * i);
    write('\n', ans);
    return ;
}
```