---
title: LaTeX Font
tags: [NFSS, glue]
categories: LaTeX
math: true
date: 2024-08-24 00:00:00
---


{% note success %}
本篇文章的绝大部分内容均来自于: [**刘海洋: $\LaTeX$ 入门**](https://book.douban.com/subject/24703731/). 这里仅仅只是本人的一个摘要和整理, 部分内容是原文的抄袭
{% endnote %}


## 字体变体
我们常用的 `\bfseries\itshape` 其实表示的就是字体变体，然而在老版本中不同轴上的变体不能合并，但是在`NFSS(新字体选择方案)`中就可以用不同轴上的变体进行融合.那么有那些轴呢? 可以参见: [读懂LaTeX的 font warning](https://zongpingding.github.io/2024/06/14/font_warning/). 

因为中文的变体一般是很少的，所以可以把字体的变体用另一种字体来代替。这也就是中文的粗体变为黑体的原因了.

> 当可缩放的矢量字体流行起来以后，字体只有一款，通过缩放达到不同的尺寸，字号也就经常作独立于字体的单独性质了。

有一个比较有用的宏包: `fonttable`, 可以用于输出字体的符号表: 使用方法如下:
```latex 
\usepackage{fonttable}
\fonttable{<fontname>}
\xfonttable{<encoding>}{<family>}{<series>}{<shape>}
```


## 强调字体
我们常用的`\emph`，`\underline` 其实是强调命令，而非其他. 此时可以引用一个比较实用的宏包: `ulem`. `CJKfntef` 宏包也对汉字提供了类似的功能,同时也进行了一些扩充. 可以使用 `fntef` 选项调用 `CJKfntef`，此时 `\emph` 的定义不会被改变为下画线格式。同时也可以使用 `\CTEXunderline` 等以`\CTEX` 开头的命令代替以 `\CJK` 开头的命令.


## 宏包内置字体命令
部分的字体宏包可能还内置了一部分的字体命令，用于设置字体的某个属性，这里以字体宏包 `Alegreya `为例，这个中提供了：

* `\ AlegreyaExtraBold`: 提供`extra bold`字重
* `\AlegreyaSansLight`: 提供`sans`族，`light`字重


所以在进行字体某个轴上的属性设置时，有必要参见对应的宏包说明。其实这写命令在此宏包中的声明为:
```latex
% package: Alegreya.sty
\newfontfamily\AlegreyaExtraBold
  [ Numbers = {\Alegreya@figurealign,\Alegreya@figurestyle},
    UprightFont    = *-ExtraBold ,
    ItalicFont     = *-ExtraBoldItalic ,
    BoldFont       = *-ExtraBold ,
    BoldItalicFont = *-ExtraBoldItalic ,
  ]{Alegreya}

% package: AlegreyaSans.sty
\newfontfamily\AlegreyaSansLight
  [ Numbers = {\AlegreyaSans@figurealign,\AlegreyaSans@figurestyle},
    UprightFont    = *-Light ,
    ItalicFont     = *-LightItalic ,
    BoldFont       = *-Light ,
    BoldItalicFont = *-LightItalic ,
  ]{AlegreyaSans}
```

## TDS 字体
想要使用一些新的字体，又不想安装，不同也不想在声明字体组时写上对应的 `Path=./Fonts/` 之类的，那么就可以利用 TeXLive 的 TDS (TeX Directory Structure) 结构， 把字体放在如下的文件中:

* `~/texmf/fonts/truetype/`: 针对 TrueType 字体，也就是以 `.ttf` 结尾的字体
* `~/texmf/fonts/opentype/`: 针对 OpenTrueType 字体，也就是以 `.otf` 结尾的字体

然后运行命令:

```shell
sudo fc-cache -fsv
```

刷新字体缓存，之后你就可以不用写这个路径了, 就可以像字体放在项目文件夹那样调用了, 如下:

```latex
\newfontfamily{\Cinzel}{CinzelRegular.ttf}[
  BoldFont=CinzelBold.ttf,
  ItalicFont=SabonItalic.ttf
]
```

这样有什么什么好处呢? 我个人觉得可以避免字体混乱，毕竟我把字体放在了当前用户（也就是我自己）的目录下.

参考文章:

* [Load font from package directory using fontspec](https://tex.stackexchange.com/a/359220/294585)
* [Using the TeX Live fonts in xelatex](https://tex.stackexchange.com/a/257242/294585)
* [XeLaTeX, fc-list, fc-cache problem](https://tex.stackexchange.com/questions/361595/xelatex-fc-list-fc-cache-problem)


## 补充
其实你在运行刷新字体缓存的命令时，可以看到有如下的输出:

```shell
Font directories:
        /usr/share/fonts
        /usr/local/share/fonts
        /usr/share/fonts/100dpi
        /usr/share/fonts/75dpi
        /usr/share/fonts/Emoji
        /usr/share/fonts/OTF
        /usr/share/fonts/TTF
        /usr/share/fonts/Windows_Fonts
        /usr/share/fonts/adobe-source-code-pro
        /usr/share/fonts/cantarell
        /usr/share/fonts/encodings
        /usr/share/fonts/gsfonts
        /usr/share/fonts/noto-fonts-cjk-vf
        /usr/share/fonts/util
        /usr/share/fonts/Windows_Fonts/YaHei-Consolas-Hybrid-1.12
        /usr/share/fonts/encodings/large
```

说明 `fontspec` 宏包会在上面的路径中去寻找字体.
