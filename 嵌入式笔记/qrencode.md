---
title: qrencode
categories: 嵌入式笔记
abbrlink: '56453724'
date: 2019-01-17 17:54:29
---
&emsp;&emsp;`QR`码是当前最流行的二维码之一，它具有可靠性高、识别速度快等特点。而`qrencode`则是由`C`语言写成的`QR`码生成与解码的函数库，它以`GNU LGPL`协议发布，是当前最常用的`QR`码识别函数库。<!--more-->
&emsp;&emsp;`Libqrencode`是完全开源的函数库，可以到它的项目主页`https://fukuchi.org/works/qrencode/`下载源程序。
&emsp;&emsp;解压完成后得到一个目录，里面就是`qrencode`的代码。进入这个目录，编译并安装`qrencode`：

``` bash
./configure
make
sudo make install
```

默认情况下，库文件会安装在`/usr/local/lib`目录下，而头文件会安装在`/usr/local/include`目录下。除此之外，还会在`/usr/local/bin`下面安装一个`qrencode`的执行文件，它是使用`libqrencode`做成的一个可以生成`QR`码图片的实例程序。安装完成后，使用如下命令：

``` bash
$ qrencode -V
qrencode version 3.4.4
Copyright (C) 2006-2012 Kentaro Fukuchi
```

在终端可以这样测试：

``` bash
qrencode -o test.png "https://www.baidu.com"
```

&emsp;&emsp;注意，`qrencode`库依赖`PNG`库或者`SDL`库；编译使用了`qrencode`库的项目时要加上`-lqrencode`选项。