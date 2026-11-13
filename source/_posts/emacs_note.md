---
title: Emacs note 
categories: Emacs
date: 2026-03-12 14:00:00
---

## 废话
很多人入 Emacs 的门就花了几年，我应该需要花费更多的时间才能入门了 ...

在入门过程中遇到的困难有:

* 用户基数小;
* lisp 作为配置语言;
* Windows 下 Emacs 的优化不行;
* 包名和部分术语过于 ~~抽象~~ 了;
* Emacs 的积累过于雄厚 - 选择困难;
* ...

在 Emacs 里面，部分包的名字，你一眼望过去，根本就不知道它能干啥 ? 比如下面这些:

```txt
ivy      --> ...
swiper   --> ...
counsel  --> ...
{ counsel { swiper { ivy } } }

helm     --> Emacs incremental completion and selection narrowing framework

avy      --> jumping to visible text using a char-based decision tree

company  --> 公司 ? 其实是 COMlete ANYwhere
corfu    --> color ？其实是 COmpletion in Region FUnction
vertico  --> vertex ? vortex ? 其实是 VERTical Interactive COmpletion

capf     --> capital ? Complete At Point Function
evil     --> vim
amx      --> An alternative M-x interface for Emacs.
ido      --> yes i do ? Interactively Do things
amc      --> Association for Computing Machinery ? a minor mode to assist in competitive programming, by saving you from tedious tasks(这个取名似乎很合理了 ?)
dabbrev  --> 这个还是很好理解的: Dynamic Abbreviation

tramp    --> Transparent Remote (file) Access, Multiple Protocol

hydra    --> make Emacs bindings that stick around

cape     --> Completion At Point Extensions

marginalia --> ...

...
```

这些名字还是比较考验能力的.

由于 Emacs 的历史积累过于雄厚，很多时候你的选择是很多的. 比如: `ivy/swiper/counsel` 和 `vertico/consult/orderless` 就是新旧的两套东西，他们做的事情都是差不多的(应该吧 ？也许部分的高级功能我还没有用到 :P)

在说一说自动补全之类的, 目前我觉得: `company`, `cape`, `acm`, `corfu` 这几个应该是差不多功能的, 都是一个前端 UI ?? 而且我也还没有分清 `completion-at-point` 和 `completion-in-region` 的区别, 这两个似乎是 Emacs 中自动补全相关的 API ? 还有一个东西 - `completion-at-point-functions`, 这里里面似乎可以放一些 lsp 的补全函数进去 ?

基本的补全逻辑应该是下面这样的吧(我也不确定):

```txt
1. <some text>|
              ^
	          your cursor (trigger CAPF)
	  
2. Emacs API

3. LSP / Other functions

4. Emacs API

5. UI
```

所以对于自动补全(+ lsp)，现在应该是推崇:

```txt
vertico
corfu
orderless
cape
marginalia
```

而不是老一套的:

```txt
ivy
company
```

不知道对不对 ? 


但是, 现在又杀出来一个 `lsp-bridge` - 已知的最快 LSP client !!! 这个插件的作者还开发了 [EAF (an extensible framework that revolutionizes the graphical capabilities of Emacs)](https://github.com/emacs-eaf/emacs-application-framework) 这样的框架出来. 这个作者似乎还是 deepin 的领导人还是开发者 ?


我也曾配置过 `lsp-bridge` 这样的插件, 比较遗憾的是，我无法使用 Python 的虚拟环境. 但是，很多时候写一些 Haskell, C, shell 脚本还是挺方便的.


Emacs 的学习不是一时的.


## 现状
我入门了 4 次 Emacs, 

* 在 windows 上尝试 Doom Emacs, 太卡了，依赖不好安装
* 在 WSL 中尝试 Doom Emacs, 字体怎么也配置不好, 图形相关的显示问题
* 再次在 WSL 中配置 Doom, 基本配置好了，但是还是不习惯 Emacs 的操作逻辑, 放弃
* 听说 Emacs 的 org mode，LaTeX 实时预览 ? 再次从头配置, 一个 vanilla emacs 诞生了; 但是始终希望将它配置成 Vscode 的模样 ？导致配置越烂越来越臃肿, 最后再次放弃


直到今天, 我再次重新配置了我的 vanilla emacs, 不追求大而全, 够用就行. 使用了半年 Emacs, 参看了很多别人的 Emacs 配置, 目前我对它很满意. 加载速度能够控制在 1.5s, 尽管还是没有 neovim 快. 下面的一段代码帮助很大:

```lisp
(use-package benchmark-init
  :ensure t
  :config
    (add-hook 'after-init-hook 'benchmark-init/deactivate))
```

我曾一直追求 200ms 的启动时间, 试图把 neovim 之类的比下去. 但是最后想了想，既然 Vscode 之类的启动都很慢了, 那么我为什么要这么苛责 Emacs 呢 ？Vscode 我一般都是打开一次就不再关闭了，但是我曾经却不停的打开/关闭 Emacs，这样确实有失公平 ...

