---
title: Qt延时的方法
categories: Qt应用示例
date: 2018-12-28 16:01:08
---
&emsp;&emsp;1. 使用`QTimer::singleShot`的方法：<!--more-->

``` cpp
void QTimer::singleShot ( int msec, QObject *receiver, const char *member ) [static]
```

实例如下：

``` cpp
#include <QApplication>
#include <QTimer>

int main ( int argc, char *argv[] ) {
    QApplication app ( argc, argv );
    QTimer::singleShot ( 600000, &app, SLOT ( quit() ) );
    ...
    return app.exec();
}
```