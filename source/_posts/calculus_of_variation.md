---
title: calculus_of_variation
index_img: /img/calculus_of_variation/banner.png
banner_img: /img/calculus_of_variation/banner.png
tags: Math
math: true
date: 2024-03-26 00:00:00
---


## 问题提出
针对变分问题：
$$
\left\{\begin{aligned}
& G\bigl[y(x)\bigr]=\int_{x_0}^{x_1}F(x, y, y')dx \\
& y(x_0) = y_0,\quad y(x_1) = y_1 
\end{aligned}\right.
$$

根据`变分法基本原理` 
$$
\delta G = \int_{x_0}^{x_1}\left(F_y - \dfrac{ {\rm d}F_{y'} }{ {\rm d}x }\right)\cdot\delta y\;\mathrm{d}x = 0
\tag{1}
$$

## 拉格朗日引理
然后这里给出一个`拉格朗日引理`

> 拉格朗日引理(Лемма Лагранжа)：若对于每个连续函数 $\eta(t)$, 有$\displaystyle\int_{t_0}^{t_1}\Phi(t)\eta(t)dt=0$ ,这里 $\Phi(t)$ 在区间 $[t_0, t_1]$ 上连续，则在该区间上 $\Phi(t) \equiv 0$


## E-L方程

于是根据上面给出的拉格朗日引理，我们可以得到`E-L方程`

$$
F_y - \dfrac{\mathrm{d} F_y'}{\mathrm{d}x} = 0 
\tag{2}
$$

对上述的`E-L方程`展开可以得到:
$$
\begin{aligned}
F_y - \dfrac{\mathrm{d}F_y'}{\mathrm{d}x} 
& = F_y - \left(
\dfrac{\partial F_{y'}}{\partial x}\cdot \dfrac{\partial {x}}{\partial x} + \dfrac{\partial F_{y'}}{\partial y}\cdot \dfrac{\partial {y}}{\partial x} +
\dfrac{\partial F_{y'}}{\partial y'}\cdot \dfrac{\partial {y'}}{\partial x}
\right) \\
& = F_y - F_{y'x} - y'F_{y'y} - y''F_{y'y'} \\
& = 0
\end{aligned}
$$
可以得到`E-L方程`的另一种形式
$$
F_y - F_{y'x} - y'F_{y'y} - y''F_{y'y'} = 0
$$









