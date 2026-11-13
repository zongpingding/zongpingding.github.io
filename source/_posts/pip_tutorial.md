---
title: Pip Tutorial
tags: [pip, python]
categories: Computer
date: 2024-05-16 00:00:00
---

## 1. 环境说明

本虚拟环境专用于数据分析，科学计算，虚拟环境的创建方法：切换到虚拟环境的目标路径下,然后运行如下命令：

```shell
# <python> -m virtualenv <虚拟环境名称>
python -m virtualenv <env-name>
```

根据你的python版本需更进行更改，或者是使用Python的具体路径进行不同版本Python的区分 

## 2. pip安装包

1. 通过命令行安装必须把  **python38(37)** 中的 **python3.8.exe(python3.7.exe**) 重命名为 **python.exe.**【一一对应即可】
2. 想要麻烦一点就使用 **Pycharm** 安装库

## 3. pip源配置

使用如下命令进行配置

```shell
pip config set global.index-url <url>
```

具体的配置文件的位置

```shell
C:\Users\PC\AppData\Roaming\pip\pip.ini
```

移除更换全局的pip源

```shell
pip config unset global.index-url
```

常用源

```shell
清华源：
https://pypi.tuna.tsinghua.edu.cn/simple

阿里云源：
http://mirrors.aliyun.com/pypi/simple/

中科大源： 
https://pypi.mirrors.ustc.edu.cn/simple/

豆瓣源：
http://pypi.douban.com/simple/
```



## 4. pip配置代理
如果你的计算机上的1080端口被代理,那么使用如下命令即可进行`pip`命令的代理:

```shell
pip install  --proxy=http://127.0.0.1:1080
```
