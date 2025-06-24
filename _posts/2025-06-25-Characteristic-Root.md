---
title: 特征根法求数列通项的一个证明
date: 2025-06-125 00:00:00 +0800
categories: [Math]
tags: [Characteristic Root]
math: true
---

考高数的时候的一道题想到的。

在高中，我们学过，欲求解满足 

$$\begin{cases}a_n=\alpha a_{n-1}+\beta a_{n-2}\\a_0=A\\a_1=B\end{cases}$$ 

的数列通项，只需要解 

$$x^2-\alpha x-\beta=0$$

得到 $x_1,x_2$ 两个根，则

$$a_n=\begin{cases}c_1x_1^n+c_2x_2^n &(x_1\neq x_2)\\(c_1n+c_2)x_1^n &(x_1=x_2)\end{cases}$$

其中 $c_1,c_2$ 是常数，代入两个初值即可。

下面给出证明。

记 

$$S(x)=\sum\limits_{n=0}^\infty \frac{a_n}{n!}x^n$$

其中 $a_n$ 满足上述条件。

$$\begin{aligned}S(x)&=\sum\limits_{n=0}^\infty \frac{a_n}{n!}x^n\\&=\frac{a_0}{0!}x^0+\frac{a_1}{1!}x^1+\sum\limits_{n=2}^\infty\frac{a_n}{n!}x^n\\&=a_0+a_1x+\sum\limits_{n=2}^\infty\frac{\alpha a_{n-1}+\beta a_{n-2}}{n!}x^n\\&=a_0+a_1x+\alpha\sum\limits_{n=1}^\infty\frac{ a_{n}}{(n+1)!}x^{n+1}+\beta\sum\limits_{n=0}^\infty\frac{a_n}{(n+2)!}x^{n+2}\\&=a_0+a_1x+\alpha F(x)+\beta G(x)\end{aligned}$$

下面考察 $F(x),G(x)$ 与 $S(x)$ 的关系。

注意到

$$F^{\prime}(x)=\sum\limits_{n=1}^\infty\frac{ a_{n}}{n!}x^{n}=S(x)-a_0$$

$$G^{\prime\prime}(x)=\sum\limits_{n=0}^\infty\frac{a_n}{n!}x^{n}=S(x)$$

于是我们只需要对上式求两次导，就有

$$S^{\prime}(x)=a_1+\alpha(S(x)-a_0)+\beta G^{\prime}(x)$$

$$S^{\prime\prime}(x)=\alpha S^{\prime}(x)+\beta S(x)$$

整理上式，有

$$S^{\prime\prime}-\alpha S^\prime-\beta S=0$$

解这个齐次线性微分方程，也就是解这个特征根方程

$$\lambda^2-\alpha\lambda-\beta=0$$

从而得到 $\lambda_1,\lambda_2$，其通解为

$$S(x)=\begin{cases}C_1e^{\lambda_1x}+C_2e^{\lambda_2x} &(\lambda_1\neq \lambda_2)\\(C_1x+C_2)e^{\lambda_1x} &(\lambda_1=\lambda_2)\\(C_1\cos qx+C_2\sin qx)e^{px} &(\lambda_{1,2}=p\pm q\mathrm i)\end{cases}$$


以第一种 $\lambda_1\neq \lambda_2$ 的情况为例，这里的 $C_1,C_2$ 是由初值 $a_0,a_1$ 得到的。


下面依次代入解出 $C_1,C_2$。令 $x=0$，有

$$\begin{cases}S(0)=C_1+C_2=a_0\\S^{\prime}(0)=\lambda_1C_1+\lambda_2C_2=a_1\end{cases}$$

使用 Cramer 法则解这个方程组，有

$$\begin{cases}C_1=\frac{\lambda_2a_0-a_1}{\lambda_2-\lambda_1}\\C_2=\frac{a_1-\lambda_1a_0}{\lambda_2-\lambda_1}\end{cases}$$

得到

$$S(x)=\frac{\lambda_2a_0-a_1}{\lambda_2-\lambda_1}e^{\lambda_1x}+\frac{a_1-\lambda_1a_0}{\lambda_2-\lambda_1}e^{\lambda_2x}$$

最精彩的来了：对 $e^{\lambda x}$ 使用幂级数展开

$$e^{\lambda x}=\sum\limits_{n=0}^\infty\frac{\lambda^n}{n!}x^n$$

就有

$$S(x)=\sum\limits_{n=0}^\infty\frac{\frac{\lambda_2a_0-a_1}{\lambda_2-\lambda_1}\lambda_1^n+\frac{a_1-\lambda_1a_0}{\lambda_2-\lambda_1}\lambda_2^n}{n!}x^n$$

比较 $S(x)$ 的定义式，立刻得到

$$a_n=\frac{\lambda_2a_0-a_1}{\lambda_2-\lambda_1}\lambda_1^n+\frac{a_1-\lambda_1a_0}{\lambda_2-\lambda_1}\lambda_2^n=C_1\lambda_1^n+C_2\lambda_2^n$$

这也就是特征根法所得到的答案。

这实在是太美丽了！特征根法之所以叫做特征根，正是因为：**这个方法的本质是求解一个二阶常系数线性微分方程**！而通过解微分方程得到的含 $e^{\lambda x}$ 的答案，通过幂级数展开后恰好对每一项进行了一一对应！

实在美丽。