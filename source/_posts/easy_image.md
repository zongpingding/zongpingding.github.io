---
title: Easyimage图床搭建
tags: [Hexo, 图床]
categories: [博客]
date: 2024-03-26 00:00:00
---


## 前言
这里主要是基于以下仓库: [EasyImages2.0I](https://github.com/icret/EasyImages2.0)


之前参考的多篇文章都是使用宝塔面板来进行安装EasyImage，但是我在这里坚决不使用宝塔，你使用宝塔就相当于把自己的root权限给了别人，把你的整个计算机给了别人。所以坚持自己搭建，对于宝塔是否留有后门先存疑?

还有一点值得吐糟的就是网上大部分的教程有如下弊端:
* 基于宝塔搭建（甚至官方github仓库都是这个方法，我不知道为什么）
* 大部分的教程都是错的，甚至我在某个教程中发现了如下的代码:
    ``` php
    <?php
        // 顺便可以看一下php的扩展全不全
        phpinfo();
    ```
    完整的代码不应该是这样的吗?
    ```php
    <?php
        // 顺便可以看一下php的扩展全不全
        phpinfo();
    ?>
    ```

> 本博客主要[参考文章](https://chenhaotian.top/linux-app/easy-image/index.html)


## 主要步骤
基本全部都是照着上面的博客来，但是我做了如下改变，把原文去修改如下的文件`/etc/nginx/sites-enabled/default`变成了目录 `/etc/nginx/conf.d/`中增加配置`server`项`easyimage.conf`，主要内容如下:

``` toml
server {
    server_name zpding.tech;
    listen 9000;
    root /var/www/EasyImages2.0/;
    index index.php index.html;

    client_header_timeout 300s;
    client_body_timeout 300s; # 防止大图片上传超时
    client_max_body_size 2000m; # 应不小于100m，防止上传大图片 HTTP 413 错误

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }

    location ~* ^/(config|docs|i|public)/.*\.(php|php5)$ {
        deny all; # 安全设置
    }
}
```


同时也没有采用他的SSL证书的方法，目前仅打算个人使用，而且关键的是目前我只有一个域名。所以就暂时不绑定了，但是看到一篇文章说是可以一个域名绑定多个端口:[nginx配置一个域名多个端口](https://juejin.cn/s/nginx%E9%85%8D%E7%BD%AE%E4%B8%80%E4%B8%AA%E5%9F%9F%E5%90%8D%E5%A4%9A%E4%B8%AA%E7%AB%AF%E5%8F%A3)


目前服务器在国内，不打算备案，所以还没有去研究，等之后再研究.


## 文章备份
> * 为了防止参考博客删除，在此做一备份
> * 以下所有操作均在root下进行

### 安装PHP相关
``` shell
apt update -y
```

### PHP本体
``` shell
apt install php7.4 php7.4-fpm
```

### 拓展
```shell
apt install php7.4-fileinfo php7.4-iconv php7.4-zip php7.4-mbstring php7.4-gd
```

### PHP接口配置
```shell
vim /etc/php/7.4/fpm/php.ini
```

查找 `upload_max_filesize`, `post_max_size`, `memory_limit`,按需求设置，然后重启PHP
```shell
systemctl restart php7.4-fpm
```

### 安装Nginx
```shell
apt install nginx
```

### 安装easyimage
首先就是clone它的github仓库:
```shell
cd /var/www/
git clone https://github.com/icret/EasyImages2.0.git
```

#### 权限修改
```shell
chmod -R 755 /var/www/EasyImages2.0/
chown -R www-data:www-data /var/www/EasyImages2.0/
```

#### 配置Nginx
``` shell
vim /etc/nginx/conf.d/easyimage.conf
```


然后贴入最开始的内容:
``` toml
server {
    server_name zpding.tech;
    listen 9000;
    root /var/www/EasyImages2.0/;
    index index.php index.html;

    client_header_timeout 300s;
    client_body_timeout 300s; # 防止大图片上传超时
    client_max_body_size 2000m; # 应不小于100m，防止上传大图片 HTTP 413 错误

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }

    location ~* ^/(config|docs|i|public)/.*\.(php|php5)$ {
        deny all; # 安全设置
    }
}
```

#### 启动Nginx
```shell
nginx -t
# 这一步是可选的
nginx -s reload
systemctl restart nginx
```

SSL证书获得
``` shell
apt install certbot python3-certbot-nginx
certbot --nginx -d your_domain zpding.tech
# 测试SSL证书自动更新
certbot renew --dry-run
```

安装环境部署完毕，访问你的域名即可进行网站初始化


## 成果
在服务器中的具体位置如下:
``` text
user@GhostBlog:~$ l /var/www/EasyImages2.0/i/2023/12/19/
total 11612
drwxr-xr-x 2 www-data www-data    4096 Dec 18 17:11 .
drwxr-xr-x 3 www-data www-data    4096 Dec 18 17:11 ..
-rw-r--r-- 1 www-data www-data  317469 Dec 18 17:11 1u4vvf.png
-rw-r--r-- 1 www-data www-data 5832718 Dec 18 17:11 1u52is.png
-rw-r--r-- 1 www-data www-data 5722964 Dec 18 17:11 1u5e21.png
```

> 当然了，时间不同目录也就不同了
