---
title: Emacs CAPF 
tags: CAPF
categories: Emacs
date: 2026-05-17 22:20:00
---

## 一个问题
今天终于腾出时间来修一修 Emacs 的问题了 - 使用 `corfu` 补全框架时, 怎么将 `yasnippet` 加入到 Emacs 的 `completion-at-point-functions` 中 ? 

默认情况下, 在使用 `AucTeX`, `lsp-mode` 的情况下, 变量 `completion-at-point-functions` 的值如下:

``` lisp
(lsp-completion-at-point
 TeX--completion-at-point
 t
 LaTeX--arguments-completion-at-point
 ispell-completion-at-point)
```

我不喜欢默认的配置, 所以对它进行了一些修改. 之前这部分配置大致如下:

``` lisp
(defun my/lsp-setup ()
  (setq-local completion-at-point-functions
	 (list 
	   #'lsp-completion-at-point
	   #'yasnippet-capf
	   ...
	 )))
(add-hook 'LaTeX-mode-hook #'my/lsp-setup)
```

为了便于讨论，我们假定已经加载了如下设置:

``` lisp
(defvar my/latex-snippets '(
  ("mrm" "\\mathrm{$1}"        "math roman")
)
(yas-define-snippets 'LaTeX-mode  my/latex-snippets)
```

在 `LaTeX-mode-hook` 下，当光标位于 `mrm` 之后时, 在 corfu 的弹出菜单中，并没有 yasnippet 相关的选项. 使用 `C-h v completion-at-point-functions` 得到的结果为:

``` lisp
;; Value in #<buffer main.tex>
(lsp-completion-at-point
 yasnippet-capf ...)
```

这个 `yasnippet-capf` 不是在这个 `completion-at-point-functions` 变量里面吗? 为什么不起作用呢 ? 很奇怪，是不是 ? 

后来才了解到一个叫做 `Exclusivity` 的东西. Emacs 会从左到右依次执行 `completion-at-point-functions` 列表中的函数。 当光标在 `mrm` 后面时，Corfu 尝试自动触发补全，它首先调用了 `lsp-completion-at-point`。`lsp-mode` 的补全函数非常 `贪婪`，它通常会接管当前位置的补全任务。即使 LSP 服务器没有关于 `mrm` 的补全建议，它也会返回一个结果（可能为空），从而阻断了 Emacs 继续向后寻找其他 Capf 函数。

因此，排在第二位的 `yasnippet-capf` 根本没有机会被 Corfu 自动调用。但是, 当你手动执行 `yasnippet-capf` 命令时，你便可以绕过这个机制，直接调用该函数，此时该 snippets 便会显示在补全菜单中。

上面的 `贪婪` 也许翻译为 `排外(exclusivity)` 更加的贴切，一个 capf 只允许自己提供补全数据，截断了之后所有成员(capf) 补全的机会.


## 解决方案
### 合并后端 - 'cape-capf-super'
既然问题找到了，我们就应该着手解决它了. 既然我们使用了 corfu, 那么它的孪生项目 - `cape` 便值得我们一看。经过一段时间的查询, 我发现后者提供了一个叫做 `cape-capf-super` 的命令(高阶函数 - 用于生成函数的函数). 

我们可以使用该命令将上述的两个后端合并在一起, 这样 LSP 的补全提示和 Yasnippet 的片段就会在 Corfu 的同一个弹窗中混合显示。

所以，这个问题似乎可以这样解决:

``` lisp
(defun my/lsp-setup ()
  (setq-local completion-at-point-functions
	(list 
	  (cape-capf-super #'lsp-completion-at-point #'yasnippet-capf)
	  ...
	)))
(add-hook 'LaTeX-mode-hook #'my/lsp-setup)
```

好了，重启 Emacs, 打开我最喜欢的一个 TeX 文件, 输入 `mrm`, 还是不行 !!! 为什么 ??? 好吧，再次运行 `C-h v completion-at-point-functions` 命令, 这次我们会得到:

``` lisp
;; Value in #<buffer main.tex>
(lsp-completion-at-point
 #[0 "\303\302\301\300#\207"
    [(yasnippet-capf) lsp-completion-at-point cape-wrap-super apply] 4])
```

What F**k ??? 为啥第一个 capf 还是 `lsp-completion-at-point` 不是我们设置的那个 `(cape-capf-super #'lsp-completion-at-point #'yasnippet-capf)` ? 而且，里面的那一坨乱码是什么东西 ?

首先解决第一个问题, 还记得我们之前说过的:

> `lsp-mode` 的补全函数非常 `贪婪`，它通常会接管当前位置的补全任务 ...

所以，这个 `lsp-completion-at-point` 函数是 `lsp-mode` 偷偷加进去的. 怎么避免呢 ? 更换一下我们的钩子(Hook)就行了:

```elisp
(defun my/lsp-setup ()
  (setq-local completion-at-point-functions
    (list 
      (cape-capf-super #'lsp-completion-at-point #'yasnippet-capf)
    )))
(add-hook 'lsp-completion-mode-hook #'my/lsp-setup)
```

