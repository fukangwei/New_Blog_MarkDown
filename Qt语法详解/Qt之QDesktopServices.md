---
title: Qt之QDesktopServices
date: 2019-01-02 09:56:28
categories: Qt语法详解
---
&emsp;&emsp;如果使用`Qt`开发界面，往往离不开`QDesktopServices`。`QDesktopServices`不仅可以打开本地浏览器，而且还可以打开本地文件(或文件夹)，可以获取桌面、我的文档、`Home`等目录。
&emsp;&emsp;1. 打开浏览器网页：

``` cpp
QUrl url ( QString ( "www.google.com" ) );
bool is_open = QDesktopServices::openUrl ( url );
```

&emsp;&emsp;2. 打开本地文件(或文件夹)、可执行程序等：

``` cpp
QString local_path = QString ( "E:/新建文件夹" );
QString path = QString ( "file:///" ) + local_path;
bool is_open = QDesktopServices::openUrl ( QUrl ( path, QUrl::TolerantMode ) );
```

`local_path`可以是文件(或文件夹)路径、可执行程序路径。`local_path`为文件时，会选择默认打开方式进行打开。
&emsp;&emsp;3. 获取桌面、我的文档、`Home`等目录的路径：

``` cpp
QString desktop_path = QDesktopServices::storageLocation ( QDesktopServices::DesktopLocation );
QString document_path = QDesktopServices::storageLocation ( QDesktopServices::DocumentsLocation );
QString home_path = QDesktopServices::storageLocation ( QDesktopServices::HomeLocation );
QString application_path = QDesktopServices::storageLocatio ( QDesktopServices::ApplicationsLocation );
QString temp_path = QDesktopServices::storageLocation ( QDesktopServices::TempLocation );
```