---
title: Qt获取MD5值
categories: Qt应用示例
abbrlink: ef49342a
date: 2018-12-28 16:05:08
---
&emsp;&emsp;`QT`提供了`QCryptographicHash`类，可以很方便地实现`MD5`等加密算法。
&emsp;&emsp;第一种方法如下：

``` cpp
#include <QtCore/QCoreApplication>
#include <QCryptographicHash>
#include <iostream>
​
int main ( int argc, char *argv[] ) {
    QCoreApplication a ( argc, argv );
    QString pwd = "abcdef";
    QString md5;
    QByteArray ba, bb;
    QCryptographicHash md ( QCryptographicHash::Md5 );
    ba.append ( pwd );
    md.addData ( ba );
    bb = md.result();
    md5.append ( bb.toHex() );
    std::cout << md5.toStdString() << std::endl;
    return a.exec();
}
```

&emsp;&emsp;第二种方法如下：

``` cpp
#include <QtCore/QCoreApplication>
#include <QCryptographicHash>
#include <iostream>
​
int main ( int argc, char *argv[] ) {
    QCoreApplication a ( argc, argv );
    QString md5;
    QString pwd = "abcdef";
    QByteArray bb;
    bb = QCryptographicHash::hash ( pwd.toAscii(), QCryptographicHash::Md5 );
    md5.append ( bb.toHex() );
    std::cout << md5.toStdString() << std::endl;
    return a.exec();
}
```