---
title: Qt调用外部程序
categories: Qt语法详解
date: 2019-01-02 11:18:45
---
&emsp;&emsp;`Qt`调用外部程序有下面几种方法：<!--more-->

``` cpp
/* 通过调用linux的C函数 */
system ( "opt/myApp" );
/* 通过QProcess的阻塞调用 */
QProcess::execute ( "/opt/myApp" );
/* 通过QProcess的非阻塞调用*/
QProcess *pro = new QProcess;
pro->start ( "/opt/myApp" );
```

&emsp;&emsp;第一种方法是调用`linux`中`C`函数库中的`system(const char *string);`，第二种方法和第三种方法是调用`Qt`的函数。需要说明的是：

- 前两种方法会阻塞进程，直到`myApp`程序结束；而第三种方法则不会阻塞进程，可以多任务运行。
- `Qt`在运行时，需要启动`qws`服务。如果使用前面两种方法，需要在运行时新开启一个`qws`，否则不能运行；而用第三种方法不需要再开启`qws`，它和主进程共用一个`qws`。
- 第三种方法虽然不会阻塞，但是有可能在终端上看不到打印出来的信息。所以要在终端显示信息时，可以考虑用阻塞模式。

### 关于QWS

&emsp;&emsp;`QWS`的全称是`Qt windows system`，它是`Qt`自行开发的窗口系统，体系结构类似`X Windows`，是一个`C/S`结构，由`QWS Server`在物理设备上显示，由`QWS Client`实现界面，两者通过`socket`进行彼此的通讯。在很多嵌入式系统里，`Qt`程序基本上都是用`QWS`来实现，这样保证程序的可移植性。
&emsp;&emsp;另外，在运行`Qt`程序时添加`-qws`参数，表示这个程序是`QWS Server`，否则是`QWS Client`。任何一个基于`Qt`的`application`都可以做为`QWS Server`。当然，`QWS Server`一定先于`QWS Client`启动，否则`QWS Client`将启动失败。在实际应用中，系统会指定某个特殊的`application`做`QWS Server`，这个`application`一般还会管理一些其它的系统资源。