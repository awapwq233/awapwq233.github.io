---
title: 浅谈代码风格
date: 2024-10-22 17:00:00 +0800
categories: [OI]
tags: [Code-Style]
math: true
---

### 0x00 前言

众所周知，在进行代码的编写时，我们不可避免的拥有自己的代码风格，但是为了以下等原因，你或许需要规范自己的代码风格：

1. 便于自己理解

2. 便于团队里其他人理解

3. 自己看得爽

4. **方便他人帮你调试代码**

以下是一些较为常见的风格分析，可以根据重要程度（星数）规范自己的写法。

### 0x01 代码块的缩进 [★★★]

`C++` 和 `python` 不一样，`python` 严格强调缩进，并使用缩进来划定代码块；而 `C++` 使用大括号来划定。这也导致了我们甚至可以把很长的代码压缩到一行。这必然导致了阅读和理解的困难，而良好的缩进可以让读者迅速找到逻辑层次关系，这非常有意义。具体的，我们在大括号内缩进，说明当前内容是在一个代码块内，使用 `Tab` 键插入制表符（默认四个空格的长度）或者 `Space` 键插入四个空格（也存在两个或者三个的情况）。

在 `Dev-C++` 中，对一个未展开的大括号回车会自动完成换行并缩进。

在 `Vscode` 中，对一个未展开的大括号回车会自动完成换行并缩进；按下 `Shift + Tab` 可以反向缩进。

```cpp
while(true)
{
    cout << "awa";
    cout << "pwq";
    cout << "233" << endl;
}
```

特别的，当代码块内仅有一个语句时，通常有以下三种写法：

```cpp
while(true) cout << "awapwq233" << endl;

while(true)
    cout << "awapwq233" << endl;

while(true)
{
    cout << "awapwq233" << endl;
}
```

第一种最简洁，但是不能表现层次，常用于后面的式子较为简单时（例如 `i ++`）；

第二种我个人推荐，保留了层次，相对简洁；

第三种初学者推荐，保证了即使再在里面插入内容也不会导致含义的改变，但是大括号占行显得相对冗长。

但是，我们有一个约定俗成的特例：`else if` 语句。实际逻辑应当是这样的：

```cpp
if(A)
{
    a;
}
else 
{
    if(B)
    {
        b;
    }
    else 
    {
        if(C)
        {
            c;
        }
        else
        {
            d;
        }
    }
}
```

但在实际过程中，通常种种情况是并列的，这样的缩进恐怕不能体现并列的特点，所以现在的写法通常都是：

```cpp
if(A)
{
    a;
}
else if(B)
{
    b;
}
else if(C)
{
    c;
}
else
{
    d;
}
```

另外，如果在合理的范围内缩进能够给你或者其他人带来美观，而你又想这样做，那就这样做。具体看以下例子：

```cpp
F(i, 2, m)
{
    int l = b.x - b.y - a.x - a.y >> 1;
    if(a.x - b.x == a.y - b.y) 		f[i] = (f[i - 1] + (a.y ? 0 : g[i - 1])) % mod;
    else if(a.x - b.x == b.y - a.y) g[i] = (f[i - 1] + (g[i - 1])) % mod;
    else if(l < 0) 					g[i] = (f[i - 1] + (a.y ? 0 : g[i - 1])) % mod;
    else if(l == 0)					f[i] = (f[i - 1] + (g[i - 1])) % mod,
                                    g[i] = (f[i - 1] + (a.y ? 0 : g[i - 1])) % mod;
    else 
    {
        int k = fastpow(2, l - 1, mod);
        if(b.y) f[i] = 1ll * ((f[i - 1] << 1) + g[i - 1]) * k % mod;
                g[i] = 1ll * ((f[i - 1] << 1) + g[i - 1]) * k % mod;
    }
    if(g[i] || b.y == 0) ans = qmax(ans, b.y, b.x + b.y - a.x + a.y >> 1);
}
```

### 0x02 大括号换行 [★★]

有 **悬挂** 和 **换行** 两种写法。其实这根本无关紧要，只是大家都爱在这个地方争吵（笑）

悬挂：节省了左大括号的一行，放在上一行末尾。

```cpp
while(true){
    cout << "awapwq233" << endl;
}
```

换行：换行，使左右大括号对齐。
```cpp
while(true)
{
    cout << "awapwq233" << endl;
}
```

注意：**这根本无关紧要！** 你喜欢用哪个就用哪个。不过通常情况下人们会更喜欢调自己喜欢风格的代码。

### 0x03 变量的命名 [★★★]

我们显然不喜欢这样的代码：

