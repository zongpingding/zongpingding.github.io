---
title: Windows屏保Fliqlo设置
tags: Windows
categories: [Computer]
index_img: /img/windows_fliqlo/banner.png
banner_img: /img/windows_fliqlo/banner.png
date: 2024-03-26 00:00:00
---

## 前言
之前就想要了解以下Fliqlo这个东西，但是之前在Windows上一直没有折腾成功，反正就是`flash`, `.Net`之类的bug，一直没有去解决，于是就放弃了.但是今天突然想起来了还有这个玩意儿，所以就有了今天这篇文章。


## 安装
按照官网来就行了，软件的内部有一个 `ReadMe.txt`, 内容如下:
``` text
Fliqlo Screensaver for Windows ReadMe
Updated on March 12, 2021 (for version 1.5.1)

SYSTEM REQUIREMENTS
- Windows 10 / 8.1
- Internet connection to display content

BEFORE INSTALLATION
If you already have an older version of the Fliqlo screensaver installed, you need to uninstall it before installing the new version. To uninstall the screensaver, in Control Panel or Apps & Features (Windows 10), uninstall the program named "Fliqlo". For manual removal, in File Explorer, find and remove the file named "Fliqlo.scr" in C:\Windows\system32 and/or C:\Windows\SysWOW64 folders.

INSTALLATION
Right-click the file named "Fliqlo.scr" and select "Install" to make it your default screensaver, then Screen Saver Settings panel will open automatically. Alternatively, in File Explorer, copy or move the file named "Fliqlo.scr" to C:\Windows\system32. Then open Screen Saver Settings panel to set Fliqlo as your default screensaver.

UNINSTALLATION
In File Explorer, remove the file named "Fliqlo.scr" installed by the above method.

PLEASE NOTE
Currently, this software is built without code-signing, so during installation you may see a Windows security warning that the publisher could not be verified. To bypass the warning, you need to allow the unsigned software to run on your computer when the warning appears.

TERMS OF USE
This software can be used in your home, office, and store. No portion of the software, including the other files in the downloaded zip archive named "FliqloScr.zip", may be redistributed, sold, renamed, converted, or made available for download from websites other than fliqlo.com. Instead, please provide a link to https://fliqlo.com/ if you wish to share the software.

DISCLAIMER
This software is provided to you free of charge. The author gives no warranty in relation to these softwares, and you use them at your own risk. By using or installing the software, you agree to be bound by the terms of this agreement. The author will not be liable for any damage to your system, any loss or corruption of any data or software, or any other loss or damage that you may suffer as a result of downloading or using the software, whether it results from the author's negligence or in any other way.

PRIVACY POLICY
Please check on the fliqlo.com website.

DONATION
If you want to make a donation to support the further development of this software, it is highly appreciated. To donate, double-click the internet shortcut file named "Donate with PayPal.url" in the zip archive named "FliqloScr.zip", or visit the author's PayPal.me page (https://www.paypal.me/yjadc/). You may then enter any amount you wish, and PayPal.me will securely do the rest. Thank you for your support!
* Please refrain from donating less than $1, such as $0.1, as most will be processed as PayPal transaction fees and the actual donation is $0.

ABOUT THE AUTHOR
Yuji Adachi
Freelance Web/UI designer, living in Tokyo, Japan

SUPPORT
Website: https://fliqlo.com/
Email: info@9031.com

© 2021 9031
```

我是采用的第一种方式进行安装的，因为我把应用移动到`system32`文件夹之后出现了什么 `.Net`的错误，不想去折腾了。


## 快捷键调用
采用了一个简单的方法：直接把这个东西固定到我的如 `任务栏`, 这样就可以使用windows默认的 `win + <num>`直接调用这个应用，比如我固定到第一个位置，当我摁下 `win+1` 时就会自动执行这个Fliqlo应用。

但是你很有可能会遇到如下的问题：找不到任何的方法可以直接把这个非 `.exe`文件直接固定到任务栏，于是我们采用曲线救国的策略:
* 首先把这个应用 `Fliqlo.scr`更改为 `Fliqlo.exe`，然后执行这个文件，这样你就会在你的任务栏看到这个应用的图标，然后右键固定任务栏即可。

* 更改`.exe`为`.scr`? 为什么？因为默认的运行 `.exe`格式的文件时，这个效果并不是你希望的，他会打开这个应用的设置界面，并不是启动屏保。只有执行以 `.scr` 为后缀的这个文件时才会打开屏保。
怎么修改？ 直接把右键这个固定的快捷方式，然后选择`属性`，然后把`C:\BIN\Fliqlo.exe`改为 `C:\BIN\Fliqlo.scr`即可

> * 其实你在cmd窗口中运行这个应用时，不知道为什么，它还是打开了fliqlo的设置界面，而非对应的屏保，但是你在powershell中执行这个应用时就是正确的结果.
> * 其实从上面也可以看出，我们不能用AutoHotKey的 `Run "Fliqlo.scr"`命令来启用屏保了，以为它好像是默认采用cmd去执行这个应用.

## 备注
我之前写的一个autohotkey快捷键脚本，如下:
``` text
; Fliqo 快捷锁屏
#k::
{
 	Run "cmd Fliqlo.scr"
}
```




