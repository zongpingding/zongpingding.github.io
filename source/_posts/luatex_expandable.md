---
title: Everything is fullly expandable in LuaTeX
tags: LuaTeX
categories: LaTeX
date: 2026-11-07 00:00:00
---


一段我之前写的, 但是经常忘记发生放在哪儿的, 比较有用的, 体现 luatex 价值的代码:

```tex
% !TeX program = LuaLaTeX
% ==> add '#1' and '#2' to cmd code
\documentclass{article}
\usepackage[T1]{fontenc}
\parindent0pt


\begin{document}
\ExplSyntaxOn
% luatex only
\cs_new:Npn \LuatlMap #1#2
  {
    \tex_immediateassignment:D \cs_new:Npn 
      \__tl_item_cmd:n ##1 { #2 }
    \tl_map_function:nN { #1 }
      \__tl_item_cmd:n
  }
\edef\TTT{\LuatlMap {a12{xx}b}{[#1],}}
\detokenize\expandafter{\expanded{\TTT}}
% \show\TTT
% > \TTT=macro:
% ->[a],[1],[2],[xx],[b],.

% \TTT
\ExplSyntaxOff
\end{document}
```
