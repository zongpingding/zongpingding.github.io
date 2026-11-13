---
title: Lp Normal in Dual Space
tags: Normal Inequality
categories: Math
math: true
date: 2024-05-16 00:00:00
---

{% note danger %}
楼红卫老师原书中定理自己理解有误, 其分子为 $\displaystyle\int_{\mathbb{R}^n}{f(x)g(x)}\;\mathrm{d}\bm{x}$, 可以不用加绝对值.只需要添加一个对应的符号函数项即可. 尽管一般的书里面写的是 $\|u\|_{L^1(\Omega)}$的形式,而这个形式里面就有绝对值，所以自己才把这个搞混了.
{% endnote %}

## 定理陈述

我们可以通过HÖlder不等式得到 $L^p$中函数的模的另一种形式, 这一形式定义如下:
$$
\|f\|_p = \sup\limits_{\substack{g\in L^q(\mathbb{R}^n)\\ \|g\|_q\neq 0}} 
\frac{\displaystyle\int_{\mathbb{R}^n}{|f(x)g(x)|}\;\mathrm{d}\bm{x}}{\|g\|_q} = 
\sup\limits_{\substack{g\in L^q(\mathbb{R}^n)\\ \|g\|_q=1}}\displaystyle\int_{\mathbb{R}^n}{|f(x)g(x)|}\;\mathrm{d}\bm{x}
$$

这里 $p, q\in(1, +\infty)$为对偶数. 这里仅证明第一个等号

## 证明

根据Holder不等式有:
$$
\begin{aligned}
\text{RHS} 
& = \sup\limits_{\substack{g\in L^q(\mathbb{R}^n)\\ \|g\|_q\neq 0}} \displaystyle\int_{\mathbb{R}^n}{|f(x)g(x)|}\;\mathrm{d}\bm{x}\bigg/\|g\|_q\\
& \le \sup\limits_{\substack{g\in L^q(\mathbb{R}^n)\\ \|g\|_q\neq 0}} \left(\displaystyle\int_{\mathbb{R}^n}{|f(x)|^p}\;\mathrm{d}\bm{x}\right)^{1/p}\cdot \left(\displaystyle\int_{\mathbb{R}^n}{|g(x)|^q}\;\mathrm{d}\bm{x}\right)^{1/q}\bigg/\|g\|_q\\
& = \|f\|_p \cdot \|g\|_q \bigg/\|g\|_q \\
& = \|f\|_p = \text{LHS}
\end{aligned}
$$

然后选取一个特殊的 $g(x) = g(_0x)$, 证明 $\text{LHS}\le \text{RHS}$, 这里取$g_0(x) = f^{p-1}\cdot \mathrm{sgn}(f)
$, 那么此时是我们有 
$$
\begin{aligned}
\|g\|_q  
& = \left(\int_{\mathbb{R}^n}{|f(x)|^{(p-1)q}}\;\mathrm{d}\bm{x}\right)^{1/q} \\
& = \left(\int_{\mathbb{R}^n}{|f(x)|^{p}}\;\mathrm{d}\bm{x}\right)^{1/q} \\
& = \left(\int_{\mathbb{R}^n}{|f(x)|^{p}}\;\mathrm{d}\bm{x}\right)^{(1/p)\cdot(p/q)} \\
& = \|f\|_p^{p-1}
\end{aligned}
$$ 

那么此时对于$\text{RHS}$有:
$$
\begin{aligned}
\text{RHS}
& \ge \int_{\mathbb{R}^n}{|f(x)g_0(x)|}\;\mathrm{d}\bm{x}\bigg/\|g_0\|_q \\
& \ge \left(\int_{\mathbb{R}^n}{|f(x)|^p}\;\mathrm{d}\bm{x}\right)^{(1/p)\cdot p}\bigg/\|g_0\|_q \\
& = \|f\|_p^p\bigg/\|f\|_p^{p-1}\\
& = \|f\|_p = \text{LHS}
\end{aligned}
$$

{% note success %}
因为 $1/p+1/q = 1$, 所以有 $pq=p+q, p/q=p(1-1/p)=p-1$.
{% endnote %}

## 对偶空间

为何本篇文章的标题中要包含`Dual Space` ? 我们可以这样看, 假设 $\|f\|_p$($g\in L^q$)是一个定义在空间$L^q$($L^p, L^q$互为对偶空间)上的线性泛函，那么此时泛函$\|f\|_p$可以看作一个线性算子，此时就回归到怎么定义线性算子模这一问题了. 此篇文章开头的 $f$的模的定义正是把 $\|f\|_p$看作一个线性算子时，算子范数的标准定义.



## 参考

* [proof of lp norm is dual to lq](https://planetmath.org/proofoflpnormisdualtolq)

* 楼红卫: 数学分析
