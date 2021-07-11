---
title: 安装SQLite
categories: 软件与硬件问题
date: 2019-02-06 18:02:14
---
### Linux下的安装

&emsp;&emsp;使用如下命令进行安装：<!--more-->

``` bash
sudo apt-get install sqlite3
```

执行命令`sqlite3 -version`，查看`sqlite`的版本。

### Windows下的安装

&emsp;&emsp;访问`SQLite`的下载页面`https://sqlite.org/download.html`，并下载`sqlite-dll-win64-*.zip`和`sqlite-tools-win32-*.zip`。
&emsp;&emsp;创建文件夹`C:\sqlite`，并在此文件夹下解压下载的两个文件，将得到`sqlite3.def`、`sqlite3.dll`和`sqlite3.exe`。
&emsp;&emsp;添加`C:\sqlite`到`PATH`环境变量，执行`sqlite3 -version`命令，查看`sqlite`的版本。