---
title: Page-breakable Box in TeX
tags: breakable box
categories: LaTeX
date: 2024-06-15 00:00:00
---

## 引入
尽管常见的 `tcolorbox`,  `framed`, `boites` 等宏包提供了一个可跨页的盒子，但是这个跨页的原理是什么? 到底怎么实现的? TeX SE 上的如下问题: [breakable box](https://tex.stackexchange.com/questions/52943/background-color-of-breakable-box-with-thin-blank-lines) 有一个关于“可跨页盒子” 的实现代码(这里是简化后代码):

```latex
\documentclass[11pt]{article}
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{lipsum}
\pagestyle{empty}

\newbox\boxhead
\newbox\boxtail
\newbox\boxtemp

\newdimen\dimtemp

\def\boxsplit{%
  % total height of original box
  \dimtemp\ht\boxtail
  \advance\dimtemp\dp\boxtail
  % split original box in order to retrieve its first line
  \setbox\boxhead\vsplit\boxtail to 0pt
  % fix the height of the first line
  \setbox\boxhead\vbox{\unvbox\boxhead}%
  % restore original size
  \setbox\boxtemp\vbox{\copy\boxhead\copy\boxtail}%
  \advance\dimtemp-\ht\boxtemp
  \advance\dimtemp-\dp\boxtemp
  \advance\dimtemp\dp\boxhead
  \dp\boxhead\dimtemp
}

\newenvironment{mybox}{%
  \ifhmode\par\fi
  \setbox\boxtail\vbox\bgroup
}{%
  \egroup
  {%
    \noindent
    \fboxsep 0pt
    \splittopskip 0pt
    \baselineskip 0pt
    \lineskiplimit 0pt
    \lineskip 0pt
    \vfuzz\maxdimen
    \leavevmode
    \loop
      \ifvoid\boxtail
      \else
        \boxsplit
        \hbox to \linewidth{\box\boxhead}\hfil%
        \hfil
        \repeat
  }%
}

\begin{document}

\begin{mybox}
  \lipsum[1-5]
\end{mybox}

\end{document}
```

## 目标
我想要根据上述的原理，自己定义一个对应的可跨页的盒子，然后把这个盒子的声明封装为一个命令，方便在之后的模板 `zlatex` 中调用.
