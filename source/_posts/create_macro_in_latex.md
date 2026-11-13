---
title: Create Macro in LaTeX 
tags: [parameter, long macro]
categories: LaTeX
math: True
date: 2024-06-14 00:00:00
---

## long macro
### LaTeX 2e
在 LaTeX2e 中可以看到下面这样的命令:

```latex
\newcommand*
\renewcommand*
```

那么这两个带`*`的命令和传统的 \newcommand 命令有什么区别？其实就是long macro和macro的区别:

* \newcommand* 定义的 macro 不是 \long 的，意味着你不能在宏参数中包含 \par,空行等东西
* 然而常用的 \newcommand 就可以包含 \par, 空行等的元素

和传统的 \def 命令的区别如下:

```latex
\def <=> \newcommand*
\long\def <=> \newcommand
```

### LaTeX 3
同样的在LaTeX3中也有类似的操作:

```latex
\cs_new_nopar:Npn 
\cs_set_nopar:Npn 
```

其实这个 `_nopar` 的 signature 就表示这个 macro 所含的接受的参数中不能含有 \par 命令，和前面的 \newcommand*是类似的. 什么时候需要用这些个命令呢? 那就看你的设计意图了.

## p-signature in \cs_new:Npn 
对于在 $\rm\LaTeX 3$中创建新的函数，往往有下面两种方式:
``` LaTeX
\cs_new:Nn \foo:nn {}
\cs_new:Npn \foo:nn #1#2 {}
```

那么我们到底需要用 `p-signature` 来显示的声明我们所创建的命令 `\foo:nn` 接受的参数个数吗? 

其实是不需要的，下面以 `\foo:nn` 命令进行说明。其实 `\foo:nn` 命令后面的两个 `n-signature` 就表示这个命令会吃掉两个参数, 而且在声明过程中也可以直接用 #1, #2来分别表示接收到的参数. 但是我个人还是比较喜欢第二种方式.

> 其实还有一种比较奇怪的声明方式: `\cs_new:Npn \foo {}`, 因为 "The functions with `p` allow for `non-colon` macro names, the other ones must have a colon at the end". 一个具体的示例:
> ```latex
> \cs_gset_nopar:Npn \world(#1) {WORLD=>~|#1|~|#1|}
> \world (world)AAA
> % RESULT:
> % WORLD=> |world| |world|AAA
>```
> 其实这种情况下，你就可以直接把前面的一堆 `\cs_gset_nopar:Npn` 等价成一个 `\def` 了，毕竟 `p` specifier 的含义就是 `traditional paramerter`. 更多详细信息可以参见: [A LaTeX3 new macro/function definition problems](https://tex.stackexchange.com/q/350908/294585)
