---
title: Dimension in LaTeX
tags: glue distance
categories: LaTeX
math: false
date: 2024-08-25 00:00:00
---



## 距离的使用
除了默认已经定义的距离外，可以使用:

* `\hspace{<length>}`: 产生可断行的距离, 在某些只有一边有内容的地方（如强制断行的行首），LaTeX 会忽略 `\hspace` 产生的距离
* `\hspace*{<length>}`: 阻止距离被忽略

LaTeX 中预定义的一些长度可以使用 `\setlength` 来修改, 如:
```latex
\setlength{\parindent}{4em}
```

上述的代码将段落的缩进设置为4em.


## glue
单词间的空格、标点后的距离都是橡皮长度，这样才能保证在分行时行末的对齐，因此在定义经常出现的距离时应该使用橡皮长度. 上述的`\hspace` 就可以产生随内容可伸缩的距离:

```latex
\hspace{2em plus 1em minus 0.25em}
```

有一种特殊的橡皮长度`\fill`，它可以从零开始无限延伸, 可以用它来把几个内容均匀排列在一行之中. 

```latex
\hspace{\fill} = \hfill
```

`\fill` 命令可以和 `\stretch` 命令一起使用，如:

```LaTeX
left\hspace{\stretch{2}} $2/3$ \hspace{\fill}right
```
上述代码 $2/3$ 左侧的空白是右侧空白的两倍.还有两个比较有意思的命令:
```latex 
\hrulefill
\dotfill
```

分表表示水平线和点的填充.

## Phantom
LaTeX 有预定义的垂直支架 `\strut`，占有当前字号大小的**高度**和**深度**. 有时也可以使用长或宽为零的标尺盒子来表示任意大小的支架. 即 `\rule{0pt}{4em}` 表示一个4em高的`strut`.


