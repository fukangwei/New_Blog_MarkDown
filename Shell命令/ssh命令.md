---
title: ssh命令
categories: Shell编程
date: 2019-01-19 18:26:24
---
&emsp;&emsp;使用`ssh`命令可以登录远程主机，该命令需要`user`和`hostname`：<!--more-->

``` bash
ssh user@hostname
```

- `user`：用户名。
- `hostname`：可以是`IP`地址或者域名。

使用示例：

``` bash
ssh pi@10.42.0.47
```