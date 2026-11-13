---
title: LaTeX Hook management
tags: Hook
categories: LaTeX
date: 2024-11-12 00:00:00
---



## internal hook commands
Some internal hook commands defined in LaTeX2e, for example the `\g@addto@macro` macro:

```latex
\long\def\g@addto@macro#1#2{%
  \begingroup
    \toks@\expandafter{#1#2}%
    \xdef#1{\the\toks@}%
  \endgroup}
```


## Hook Managemant
![](/img/latex_hook/0001.svg)
![](/img/latex_hook/0002.svg)
![](/img/latex_hook/0003.svg)
![](/img/latex_hook/0004.svg)
![](/img/latex_hook/0005.svg)
![](/img/latex_hook/0006.svg)
![](/img/latex_hook/0007.svg)
![](/img/latex_hook/0008.svg)
![](/img/latex_hook/0009.svg)
![](/img/latex_hook/0010.svg)
![](/img/latex_hook/0011.svg)
![](/img/latex_hook/0012.svg)
![](/img/latex_hook/0013.svg)
![](/img/latex_hook/0014.svg)
![](/img/latex_hook/0015.svg)


PDF at [LaTeX Hook management](https://github.com/zongpingding/All-About-TeX/blob/main/LaTeX_Notes/LaTeX_Hook_Management/hook.pdf), feel free to get it.
