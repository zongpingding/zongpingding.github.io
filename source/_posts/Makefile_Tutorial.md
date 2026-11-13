---
title: Basic Makefile Tutorial
tags: makefile
categories: Linux
math: false
date: 2024-08-25 00:00:00
updated: 2026-06-12 15:00:00
---


## 基本概念

核心: make使用文件的创建和修改时间来判断是否应该更新一个目标文件

一个基本的 Makefile 文件的基本书写格式为:

```makefile
# 目标文件: 依赖文件1 依赖文件2
# 1. Tab开头的是命令
# 2. make执行时，默认执行第一条规则. 
# 把默认执行的规则放第一条，其他规则的顺序是无关紧要的，因为make执行时自动判断依赖
x.txt: m.txt c.txt
	cat m.txt c.txt > x.txt

m.txt: a.txt b.txt
	cat a.txt b.txt > m.txt
```

## 伪目标

常用于没有依赖的目标, 比如我们常用的 clean 等, 一个示例如下:

```makefile
clean:
	rm -f m.txt
	rm -f x.txt
```

由于和前面的规则不同，所以执行 `clean` 这以目标时，需要使用命令: make clean. 有时候为了防止这种为目标出现一些意想不到的结果，可以对伪目标进行严格的声明，如下:

``` makefile
.PHONY:clean
clean:
    find ${DIR_OBJ} -name *.o -exec rm -rf {}
```


那么这里 `意想不到的结果` 到底是什么 ? 比如目标 `clean`： 如果当前目录下已经存在一个名为 `clean` 的文件, 此时 `make` 命令发现其生成日期是最新的，就会拒绝执行 `clean` 下的任务.

一些注意点如下:

* 可以使用 `\` 把一行语句拆成多行，便于浏览
* 多条语句可以用 `&&` 或者是 `;` 连接

## 错误捕捉

忽略错误，继续执行后续命令，可以在需要忽略错误的命令前加上`-`：

```makefile
ignore_error:
	-rm zzz.txt
	echo 'ok
```

## 隐式规则

`make` 针对 C、C++、ASM、Fortran 等程序内置了一系列隐式规则， 所以类似于 `xyz.o` 这种规则可以不写，make 会自动创建如下的规则:

```makefile
xyz.o: xyz.c
	cc -c -o xyz.o xyz.c
```

## 变量

**定义:** 变量定义用 `变量名 = 值` 或者 `变量名 := 值`，通常变量名全大写。引用变量用 `$(变量名)`，非常简单。

还有一些内置的变量, 如 CC, CFLAGS 等，但是我们仍然可以重定义:

```
CC = gcc
CFLAGS = -g -Wall -I${DIR_INC}
```

## 模式替换

如果一条规则依赖的文件过多，那么就可以使用通配符的形式进行规则的编写. 比如下面的一个例子:

```makefile
# $(wildcard *.c) 列出当前目录下的所有 .c 文件: hello.c main.c
# 用函数 patsubst 进行模式替换得到: hello.o main.o
OBJS = $(patsubst %.c,%.o,$(wildcard *.c))
TARGET = world.out

$(TARGET): $(OBJS)
	cc -o $(TARGET) $(OBJS)

clean:
	rm -f *.o $(TARGET)
```



## 自动变量

一些常见的自动变量如下:

``` makefile
world.out: hello.o main.o
	@echo '$$@ = $@' # 变量 $@ 表示target
	@echo '$$< = $<' # 变量 $< 表示第一个依赖项
	@echo '$$^ = $^' # 变量 $^ 表示所有依赖项
	cc -o $@ $^
```

执行结果输出如下：

```bash
$@ = world.out
$< = hello.o
$^ = hello.o main.o
cc -o world.out hello.o main.o
```

## 一些内置函数

make 内置的一些函数在必要的时候可以加大的简化我们的 makefile 文件编写难度:

* wildcard, 使用的是 `*`
* patsubst, 使用的是 `%`
* shell, 返回一个shell命令执行结果
