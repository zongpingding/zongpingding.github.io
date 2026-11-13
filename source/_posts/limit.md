---
title: 数列极限定义中的放缩操作
tags: [limit, sequence]
categories: Math
math: true
date: 2024-03-27 00:00:00
---

## 0. 引言
由于$|x_n-A|<\varepsilon$这个东西通常是很难解出精确解$N=N(\varepsilon)$，所以我们常常会预设$\varepsilon$或者是$N$的范围，减小$\varepsilon$抑或是增大$N$。这样做有什么道理呢？或者说是，我为什么这样可行呢？

## 1. 解答
我们常常是缩小$\varepsilon$的，例如假设:$\varepsilon<1$ ,你肯定想说，定义里边不是任意的$\varepsilon>0$吗？你预设了$\varepsilon<1$，那么假如我取 $\varepsilon=2$，那么我还能够满足如下的定义吗
$$
\forall\varepsilon>0,\exists N_0\in N^+,s.t,\forall n>N_0,|x_n-A|<\varepsilon 
$$

假如满足我的$|x_n-A|<\varepsilon$ 这个不等式的$N$取值是$N(\varepsilon_0)$ ，现在我的$\varepsilon'=2$ ，此时我仍然可以找到这样的$N(\varepsilon)$,怎么找？不就是上边的$N(\varepsilon_0)$吗.你可能会说，那么我把这个$N=N(\varepsilon_0)$带入到$|x_n-A|$中,它还满足我们的$|x_n-A|<\varepsilon'$吗？

肯定小于的？以下就是证明

$$
|x_n-A|<\varepsilon<\varepsilon^{^{\prime}}\Rightarrow|x_n-A|<\varepsilon^{^{\prime}}
$$

看，是不是就成立了.其实这个缩小$\varepsilon$，我们用的比较少，主要是增大$N$我们用的比较多


## 2. 增大N
一下就解释一下增大$N$的原理：比如我N=100时成立$|x_n-A|<\varepsilon$，我们的定义里边说的是，当$n>N$时这个不等式都是成立的。比如这里的$N>100$时这个不等式成立，那么我们把$N$放大为$N=N'>10000$.你说当$n>N$时，这个不等式成立吗？

当然成立 !

> 这里的$\varepsilon$是没有变的


## 3. 其他的方法
可能你还会看到这样处理来求取$N(\varepsilon)$的例子，即$|x_n-A|<|y_n-A|<\varepsilon$,其实这个就是常见的放大法。这个是比较好理解的，不多解释了。

## 4. 思想推广
这种方法是可以推广到函数极限，无穷级数里边去的。可以这样说，任何有关极限的东西，都可以用这个思想
