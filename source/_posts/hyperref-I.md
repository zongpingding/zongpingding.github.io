---
title: 使用Hyperref进行精确跳转
tags: [hyperref, label/ref]
categories: LaTeX
math: true
date: 2024-05-29 00:00:00
---


## 什么是精确跳转
在 $\LaTeX$ 中使用 `\label-\ref` 一组命令用于标签的跳转时往往会出现下面的情况:
> 我命令在某一行定义的这个label，但是为什么在后文ref引用后，点击跳转到了一个很奇怪的地方，而不是我自己定义label的所在行

其实这个并不是 $\LaTeX$ 的问题，你可以理解为这个label激活的位置是与这个便签最邻近的如下元素:
* chpater/section/subsection 等元素
* 由`\newtheorem` 声明的定理类环境位置

然后你点击后就会跳转到上述的"奇怪"的位置

## 方法
但是总归是有方法的，其实就是使用宏包 `hyperref`, 使用它提供的如下两个命令
* `\hypertarget` 进行label的定义
* `\hyperlink` 进行跳转操作


一个完整的示例如下
``` latex 
\documentclass{ctexart}
\usepackage{zhlipsum}
\usepackage{hyperref}

\begin{document}
\section{Test 1}
\zhlipsum[1-10]

\noindent\rule{1\linewidth}{2pt}
\hypertarget{label-1}{\textbf{Destination}}

\zhlipsum[1-10]

\section{Test 2}
\zhlipsum[3-4]
\hyperlink{label-1}{\textbf{ClickMe}}
\end{document}
```
