---
title: Qt自动销毁窗体类对象
categories: Qt语法详解
abbrlink: dfe1d7b3
date: 2019-01-02 09:50:20
---
&emsp;&emsp;看下面一段代码：<!--more-->

``` cpp
QMainWindow *ImgWindow1;
ImgWindow1 = new QMainWindow ( this );
ImgWindow1->show();
​
connect ( ImgWindow1, SIGNAL ( destroyed() ), this, SLOT ( CloseImgWindow() ) );
​
void QMainFunction::CloseImgWindow() {
    qDebug() << "It is Destroyed!";
}
```

但在窗口关闭时，没有执行`qDebug`那句代码。
&emsp;&emsp;解决方法：要对窗口设置`WA_DeleteOnClose`属性，默认情况下关闭窗口仅仅意味着隐藏它：

``` cpp
ImgWindow1->setAttribute ( Qt::WA_DeleteOnClose, true );
```