---
title: Qt延时的方法
categories: Qt应用示例
abbrlink: 4bd9ba2
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
​
int main ( int argc, char *argv[] ) {
    QApplication app ( argc, argv );
    QTimer::singleShot ( 600000, &app, SLOT ( quit() ) );
    ...
    return app.exec();
}
```

&emsp;&emsp;2. 如下代码可以让程序以毫秒延时：

``` cpp
QTime n = QTime::currentTime();
QTime now;
​
do {
    now = QTime::currentTime();
} while ( n.msecsTo ( now ) <= 500 ); /* 延迟500毫秒 */
```

&emsp;&emsp;3. 如下代码可以让程序以秒延时：

``` cpp
QDateTime n2 = QDateTime::currentDateTime();
QDateTime now;
​
do {
    now = QDateTime::currentDateTime();
} while ( n2.secsTo ( now ) <= 6 ); /* 延时6秒 */
```