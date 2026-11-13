---
title: SCP文件传输
tags: [Linux]
categories: [Computer]
index_img: /img/scp/banner.png
banner_img: /img/scp/banner.png
date: 2024-03-26 00:00:00
---


主要参考: [scp命令 Linux和Windows文件互传](https://www.cnblogs.com/zhuawang/p/12433357.html)

## 生成密钥
按照原始博客操作即可, 在windows上使用
``` shell
ssh-keygen -t rsa
```

然后一直回车，生成一个密钥在 `C:\Users\PC.ssh\id_rsa.pub`然后复制其中的内容到Linux的如下文件中:
```text 
/home/zpd/.ssh/authorized_keys
```

然后就可以愉快玩耍了

> 主要如果只是复制到普通用户的这个文件中，那么你只能把文件上传到这个用户具有写权限的目录中

## 实践
比如在windows上把当前路径下的 main.c传到Linux下的 `/home/zpd/temp/`目录，只需要运行如下命令:

``` shell
scp main.c zpd@<my ip>:/home/zpd/Temp/
```

## 自定义命令
我自己自定了两个powershell命令:`push_file`, `pull_file`分别负责从Windows上给Linux传输文件，以及从上面拉取文件。把下面这段代码粘贴到你的powershell配置文件中:

> 注意：我在Linux上建立了一个`Windows_Linux_Share`文件夹用于平时的文件交换
> 假设你的用户名为: `user`, 你服务器的IP地址为 `192.168.0.1`

``` shell
# scp  
# push file from local windows
function push_file{
	param(
		[switch] $recurse
	)
	if ($recurse){
		scp -r $args user@192.168.0.1:/home/user/Windows_Linux_Share
	}else{
		scp $args user@192.168.0.1:/home/user/Windows_Linux_Share
	}
}
# pull file from remote linux
function pull_file{
	param(
		[switch] $recurse
	)
	if ($recurse){
		scp -r user@192.168.0.1:/home/zpd/Windows_Linux_Share/$args ./
	}else{
		scp user@192.168.0.1:/home/zpd/Windows_Linux_Share/$args ./
	}
}
```


使用如下语法打开powershell的配置文件:

```shell
notepad $profile
```

然后你的记事本就会打开这个配置文件，把他粘贴到最后即可，然后回到powershell中执行命令

``` shell
. $profile
```

让刚才那两个命令生效。使用方法如下:
这里我在我家加目录下建立了一个 `~/Windows_Linux_Share/`的共享文件夹。
文件的上传：
```shell
# 单个文件（非文件夹）
push_file <path to your single file in windows>

# 文件夹及其内部所有文件(就是scp的原本语法)
push_file -r <path to your folder in windows>


下载文件# 单文件
pull_file <path to your single file in Linux>


# 文件夹及其内部所有文件
pull_file -r <path to your folder in Linux>
```

> 两个命令的调用格式是类似的



