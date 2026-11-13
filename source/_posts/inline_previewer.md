---
title: Terminal inline Previewer
tags: [sixel, fzf, shell]
categories: Linux
date: 2026-03-08 01:30:00
---

今天，终于把这个终端文件预览脚本写好了。这个脚本(其实是两个脚本)能干些什么事情呢 ?

* 立马在终端预览预览文件，不用离开终端
* 集成 `fzf`, 可以用于目录切换，文件打开

一个脚本叫做 `pre`，(只能)用于在终端打印文件的预览; 支持目录, 文本，图片, tar(.gz), zip, PDF, Djvu, Epub, 视频以及字体的预览.

另一个脚本(函数)叫做 `ipre`, 它把 `pre` 和 `fzf` 进行了深度的集成. 为什么要分成两个文件 ? 大概这样做更加符合 Unix 哲学吧 ?

**Do one thing and do it well !**

其实这个 `ipre` 函数还是比较简单的:


```bash
function ipre() {
    local cmd file
    if [[ -n "$IPRE_LS" ]]; then
        cmd=(${=IPRE_LS})
    else
        cmd=(
            fd --hidden --follow
            -E .git
            -E target
            -E .cargo
            -E build
            -E .npm
            -E node_modules
            -E .cache
            -E dist
            -E out
            -E lsp-bridge
            # other ignore dir
            -E .java
            -E .gradle
            -E .nuget
            -E .steam
            -E .android
            -E .ipython
            -E .vscode
            -E .vscode-oss
            -E .oh-my-zsh
            -E .emacs-tmp
            -E .pub-cache
            -E .proxyman
            . # search all patterns
            )
    fi
    if [[ $# -gt 0 ]]; then
        cmd+=("$@")
    fi
    # file select and preview
    cmd_str=$(printf '%q ' "${cmd[@]}")
    # merge with fzf
    FZF_STATE_FILE="$HOME/.cache/pre_thumbs/fzf_state"
    echo "file" > "$FZF_STATE_FILE"
    file=$(
        "${cmd[@]}" | fzf \
        --multi \
        --prompt "ipre > " \
        --preview 'pre {}' \
        --height=50% --reverse \
        --preview-window=right:60% \
        --bind 'resize:refresh-preview' \
        --bind 'focus,load:transform-header:file --brief {}' \
        --bind "\`:reload(\
               if grep -qxF 'file' '$FZF_STATE_FILE'; then \
                   echo 'directory' > '$FZF_STATE_FILE' && \
                   $cmd_str --type f $dir; \
               elif grep -qxF 'directory' '$FZF_STATE_FILE'; then \
                   echo 'all' > '$FZF_STATE_FILE' && \
                   $cmd_str --type d $dir; \
               else \
                   echo 'file' > '$FZF_STATE_FILE' && \
                   $cmd_str $dir; \
               fi)" \
        --bind 'alt-a:select-all' \
        --bind 'alt-a:+execute-silent(echo {+} | wl-copy)' \
        --bind 'alt-y:execute-silent(echo {} | wl-copy)' \
        --bind "alt-r:execute-silent(rm -f {})+reload($cmd_str)" \
        --bind "alt-left:reload($cmd_str $dir)" \
        --bind "alt-right:reload(\
               if [[ -d '{}' ]]; then \
                  $cmd_str {}; \
               else \
                  $cmd_str \$(dirname {}); \
               fi)"
        )
    # post action for selection:
    [[ -z "$file" ]] && return
    if [[ -d "$file" ]]; then
        cd "$file"
        exit 0
    fi
    case "${file:l}" in
        *.png|*.jpg|*.jpeg|*.gif|*.webp|*.bmp|*.tiff)
            nohup imv "$file" &>/dev/null &
            ;;
        *.pdf|*.djvu|*.epub|*.mobi)
            nohup zathura "$file" &>/dev/null &
            ;;
        *.tar|*.tar.gz|*.tgz|*.tar.xz|*.txz|*.tar.bz2|*.tbz2)
            return
            ;;
        *.zip)
            return
            ;;
        # for text files to open:
        *)
            ${EDITOR:-nvim} "$file"
            ;;
    esac
}
```


相比于 `lf`, `ranger`, `yazi` 等成熟的软件, 该脚本的优势(我最喜欢的一点)就是, 我可以不用离开当前终端，在选择,浏览或预览文件时还可以看到之前命令的输出. 另一些比较小的优势, 轻量化以及完全掌控.

目前, 该项目已经在 Github 上开源, 点击 [ipre - terminal inline previewer](https://github.com/zongpingding/ipre) 跳转.

在些这个脚本的过程中，参考了一些类似项目, 比如: [fzf-preview](https://github.com/gmou3/fzf-preview), 以及 fzf 的官方手册. 遇到了哪些困难呢 ? 最大的困难应该是 **不熟悉 fzf 的命令参数**, 也不能怪我，这个命令的参数真的太多了(所以, 人们对 fzf 的开发不到 1% >_<)

和 fzf 类似的一个软件叫做 [skim](https://github.com/skim-rs/skim), 不错，又是一个 Rust 重写万物的产物 ... 还没有体验过，不过，看官方文档的介绍，skim 有一个什么 interasctive mode, 这个是它的特色. 之后有时间了就去试试.

之后，我也许还会用 fzf 写一写脚本，用于和 `ps`, `kill`, `cd`, 之类的命令集成一下，这个应该不难，毕竟 fzf 官方已经提供了一些详细的(高级的)使用案例, 参见 [fzf advanced examples](https://github.com/junegunn/fzf/blob/master/ADVANCED.md).