现在所有的功能都正常了，lsp 的补全和 yasnippet 的补全完美融合在一起了:

``` lisp
;; Value in #<buffer main.tex>
(#[0 "\303\302\301\300#\207"
    [(yasnippet-capf) lsp-completion-at-point cape-wrap-super apply] 4])
```


第二个问题，也许不能叫做一个问题: 那些以 `#[` 开头的字符串并不是真正的乱码，而是 Emacs Lisp 的编译字节码 (Compiled Byte-code)。为什么会出现字节码 ?

在 Emacs 里，`completion-at-point-functions` 这个列表通常里面装的是一系列的函数(比如 `TeX--completion-at-point`, `t`, `yasnippet-capf` 等):

但是，当你使用了 `(cape-capf-super #'lsp-completion-at-point #'yasnippet-capf)` 后，发生了一些有趣的事情：

1. `cape-capf-super` 是一个高阶函数(一个用来生成其他函数的函数).
2. 它读取了你给它的两个函数，然后在内存中动态生成了一个全新的、没有名字的`匿名函数(闭包)`。这个新函数的作用就是去同时呼叫 `LSP` 和 `Yasnippet`.
3. 为了让代码运行得更快，Emacs 自动将这个动态生成的匿名函数编译成了底层的机器指令，也就是 `字节码`.

当你使用 `C-h v` 查看变量时，Emacs 无法打印一个 "没有名字" 的函数，所以它只能把这个函数编译后的底层形态原原本本地打印出来给你看。

等等, 上面有一个 `t`, 这又是啥 ? 这个代表全局变量 `completion-at-point-functions` 的值. 一般情况下，每一个 Major mode 都会设置自己的 `completion-at-point-functions` 变量。所以用户可以使用 `t` 来引用全局变量中的值.


### 去除 Capf 排他性 - 'cape-wrap-nonexclusive'
上面仅仅只有两个补全后端，如果我想要更多的补全后端呢 ? 当然了，你可以像下面这样写:

``` lisp
(defun my/lsp-setup ()
  (setq-local completion-at-point-functions
    (list 
      (cape-capf-super #'lsp-completion-at-point #'yasnippet-capf #'cape-dabbrev)
    )))
(add-hook 'lsp-completion-mode-hook #'my/lsp-setup)
```

我们添加了一个 `cape-dabbrev` 后端，它可以(动态地)根据当前 buffer 中的内容给我们提供补全数据. 有没有其它的实现途径呢 ? 有的 ! 看下面这两个代码:

代码 1:

``` lisp
;; code 1
(defun lsp-completion-at-point-nonexclusive ()
  (cape-wrap-nonexclusive #'lsp-completion-at-point))
(defun my/lsp-setup ()
  (setq-local completion-at-point-functions
    (list 
      #'lsp-completion-at-point-nonexclusive
      #'yasnippet-capf
     )
  )
)
```


代码 2:

``` lisp
;; code 2
(defun my/lsp-setup ()
  (setq-local completion-at-point-functions
    (list 
      (lambda()(cape-wrap-nonexclusive #'lsp-completion-at-point))
      #'yasnippet-capf
     )
  )
)
```

> 不要忘记嵌套一层 `lambda`, 写成 `(cape-wrap-nonexclusive #'lsp-completion-at-point)` 是错的. 否则，你就会看到这样的情景: 
>
> ```elisp
> Corfu detected an error:
>   backtrace-to-string()
>   corfu--debug((invalid-function (1 1 #f(compiled-function (str pred action) #<bytecode -0x1c9f0d24d129629d>) :exclusive no :annotation-function lsp-completion--annotate :company-kind lsp-completion--candidate-kind :company-deprecated lsp-completion--candidate-deprecated :company-require-match never :company-prefix-length nil :company-match lsp-completion--company-match :company-docsig lsp-completion--company-docsig :company-doc-buffer #f(compiled-function (&rest args) #<bytecode 0x1d69c45702cd6ef6>) :exit-function #f(compiled-function (&rest args-before) #<bytecode -0x2e6361e7a02ddcf>))))
>   (1 1 #f(compiled-function (str pred action) #<bytecode -0x1c9f0d24d129629d>) :exclusive no :annotation-function lsp-completion--annotate :company-kind lsp-completion--candidate-kind :company-deprecated lsp-completion--candidate-deprecated :company-require-match never :company-prefix-length nil :company-match lsp-completion--company-match :company-docsig lsp-completion--company-docsig :company-doc-buffer #f(compiled-function (&rest args) #<bytecode 0x1d69c45702cd6ef6>) :exit-function #f(compiled-function (&rest args-before) #<bytecode -0x2e6361e7a02ddcf>))()
>   corfu--capf-wrapper((1 1 #f(compiled-function (str pred action) #<bytecode -0x1c9f0d24d129629d>) :exclusive no :annotation-function lsp-completion--annotate :company-kind lsp-completion--candidate-kind :company-deprecated lsp-completion--candidate-deprecated :company-require-match never :company-prefix-length nil :company-match lsp-completion--company-match :company-docsig lsp-completion--company-docsig :company-doc-buffer #f(compiled-function (&rest args) #<bytecode 0x1d69c45702cd6ef6>) :exit-function #f(compiled-function (&rest args-before) #<bytecode -0x2e6361e7a02ddcf>)) 2)
>   run-hook-wrapped(corfu--capf-wrapper (1 1 #f(compiled-function (str pred action) #<bytecode -0x1c9f0d24d129629d>) :exclusive no :annotation-function lsp-completion--annotate :company-kind lsp-completion--candidate-kind :company-deprecated lsp-completion--candidate-deprecated :company-require-match never :company-prefix-length nil :company-match lsp-completion--company-match :company-docsig lsp-completion--company-docsig :company-doc-buffer #f(compiled-function (&rest args) #<bytecode 0x1d69c45702cd6ef6>) :exit-function #f(compiled-function (&rest args-before) #<bytecode -0x2e6361e7a02ddcf>)) 2)
>   #f(compiled-function () #<bytecode 0x1be71ca7edce3850>)()
>   #f(compiled-function () #<bytecode -0x107ddc99e8181644>)()
>   handler-bind-1(#f(compiled-function () #<bytecode -0x107ddc99e8181644>) (error) corfu--debug)
>   corfu--protect(#f(compiled-function () #<bytecode 0x1be71ca7edce3850>))
>   corfu-auto--complete-deferred((#<window 3 on main.tex> #<buffer main.tex> 415 455))
>   apply(corfu-auto--complete-deferred (#<window 3 on main.tex> #<buffer main.tex> 415 455))
>   timer-event-handler([t 27145 41316 398821 nil corfu-auto--complete-deferred ((#<window 3 on main.tex> #<buffer main.tex> 415 455)) nil 95000 nil])
> ```

