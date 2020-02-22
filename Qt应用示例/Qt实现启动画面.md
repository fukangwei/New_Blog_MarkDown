---
title: Qt实现启动画面
categories: Qt应用示例
date: 2018-12-28 16:22:52
---
&emsp;&emsp;如果一个程序的启动比较耗时，为了不让用户枯燥地等待或者是误以为程序运行异常了，我们都会在启动比较耗时的程序中加上启动界面。在`Qt`中实现启动界面，主要就是使用`QSplashScreen`类：<!--more-->

``` cpp
#include <QApplication>
#include <QSplashScreen>
#include <QPixmap>
#include <mainwindow.h>
#include <QDebug>
#include <QDateTime>

int main ( int argc, char *argv[] ) {
    QApplication app ( argc, argv );
    QPixmap pixmap ( "screen.png" );
    QSplashScreen screen ( pixmap );
    screen.show();
    screen.showMessage ( "LOVE", Qt::AlignCenter, Qt::red );
    /*-----------------------------------------------------*/
    QDateTime n = QDateTime::currentDateTime();
    QDateTime now;

    do {
        now = QDateTime::currentDateTime();
        app.processEvents();
    } while ( n.secsTo ( now ) <= 5 ); /* 延时5秒 */
    /*----------------------------------------------------*/
    MainWindow window;
    window.show();
    screen.finish ( &window );
    return app.exec();
}
```

&emsp;&emsp;如果需要实现带进度条的启动界面，需要实现如下代码：
&emsp;&emsp;`mysplashscreen.h`如下：

``` cpp
#ifndef __MYSPLASHSCREEN_H
#define __MYSPLASHSCREEN_H
#include <QtGui>

class MySplashScreen: public QSplashScreen {
    Q_OBJECT
private:
    QProgressBar *ProgressBar;
public:
    MySplashScreen ( const QPixmap &pixmap );
    ~MySplashScreen();
    void setProgress ( int value );
    void show_started ( void );
private slots:
    void progressChanged ( int );
};

#endif // __MYSPLASHSCREEN_H
```

&emsp;&emsp;`mysplashscreen.cpp`如下所示：

``` cpp
#include "mysplashscreen.h"
#include <QDateTime>

MySplashScreen::MySplashScreen ( const QPixmap &pixmap ) : QSplashScreen ( pixmap ) {
    ProgressBar = new QProgressBar ( this ); /* 父类为MySplashScreen */
    ProgressBar->setGeometry ( 0, pixmap.height() - 50, pixmap.width(), 30 );
    ProgressBar->setRange ( 0, 100 );
    ProgressBar->setValue ( 0 );
    /* 值改变时，立刻repaint */
    connect ( ProgressBar, SIGNAL ( valueChanged ( int ) ), this, SLOT ( progressChanged ( int ) ) );
    QFont font;
    font.setPointSize ( 32 );
    ProgressBar->setFont ( font ); /* 设置进度条里面的字体 */
}

MySplashScreen::~MySplashScreen() {
}

void MySplashScreen::setProgress ( int value ) {
    ProgressBar->setValue ( value );
}

void MySplashScreen::progressChanged ( int ) {
    repaint();
}

void MySplashScreen::show_started ( void ) {
    this->show(); /* 显示 */
    this->setProgress ( 30 ); /* 显示30% */
    /* 这里需要插入延时函数 */
    this->setProgress ( 60 );
    /* 这里需要插入延时函数 */
    this->setProgress ( 90 );
    /* 这里需要插入延时函数 */
    this->setProgress ( 100 );
}
```

&emsp;&emsp;`main.cpp`如下：

``` cpp
#include "widget.h"
#include <QApplication>
#include <QSplashScreen>
#include <QPixmap>
#include "mysplashscreen.h"

int main ( int argc, char *argv[] ) {
    QApplication app ( argc, argv );
    MySplashScreen *splash = new MySplashScreen ( QPixmap ( "./image/miaojie.png" ) );
    splash->show_started();
    app.processEvents();
    Widget w;
    w.show();
    splash->finish ( &w );
    return app.exec();
}
```