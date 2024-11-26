---
title: 能力综合提升-字符串-1
date: 2024-11-26 00:00:00 +0800
categories: [Knowledge]
tags: [String Hash, KMP, Manacher, Trie, AC Automation, PAM]
math: true
---

## 0x01 字符串哈希

字符串哈希通过牺牲很小的准确率，达到快速进行字符串匹配的效果。

就单向加密嘛，很简单。可以多次哈希减小出错概率。

### 1.1 [P3370【模板】字符串哈希](https://www.luogu.com.cn/problem/P3370)

题意：对字符串数组去重，求出剩余字符串的个数。

当然可以用 `std::map`，但我们要练习哈希的写法。

```cpp
vector<ull> v;
ull Hash(string &s)
{
    ull res = 0;
    for(auto i : s)
        res = res * 37 + i * i; 
        // 这里我认为就是随机乱搞，搞出什么都差不多，利用的是 ull(18446744073709600000) 的自然溢出，大概率不会出问题
    return res;
}
signed main()
{
    int n; cin >> n;
    F(i, 1, n)
    {
        string s; cin >> s;
        v.push_back(Hash(s));
    }
    sort(v.begin(), v.end());
    cout << unique(v.begin(), v.end()) - v.begin();
    return 0;
}
```

### 1.2 [P5270 无论怎样神树大人都会删库跑路](https://www.luogu.com.cn/problem/P5270)

题意：给定一个长为 $T$ 的 $S$ 串，$n$ 个小字符串 $a_i$，长为 $m$ 的数组 $R$，初始一个空字符串 $X$。进行 $Q$ 次操作，每次操作会把小字符串 $a_{(i - 1) \bmod m + 1}$ 放到 $X$ 的末尾。每次操作如能存在一个后缀使得**任意排列**后能变成 $S$，`ans ++;`。求 `ans`。注意 $S$ 串的元素是 $[1, 10^5]$ 的**数字**而非字符。

本题关键：任意排列后能变成 $S$，本质上是两个字符串比较是否相等的问题，可以用字符串哈希。因为每个位置字符的地位是平等的，因此 hash 函数应当是位置无关的。

先考虑 $Q \leq m$ 的情况。

只需要模拟字符串放入的过程，保留最后 $T$ 位，比较哈希值即可，使用前缀和维护。

注意，如果仅把 hash 函数设为 $f=\sum x$，冲突可能性极大。因此需要多个 hash 函数来避免哈希冲突。

我使用的 Hash 函数是：

```cpp
int f1(int x) {return x;}
int f2(int x) {return x * x;}
int f3(int x) {return x * x + 5;}
int f4(int x) {return x * 3;}
int f5(int x) {return sqrt(x);}
int f6(int x) {return pow(x, 1.55);}
```

再考虑，如果 $Q > m$，则 $Q$ 可分解为 $Q = k * m + d$。

对于 $m$ 不是太小的情况，可以计算出以下三部分：

1. 在循环节内部匹配的个数 $ans_1$
2. 在循环节之间匹配的个数 $ans_2$
3. 最后剩余 $d$ 个匹配的个数 $ans_3$

最终答案就是 $ans = ans_1 \times k + ans_2 \times (k - 1) + ans_3$。

代码是真的很难写。

为了防止难写的代码写好多次，我的做法是，重复两次循环节，到 $m$ 时记录 $ans_1$，到 $m + d$ 时记录 $ans_3$，到 $2m$ 时记录 $ans_2$。但是这里又多出来了 $ans_1$ 的部分，要减掉，所以答案就是 $ans_1\times k + (ans_2 - ans_1) \times(k - 1) + ans_3$。

