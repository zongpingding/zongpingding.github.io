---
title: 3D object in Acrobat 
tags: Acrobat
categories: LaTeX
date: 2024-06-14 00:00:00
---

## way 1
解决方法如下，方法来自Adobe官网, 首先关闭 `Adobe Acrobat Pro`, 然后重命名以下文件夹：把 
``` shell
C:\Users\UserName\AppData\Local\Adobe\Acrobat\DC
```


 重命名为
``` shell
C:\Users\UserName\AppData\Local\Adobe\Acrobat\DC_OLD
```

如果有可能，还请将 
``` shell
C:\Users\UserName\AppData\Local\Adobe\Acrobat\20XX
```

更改为
```shell
C:\Users\UserName\AppData\Local\Adobe\Acrobat\20XX_OLD
```

然后重新打开三维PDF文件以查看

## way 2
重新安装 Adobe Reader

## warning
并非所有PDF阅读器都将查看三维PDF，如果遇到问题，请首先尝试其他PDF阅读器
