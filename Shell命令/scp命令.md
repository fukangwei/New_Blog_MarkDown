---
title: scp命令
categories: Shell编程
date: 2019-01-19 18:11:43
---
&emsp;&emsp;`scp`用于远程拷贝文件，命令格式如下：<!--more-->

``` bash
scp 可选参数 file_source file_target
```

&emsp;&emsp;从本地复制单个文件到远程服务器：

``` bash
scp local_file remote_username@remote_ip:remote_folder
```

应用实例如下：

``` bash
# 复制当前目录下的1.mp3至服务器
scp 1.mp3 fkw@192.168.0.235:/home/fkw/music
```

&emsp;&emsp;从本地复制文件夹到远程服务器：

``` bash
scp -r local_folder remote_username@remote_ip:remote_folder
```

应用实例如下：

``` bash
# 将本地music文件夹复制到远程服务器
scp -r ./music/ fkw@192.168.0.235:/home/fkw/music
```

&emsp;&emsp;从远程服务器复制文件(或文件夹)到本地：

``` bash
# 将远程服务器上的music文件夹复制到本地
scp -r fkw@192.168.0.235:/home/fkw/music ./
```