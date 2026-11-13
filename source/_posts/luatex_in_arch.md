---
title: Update LuaTeX in arch
tags: [luatex, fmt]
categories: LaTeX
date: 2026-03-04 21:10:00
---

近来，TeXLive2026 可用了，于是就运行 `sudo pacman -Syyu` 更新了一下. 结果, `/usr/share/texmf-dist/tex/luatex/` 下有一些文件冲突了. 于是我果断把整个 `luatex` 文件夹移除了. 

再次尝试 syyu，不出所料, LuaTeX 相关的所有 fmt 文件都构建失败了. 即使是我把 `luatex` 这个文件夹恢复回去也不行了， 运行了几次 `sudo pacman -Rnsc texlive-luatex` 和 `sudo pacman -S texlive-luatex` 都不行. 

检查了一下出错的命令, 如下:

```shell
luahbtex -ini   -jobname=lualatex -progname=lualatex lualatex.ini
```

报错显示: `... module 'lua-uni-data' not found: ...`。在网上查了一下, 这个 `lua-uni-data` 也是一个包, 在 github 上找到了它的地址, 把 `l3build install` 在家目录下的 `lua-uni-data` 文件夹移动到了 `/usr/share/texmf-dist/tex/luatex/` 下, 再次运行 `sudo pacman -S texlive-luatex`, LuaTeX 相关的 fmt 构建成功了.


其实最开始打算用 `sudo tlmgr install lua-uni-data` 来安装的, 结果报错显示:

```log
tlmgr: Local TeX Live (2025) is older than remote repository (2026).
Cross release updates are only supported with
  update-tlmgr-latest(.sh/.exe) -- --upgrade
See https://tug.org/texlive/upgrade.html for details.
```

可能是因为我是滚动更新的吧, 我的 `tex -v` 显示的 TeXLive 都已经到 2026 了. 不管这个啥 tlmgr 了, 我自己手动安装就行了, 复杂一点的宏包直接安装在家目录(或者用 `l3build install` 安装).