```cpp
void _(___________ &__)
{
    ll ___ = __.____________('.');
    if (___ != ___________::_____________)
    {
        ll ____ = __._______________() - 1;
        while (____ > ___ && __[____] == '0')
        {
            ____--;
        }
        if (____ == ___)
        {
            ____--;
        }
        __ = __._________________(0, ____ + 1);
    }
}
```

但是根据 C++ 标准，它是正确的、可以经过编译、运行出正确结果的程序。因为在系统底层，变量名是无意义的，仅做区分使用；而对于人工阅读代码来说，一个好的变量名能够快速被读者读懂，增加代码的可读性，便于读者理解，也有利于自己的编写和维护。

那么，如何写出一个好的变量名呢？

1. **不要用拼音！** 拼音的信息熵极低，会导致理解的困难和读音相似的变量名的重复。

2. **一些约定俗成的变量名（竞赛常见）**：
   1. `a[i]`：`array` 的首字母 `a` 用来代表数组。
   2. `n` `m`：通常指代数组长度。
   3. `i` `j` `k`：临时变量，常用于 `for` 循环中。
   4. `ans`：`answer`，答案。
   5. `res`：`result`，结果。
   6. `cnt`：`count`，计数。
   7. `tmp`：`temporary`，临时变量。
   8. `ptr`：`pointer`，指针。
   9. `s` `str`：`string`，字符串。
   10. `p` `mod`：`module`，模数。
   11. `u` `v`：节点 $u$ 和节点 $v$，通常情况下有一条 $u\to v$ 的边。
   12. `v` `val` `w`：`value`，权值，价值。
   
3. **英文的缩写**：类似 2.4 ~ 2.10 的变量命名，只要自己（他人）能够理解即可。

4. **英文全拼**：常见于项目，多人合作、反复迭代，必须要让别人理解（加上注释更好）。

5. **不要用纯下划线！** （`for` 循环用完了临时变量倒是可以，如果并没有很重要的意义）

### 0x04 运算符间空格 [★]

在一切的运算符前后加上空格。比较这两段代码：

```cpp
for(int i = 1; i <= n; i ++)
{
    cout << "awapwq233" << endl;
}

for(int i=1;i<=n;i++)
{
    cout<<"awapwq233"<<endl;
}
```

注意到 `=` `<=` `++` `<<` 都是运算符，可以在前后增加空格，使得代码更美观（因人而异，有些人不觉得），而代价是增加了敲空格的时间。

特别的，`;` 的前面不要加空格，因为它标志着语句的末尾；而后面加空格，能更好地分割下一条语句。

### 0x05 函数化编程 [★★]

如果你能够把代码拆分成若干个功能，那么你最好把每一块分到单独的函数里。这是因为：

1. 能够更加明确每一部分代码的功能；

2. 方便调试每一部分的代码；

3. 防止部分代码与部分代码之间共用一个变量导致出错。

代价是时间的消耗。如果时间紧迫则不必这样做；反之，如果时间充足或者在进行一个项目，这样做是必要的。

### 0x06 简化的表达 [★★]

如果你的函数要传入 5 个 `long long` 类型的参数，那你的写法通常会是：

```cpp
long long f(long long a, long long b, long long c, long long d, long long e);
```

这显然太长了。不妨

```cpp
#define ll long long // 把所有的 ll 在编译时替换为 long long
```

或者

```cpp
typedef long long ll; // 给 long long 一个别名叫做 ll
```

那么原来的代码就可以变成

```cpp
ll f(ll a, ll b, ll c, ll d, ll e);
```

显然小了很多。

通常情况下我们采用第二种方法，这是因为第一种写法在偷懒 `#define int ll` 时，`int(x)` 会被宏定义成 `long long(x)`，第一个 `long` 被直接分开了（应当为 `(long long)(x)`），而第二种写法不会有此问题。

> 不要用 `#define int ll`。

其他常见的还有 `double -> db`，`pair<int, int> -> pii`，`make_pair(x, y) -> mp(x, y)`，`priority_queue -> pq`，`push_back(x) -> pb(x)` 等等。

### 0x07 常量的使用 [★]

```
constexpr double e = exp(1.0);
constexpr double pi = acos(-1.0);
constexpr double eps = 1e-7;
```
> 在 `C++11` 中新增了关键字 `constexpr` 用于常量的声明。写 `const` 也是没毛病的。

常见的常量： 
1. $e=\exp (1) = e^1$；
   
2. $\cos \pi=-1\Rightarrow \pi = \arccos(-1)$；
   
3. $\epsilon = 10^{-7}$ 因为 `double` 类型在运算时有浮点误差。


