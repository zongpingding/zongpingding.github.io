---
title: Linux常见错误
tags: [Computer]
categories: [Linux]
index_img: /img/linux_common_error/banner.webp
banner_img: /img/linux_common_error/banner.webp
date: 2024-03-26 00:00:00
---

## hostname unknown
``` text
unable to resolve host <Name>: Name or service not known
```

> Solution: add the <Name> to file /etc/hosts  as  127.0.0.1 <Name>

## group not exists
when run the following cmd sudo adduser <user name> admin
``` text
there is a error usermod: group 'admin' does not exist
```

but group admin exists in file /etc/suders exactely. 

> Solution: simple add a group admin by groupadd admin
> * the file /etc/suders has no relation to group admin 's exists.

## nginx 默认访问的html文件位置
在ubuntu下的位置nginx默认访问的index文件位置如下:
``` text
/var/www/html/index.nginx-debian.html
```

> 参考: https://segmentfault.com/q/1010000009529048

并且以此还产生了一种快速部署hexo博客到服务器的方法，把hexo生成的public文件夹中的所有内容复制到默认的nginx访问此目录下，如图
``` text
drwxr-xr-x 7 root root 4096 Dec 18 17:02 .
drwxr-xr-x 3 root root 4096 Dec 18 14:59 ..
drwxr-xr-x 3 root root 4096 Dec 18 17:02 2023
drwxr-xr-x 3 root root 4096 Dec 18 17:02 archives
drwxr-xr-x 3 root root 4096 Dec 18 17:02 css
drwxr-xr-x 2 root root 4096 Dec 18 17:02 fancybox
-rw-r--r-- 1 root root 6937 Dec 18 17:02 index.html
-rw-r--r-- 1 root root  617 Dec 18 16:31 index.nginx-debian.html
drwxr-xr-x 2 root root 4096 Dec 18 17:02 js
```


然后访问你的服务器ip就可以打开你的博客了,一般的网页根目录下都必须要有一个`index.html`文件，web服务器在处理用户请求时会默认寻找名为index.html的文件作为网站的主页（网页入口）


## Linux下ip查看
### 内网ip
运行命令`ipconfig`后会返回如下的结果:
``` text
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1450
inet 172.16.0.3  netmask 255.255.0.0  broadcast 172.16.255.255
inet6 fe80::f816:3eff:fe80:7ec8  prefixlen 64  scopeid 0x20<link>
ether fa:16:3e:80:7e:c8  txqueuelen 1000  (Ethernet)
RX packets 265734  bytes 68590660 (68.5 MB)
RX errors 0  dropped 0  overruns 0  frame 0
TX packets 381513  bytes 228398471 (228.3 MB)
TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
inet 127.0.0.1  netmask 255.0.0.0
inet6 ::1  prefixlen 128  scopeid 0x10<host>
loop  txqueuelen 1000  (Local Loopback)
RX packets 6  bytes 504 (504.0 B)
RX errors 0  dropped 0  overruns 0  frame 0
TX packets 6  bytes 504 (504.0 B)
TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```



### 公网ip
其中eth0对应的172.16.0.3 就是你的网卡ip，内网ip。想要查看公网ip，使用如下命令
``` text
curl ifconfig.me
```

> 这个方法本质上就是访问一个网页，然后这个网页返回你的公网ip

## nginx权限与防火墙
如图我创建了一个测试的nginx网站，监听的是 81端口，`test.conf`文件如下；

``` toml
server {
  listen 81;
  location / {
    root /home/;
  }
}
```

尽管我的home目录下没有index.html文件，但是浏览器报错502，其实就是我设置的端口81访问被拒绝。

> Solution: 在京东云防火墙开启81端口的访问即可,更改81端口的访问权限后，再添加一个index.html文件，不然会报错403 Forbidden

一般解决方法:查看nginx监听的端口
``` shell
ps -ef | grep nginx
```
选择nginx master进程的pid，假如是`555`
``` shell
sudo netstat -anp | grep 555
```

然后可以得到如下的输出

``` text
user@lavm-dpbk4woj1d:~/Temp$ sudo netstat -anp | grep 555
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      566/nginx: master p
tcp        0      0 0.0.0.0:81              0.0.0.0:*               LISTEN      566/nginx: master p
tcp6       0      0 :::80                   :::*                    LISTEN      566/nginx: master p
unix  3      [ ]         STREAM     CONNECTED     36277    566/nginx: master p
unix  3      [ ]         STREAM     CONNECTED     36278    566/nginx: master p
unix  3      [ ]         STREAM     CONNECTED     36275    566/nginx: master p
unix  3      [ ]         STREAM     CONNECTED     36276    566/nginx: master p
```


于是说明我们的nginx监听了81,80端口.修改了81端口的访问权限之后仍然有报错403, 一般有如下的两个原因:
* index.html不存在（极大部分原因）
* 权限不够

此时然后查看nginx的日志
```shell
sudo vim -R /var/log/nginx/error.log
```

```text
2023/12/18 19:01:39 [error] 1702#1702: *18 directory index of "/home/" is forbidden, 
client: 103.172.183.221, server: , request: "GET / HTTP/1.1", host: "117.72.10.237:81"
```

进一步，网页的根目录设置为 `/home/`,然而这个下面的文件信息为：

``` text
drwxr-xr-x  3 root root 4096 Dec 18 15:12 .
drwxr-xr-x 19 root root 4096 Dec 18 18:39 ..
drwxr-xr-x  7 user  user  4096 Dec 18 19:13 zpd
```

我们只需要把上面的`test.conf`中的 root设置为 `/home/user/,然后使用如下命令重启nginx
```shell
sudo nginx -s reload
```

> 更多请参考:[解决Nginx出现403 forbidden](https://cloud.tencent.com/developer/article/1949092)


## nginx的配置文件误删除
首先删除nginx及其配置文件，使用命令
```shell
sudo apt-get purge nginx nginx-common nginx-full
```
`purge`选项可以在删除软件的同时删除其配置文件,然后重新安装nginx
```shell
sudo apt-get install nginx
```

如果不行，用如下的命令
``` shell
sudo dpkg --force-confmiss -i /var/cache/apt/archives/nginx-common_*.deb
```


> 参考:[Ubuntu – 如何恢复/etc/nginx](https://www.jianshu.com/p/73983662ae19)


## 普通用户启动Vim,root保存
使用如下的命令即可：
```shell
:w !sudo tee %
```

> 参考: https://developer.aliyun.com/article/27531


## Azure服务器双重防火墙
我在Azure的虚拟机平台放行了9000端口，但是本地的ufw仍然如下:

```rext
user@GhostBlog:/etc/nginx/conf.d$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
80,443/tcp (Nginx Full)    ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
80,443/tcp (Nginx Full (v6)) ALLOW IN    Anywhere (v6)
```

也就是还需要本地放行9000端口,使用如下命令添加放行端口
```shell
sudo ufw allow 9000

# 放行端口生效
sudo ufw enable
```

然后的最终结果为:

```text 
user@GhostBlog:/etc/nginx/conf.d$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
Nginx Full                 ALLOW       Anywhere
9000                       ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
Nginx Full (v6)            ALLOW       Anywhere (v6)
9000 (v6)                  ALLOW       Anywhere (v6)
```

然后就可以加载了。
> 1.但是感觉京东云应该也可以这样弄，启用默认的ufw，但是多了一步验证
> 2.想要参考更多的关于ufw的细节可以参考 https://blog.xiaoz.org/archives/18666