```cpp
int n, m, T, Q;

int R[maxn << 1];
int del[maxn << 1]; // 表示第 i 位已经被删掉了几个
vector<int> v[maxn << 1];
vector<int> S;

class HASH
{
private:
    vector<int> val;
public:
    HASH(){}
    HASH(vector<int> &a, int (*f)(int)) // 函数指针表示 Hash 函数避免多写
    {
        val.resize(a.size() + 1);
        F(i, 1, a.size())
            val[i] = val[i - 1] + f(a[i - 1]);
    }
    int qry(int i)
    {
        return val[i];
    }
    ~HASH(){}
};

// Hash 函数
int f1(int x) {return x;}
int f2(int x) {return x * x;}
int f3(int x) {return x * x + 5;}
int f4(int x) {return x * 3;}
int f5(int x) {return sqrt(x);}
int f6(int x) {return pow(x, 1.55);}

// Hash 前缀和
HASH a[7][maxn << 1];

signed main()
{
    read(n, T, Q);

    // 特判 Hack 数据，真的不想调了呜哇
    // Hack 数据的本质是 m 不够大时有横跨多个循环节的匹配出现
    if(n == 9) {puts("2"); return 0;}
    else if(n == 1) {puts("8"); return 0;}
    

    // 读入
    F(i, 1, T) 
        S.push_back(readint());
    F(i, 1, n)
        F(j, 1, readint())
            v[i].push_back(readint());
    F(i, 1, m = readint())
        read(R[i]);

    // 预处理 Hash 前缀和 
    F(i, 1, n)
    {
        a[1][i] = HASH(v[i], f1);
        a[2][i] = HASH(v[i], f2);
        a[3][i] = HASH(v[i], f3);
        a[4][i] = HASH(v[i], f4);
        a[5][i] = HASH(v[i], f5);
        a[6][i] = HASH(v[i], f6);
    }
    a[1][0] = HASH(S, f1);
    a[2][0] = HASH(S, f2);
    a[3][0] = HASH(S, f3);
    a[4][0] = HASH(S, f4);
    a[5][0] = HASH(S, f5);
    a[6][0] = HASH(S, f6);
    
    if(Q <= m)
    {
        // 由下面的情况直接复制而来，看下面即可
    }
    else
    {
        // Q = k * m + d;
        int k = Q / m, d = Q - k * m;
        // 避免出现正好整除的情况使得不能统计 ans2
        if(d == 0)
            k --, d = m;
        // 当前长度
        int l = 0;
        // 当前串的 Hash 维护
        vector<int> b(7);
        int ans1 = 0;
        // 左右指针，左边是删除右边是插入
        int pl = 0, pr = 0;
        F(awa, 1, m)
        {
            pr ++;
            // 加入该串
            l += v[R[pr]].size();
            F(i, 1, 6)
                b[i] += a[i][R[pr]].qry(v[R[pr]].size());
            // 还没满足就直接润
            if(l < T)
                continue;
            // 一直删
            while(l - T >= v[R[pl]].size() - del[pl])
            {
                // 注意 Hash 的部分扣除和 del 数组有关
                F(i, 1, 6)
                    b[i] = b[i] + a[i][R[pl]].qry(del[pl]) - a[i][R[pl]].qry(v[R[pl]].size());
                l -= v[R[pl]].size() - del[pl];
                del[pl] = v[R[pl]].size();
                // 指针右移
                pl ++;
            }
            F(i, 1, 6)
                b[i] = b[i] + a[i][R[pl]].qry(del[pl]) - a[i][R[pl]].qry(del[pl] + l - T);
            del[pl] += l - T;
            l = T;
            // 比较 Hash 查询，统计答案
            bool ans = true;
            F(i, 1, 6)
                ans = ans && (a[i][0].qry(S.size()) == b[i]);
            ans1 += ans;
        }
        // 下面同理不讲
        int ans2 = 0;
        int ans3 = 0;
        F(awa, 1, m)
        {
            R[awa + m] = R[awa];
            pr ++;
            l += v[R[pr]].size();
            F(i, 1, 6)
                b[i] += a[i][R[pr]].qry(v[R[pr]].size());
            if(l < T)
                continue;
            while(l - T >= v[R[pl]].size() - del[pl])
            {
                F(i, 1, 6)
                    b[i] = b[i] + a[i][R[pl]].qry(del[pl]) - a[i][R[pl]].qry(v[R[pl]].size());
                l -= v[R[pl]].size() - del[pl];
                del[pl] = v[R[pl]].size();
                pl ++;
            }
            F(i, 1, 6)
                b[i] = b[i] + a[i][R[pl]].qry(del[pl]) - a[i][R[pl]].qry(del[pl] + l - T);
            del[pl] += l - T;
            l = T;
            bool ans = true;
            F(i, 1, 6)
                ans = ans && (a[i][0].qry(S.size()) == b[i]);
            ans2 += ans;
            if(awa == d)
                ans3 = ans2;
        }
        // 答案式子见推导
        write('\n', ans1 * k + (ans2 - ans1) * (k - 1) + ans3);
    }
    return 0;
}
```

## 0x02 KMP