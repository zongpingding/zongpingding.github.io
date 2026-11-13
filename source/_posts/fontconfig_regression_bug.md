---
title: Fontconfig regression bug
tags: [Archlinux, fontconfig]
categories: Linux
date: 2026-06-09 21:00:00
---


Check:

* https://github.com/msys2/mingw-packages/issues/29691
* https://www.reddit.com/r/archlinux/comments/1twdfq4/fontconfig_2181_broke_generic_font_aliases/
* https://www.reddit.com/r/archlinux/comments/1tyr1b2/did_something_change_with_the_new_fontconfig/


From `fontconfig 2.17.1-1` to `fontconfig 2.18`, many of my GUI applications broke as well:

1. LocalSend only shows a black screen.
2. Wolfram Notebook crashes with `Segmentation fault (core dumped)`.
3. OBS either fails to start or crashes when I click the **Settings** menu.

After downgrading back to `2.17.1-1`, everything works normally again.

As a bonus, I can finally remove those Flatpak alternatives `-_-`.


Maybe it's time to set up `timeshift` ?
 
