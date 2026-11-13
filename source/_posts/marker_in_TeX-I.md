---
title: Marker in TeX - I
tags: marker
categories: LaTeX
date: 2024-06-14 00:00:00
---

## 参考
本节内容大部分都是基于 TeX SE 上的回答，然后我自己部分直接抄袭，部分按照自己的理解记录在案.

## 基本概念
LaTeX中查看两个mark的值，然后再说明这两个值都表示了什么:

* \leftmark:  `Left` argument of the `Last`  \markboth on the page
* \rightmark: `Right` argument of the ` fiRst`  \markboth or `first` \markright

一个具体的 [例子](https://tex.stackexchange.com/questions/449859/what-is-the-difference-between-leftmark-and-rightmark-in-fancyhdr):

```tex
 ==== page break, new chapter:
 chapter:     {{chapter}    {   }}
 section1:    {{chapter}    {section1}}
 section2:    {{chapter}    {section2}}
 ==== page break: leftmark: chapter, rightmark: empty

 section3:    {{chapter}    {section3}}
 section4:    {{chapter}    {section4}}
 ==== page break: leftmark: chapter, rightmark: section3
```


## marker 原理
那么 \markboth 和 \markright 这两个命令它们是什么时候运行的? 其实在展开 \chapter 命令时，下面这个命令便会自动运行，从而生成对应的 \leftmark 和 \rightmark. 参见如下的 \chaptermark 命令声明:

```latex
\def\chaptermark#1{%
  \markboth {\MakeUppercase{%
    \ifnum \c@secnumdepth >\m@ne
        \@chapapp\ \thechapter. \ %
    \fi
    #1}}{}}%
```

现在, 你可以知道你的这个chapter marker到底是怎么产生的了.

## 自定义 marker
如果你不想使用这个命令中给你的 marker，那么你也可以手动进行设置这个 \leftmarker 和 \rightmark. 主要使用下面的两个命令:

* \markboth{}{}: 用于设置 \leftmark 和 \rightmark 的值,前面为 left, 后面为 right.
* \markright{}: 用于设置 \rightmark 的值.

比如你自定义你的 left mark 和 right mark 为任意的东西: 
```latex
\markboth{left item}{right item}
```

## marker 格式
其实从 \chaptermark 命令中就可以看到，默认的 \leftmark 是 uppercase 的，想要取消这个命令，一方面可以使用 fancyhdr 提供的:

```latex
\fancyhead[CO]{\textsc{\nouppercase{\leftmark}}}
```

或者是重定义这些个 \sectionmark 或者是 \chaptermark ,一个具体的示例如下:

```latex
% 1. chapter mark
\renewcommand{\chaptermark}[1]{%
  \markboth{%
    \ifnum\c@secnumdepth>\m@ne
      \@chapapp\ {\footnotesize\thechapter}. \ %
    \fi
  #1%
  }{}%
}

% 2. section mark
\renewcommand{\sectionmark}[1]{\markboth{\thesection.\ #1}{}}
```

上面这两个只是简单的示例，具体的设置请根据你自己的情况来.