这两种方法本质上都是运用 `cape-wrap-nonexclusive` 命令, 此命令可以使得一个 capf 变为 nonexclusive. 

### 最终配置
最后，我的完整配置如下:

``` lisp
;; cape config
(use-package cape
  :ensure t
  :bind ("C-c p" . cape-prefix-map) ;; Alternative key: M-<tab>, M-p, M-+
  :init
    (add-hook 'completion-at-point-functions #'cape-file)
    (add-hook 'completion-at-point-functions #'cape-dabbrev)

    ;; None exclusive lsp capf provided by 'cape'
    (defun my/lsp-capf-nonexclusive ()
      "Nonexclusive LSP CAPF"
      (cape-wrap-nonexclusive #'lsp-completion-at-point))
)


;; set up CAPFs in LaTeX-mode
;; REF: https://emacsconf.org/2025/talks/completion/
(defun my/tex-capf-nonexclusive ()
  "Nonexclusive TeX CAPF"
  (cape-wrap-nonexclusive #'TeX--completion-at-point))
(defun my/latex-args-capf-nonexclusive ()
  "Nonexclusive LaTeX argument CAPF"
  (cape-wrap-nonexclusive #'LaTeX--arguments-completion-at-point))
(defun my/capf-setup-latex ()
  (setq-local completion-at-point-functions
			  (list 
			  #'my/lsp-capf-nonexclusive
			  #'my/yasnippet-capf-nonexclusive
			  #'my/tex-capf-nonexclusive
			  #'my/latex-args-capf-nonexclusive
			  t ;; global hook
			  ))
)
;; NOTE: set the 4th argument of 'add-hook' to 't', to make it buffer only.
(defun my/latex-capf-setup ()
  "patch 'lsp-completion-mode-hook' in LaTeX mode locally, only when 'framework-cape.el' is loaded."
  (add-hook 'lsp-completion-mode-hook #'my/capf-setup-latex nil t)
)
(add-hook 'LaTeX-mode-hook #'my/latex-capf-setup)

;; clean up CAPFs in LaTeX-mode
(defun my/latex-capf-cleanup ()
  "Remove unwanted CAPFs like 'ispell'."
  (setq-local completion-at-point-functions
              (cl-set-difference
               completion-at-point-functions
               '(ispell-completion-at-point))))
(add-hook 'LaTeX-mode-hook #'my/latex-capf-cleanup)
```

对应的 `completion-at-point-functions` 变量值为:

``` lisp
(my/lsp-capf-nonexclusive
 my/yasnippet-capf-nonexclusive
 my/tex-capf-nonexclusive
 my/latex-args-capf-nonexclusive
 t)
```


## 结语
所以为什么要有 Exclusivity 呢 ? 也许是为了防止补全菜单变为各个 capf 的垃圾场吧. 我们需要部分 capf 站出来，勇敢得对后续 capf 随意往补全菜单倾倒垃圾的行为说 'NO' !


## REF
Some reference:

* [Cape](https://elpa.gnu.org/devel/cape.html)
* [Completion in Buffer](https://www.gnu.org/software/emacs/manual/html_node/elisp/Completion-in-Buffers.html)
* [Setting :exclusive 'no by default for all completion-at-point-functions](https://www.reddit.com/r/emacs/comments/zwgd54/setting_exclusive_no_by_default_for_all/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)
