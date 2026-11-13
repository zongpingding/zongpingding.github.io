---
title: Physics 2 mechanism
tags: xparse
categories: LaTeX
date: 2024-11-12 00:00:00
---

## internal commands
Some pre-defined macros:
```latex
\expandafter\def\csname phy@ab@chk\string*\endcsname{}
\expandafter\def\csname phy@ab@chk\string\big\endcsname{}
\expandafter\def\csname phy@ab@chk\string\Big\endcsname{}
\expandafter\def\csname phy@ab@chk\string\bigg\endcsname{}
\expandafter\def\csname phy@ab@chk\string\Bigg\endcsname{}
\def\phy@AB@gen#1#2{\expandafter\DeclareDocumentCommand\csname phy@#1@\string#2\endcsname}
```

## simple case
A simple example:

```latex
\DeclareDocumentCommand\phy@ab@(   {+r()}{\phy@abopen(#1\phy@abclose)}
\DeclareDocumentCommand\phy@ab@[   {+r[]}{\phy@abopen[#1\phy@abclose]}
```

We use `~` to denotes the content in such macro, analysis of the mechanism about Physics2 as below:

```latex
\long\def\phy@d@lx#1#2#3{%
  \ifcsname phy@ab@chk\string#3\endcsname\def\reserved@a{#1}% manully set delimiter size
  \else\def\reserved@a{#2}%
  \fi\csname phy@d@lx\reserved@a\endcsname#3}
\def\phy@d@l@genxm#1{%
  \long\expandafter\def\csname phy@d@lx#1\endcsname##1##2{%
    \begingroup % \endgroup is at the end of #4 of \phy@AB@gen
    \ifx##1*\let\phy@tempa=\relax\else\let\phy@tempa=##1\fi
    \csname phy@#1@\string##2\endcsname\phy@tempa##2}}
\def\phy@d@l@genxa#1{%
  \long\expandafter\def\csname phy@d@lx#1\endcsname##1{%
    \csname phy@#1@\string##1\endcsname##1}}

\phy@d@l@genxm{mb}
\phy@d@l@genxa{ab}
\DeclareRobustCommand\ab{\phy@d@lx{mb}{ab}}
```


## conclusion
Thus we have `\ab(~)`:
```latex
--> \phy@d@lx{mb}{ab}(~)
--> \def\reserved@a{ab}; \phy@d@lxab(~); % how does \phy@d@lxab defined ?
--> \phy@d@l@genxa{ab}; \def\phy@d@lxab#1{\phy@ab@#1 {#1}}
--> \phy@d@lxab(~); \phy@ab@( (~); \phy@abopen(~\phy@abclose)
```

## Apply
Nowadays, `xparse` provided so many argument specifier:
* mandatory: `m`, `r`, `R`, `v`, `b` 
* optional:`o`, `d`, `D`, `O`, `s`, `t`, `e`, `E`

Then you can create command similar to Physics2 using `D`, or others, an simple example:
```latex
% \RequirePackage{mathtools}
\NewDocumentCommand{\xl}{O{}D(){}}{\ensuremath{\xlongleftarrow[#2]{#1}}}
\NewDocumentCommand{\xxl}{O{}D(){}}{\ensuremath{\xLongleftarrow[#2]{#1}}}
\NewDocumentCommand{\xr}{O{}D(){}}{\ensuremath{\xlongrightarrow[#2]{#1}}}
\NewDocumentCommand{\xxr}{O{}D(){}}{\ensuremath{\xLongrightarrow[#2]{#1}}}
```




