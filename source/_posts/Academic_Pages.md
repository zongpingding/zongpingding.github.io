---
title: Academic Pages
tags: Academic
categories: 博客
date: 2024-08-25 00:00:00
---


## 基本环境配置

需要 `jekyll`, 一个很重要的前置工作是安装一个很重要的  [DevKit](http://rubyinstaller.org/add-ons/devkit/). 这个就相当于是工具链，否则你的 `jekyll` 可能会安装失败. 但是安装 `DevKit` 的前置条件是安装一个 `MSYS2` (感觉我的安装过好多次这个`类 Unix shell` 环境了). 完成上述步骤之后，按照: [官网](https://jekyllrb.com/) 安装即可. 这里还是再抄一次:

``` shell
gem install bundler jekyll

# check jekyll status
jekyll -v 
```

配置好这些环境后，安装本项目的依赖, 使用命令:

``` shell
bundle install
```

## 启动项目

官方的命令无法启动此项目，请使用如下命令启动项目:

```shell
bundle exec jekyll serve -l -H localhost
```

但是你仍有可能会遇到一个 bug，说你的 `time-zone` 有问题，可以参见: [time-zone](https://github.com/tzinfo/tzinfo/wiki/Resolving-TZInfo::DataSourceNotFound-Errors). 其实就是把当前项目文件下的 `Gemfile` 中添加对应的语句:

``` shell
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw]
```

然后就可以正常启动项目了.
