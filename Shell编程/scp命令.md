---
title: scp命令
categories: Shell编程
abbrlink: a0bde6e5
date: 2019-01-19 18:11:43
---
&emsp;&emsp;`scp`是`secure copy`的缩写，它是`Linux`系统下安全的远程文件拷贝命令。

``` bash
scp [可选参数] file_source file_target
```

可选参数如下：

参数      | 说明
----------|-----
`-1`      | 强制`scp`命令使用协议`ssh1`
`-2`      | 强制`scp`命令使用协议`ssh2`
`-4`      | 强制`scp`命令只使用`IPv4`寻址
`-6`      | 强制`scp`命令只使用`IPv6`寻址
`-q`      | 不显示传输进度条
`-r`      | 递归复制整个目录
`-P port` | 指定数据传输使用端口号`port`

&emsp;&emsp;从本地复制到远程服务器：

``` bash
scp local_file remote_username@remote_ip:remote_folder
```

应用实例如下：

``` bash
# 复制当前目录下的1.mp3至服务器
scp ./1.mp3 fkw@192.168.0.235:/home/fkw/music
```

&emsp;&emsp;复制目录：

``` bash
scp -r local_folder remote_username@remote_ip:remote_folder
```

应用实例如下：

``` bash
# 将本地music目录复制到远程服务器
scp -r ./music/ fkw@192.168.0.235:/home/fkw/music
```

&emsp;&emsp;从远程复制到本地：

``` bash
# 将远程服务器上的music目录复制到本地
scp -r fkw@192.168.0.235:/home/fkw/music ./
```