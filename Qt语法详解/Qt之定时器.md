---
title: Qt之定时器
date: 2019-01-23 20:44:20
categories: Qt语法详解
---
&emsp;&emsp;`Qt`使用定时器有两种方法，一种是使用`QObiect`类的定时器，一种是使用`QTimer`类。定时器的精确性依赖于操作系统和硬件，大多数平台支持`20ms`的精确度。

### QObject类的定时器

&emsp;&emsp;`QObject`是所有`Qt`对象的基类，它提供了一个基本的定时器。通过`QObject::startTimer`，可以把一个一毫秒为单位的时间间隔作为参数来开始定时器，这个函数返回一个唯一的整数定时器的标识符。这个定时器开始就会在每一个时间间隔`触发`，直到明确地使用这个定时器的标识符来调用`QObject::killTimer`结束。当定时器触发时，应用程序会发送一个`QTimerEvent`。在事件循环中，处理器按照事件队列的顺序来处理定时器事件。当处理器正忙于其它事件处理时，定时器就不能立即处理。
&emsp;&emsp;`QObject`类还提供定时期的功能。与定时器相关的成员函数有`startTimer`、`timeEvent`、`killTimer`。`startTimer`原型如下：

``` cpp
intQObject::startTimer ( int interval );
```

开始一个定时器并返回定时器`ID`，如果不能开始一个定时器，将返回`0`。定时器开始后，每隔`interval`毫秒间隔将触发一次超时事件，直到`killTimer`被调用来删除定时器。如果`interval`为`0`，那么定时器事件每次发生时没有窗口系统事件处理。
&emsp;&emsp;虚函数`timerEvent`被重载来实现用户的超时事件处理函数。如果有多个定时器在运行，`QTimerEvent::timerId`被用来查找指定定时器，对其进行操作。当定时器事件发生时，虚函数`timerEvent`随着`QTimerEvent`事件参数类一起被调用，重载这个函数可以获得定时器事件。定时器的用法如下：

``` cpp
/* 头文件 */
class QNewObject : publicQObject {
    Q_OBJECT
public:
    QNewObject ( QObject *parent = 0 );
    virtual ~QNewObject();
protected:
    void timerEvent ( QTimerEvent *event );
    int m_nTimerId;
};
​
/* 源文件 */
QNewObject::QNewObject ( QObject *parent ) : QNewObject ( parent ) {
    m_nTimerId = startTimer ( 1000 );
}
​
QNewObject::~QNewObject() {
    if ( m_nTimerId != 0 ) {
        killTimer ( m_nTimerId );
    }
}
​
voidQNewObject::timerEvent ( QTimerEvent *event ) {
    qDebug ( "timer event, id %d", event->timerId() );
}
```

### 定时器类QTimer

&emsp;&emsp;定时器类`QTimer`提供当定时器触发的时候发射一个信号的定时器，它提供只触发一次的超时事件，通常的使用方法如下：

``` cpp
QTimer *testTimer = newQTimer ( this ); /* 创建定时器 */
/* 将定时器超时信号与槽(功能函数)联系起来 */
connect ( testTimer, SIGNAL ( timeout() ), this, SLOT ( testFunction() ) );
testTimer->start ( 1000 ); /* 开始运行定时器，定时时间间隔为1000ms */
​
if ( testTimer->isActive() ) { /* 停止运行定时器 */
    testTimer->stop();
}
```

`QTimer`还提供了一个简单的只有一次定时的函数`singleShot`。 一个定时器在`100ms`后触发处理函数`animateTimeout`并且只触发一次。代码如下：

``` cpp
QTimer::singleShot ( 100, this, SLOT ( animateTimeout() ) );
```

&emsp;&emsp;`widget.h`如下：

``` cpp
#ifndef WIDGET_H
#define WIDGET_H
​
#include <QWidget>
​
namespace Ui {
    class Widget;
}
​
class Widget : public QWidget {
    Q_OBJECT
public:
    explicit Widget ( QWidget *parent = 0 );
    ~Widget();
protected:
    void timerEvent ( QTimerEvent *event );
private slots:
    void timerUpdate();
private:
    Ui::Widget *ui;
    int id1, id2, id3;
};
​
#endif // WIDGET_H
```

&emsp;&emsp;`widget.cpp`如下：

``` cpp
#include "widget.h"
#include "ui_widget.h"
#include <QDebug>
#include <QTimer>
#include <QTime>
​
Widget::Widget ( QWidget *parent ) : QWidget ( parent ), ui ( new Ui::Widget ) {
    ui->setupUi ( this );
    /* 开启一个1秒定时器，返回其ID */
    id1 = startTimer ( 1000 );
    id2 = startTimer ( 2000 );
    id3 = startTimer ( 3000 );
    QTimer *timer = new QTimer ( this ); /* 创建一个新的定时器 */
    /* 关联定时器的溢出信号到我们的槽函数上 */
    connect ( timer, SIGNAL ( timeout() ), this, SLOT ( timerUpdate() ) );
    timer->start ( 1000 ); /* 设置溢出时间为1秒，并启动定时器 */
    /* 为随机数设置初值 */
    qsrand ( QTime ( 0, 0, 0 ).secsTo ( QTime::currentTime() ) );
    /* singleShot函数用来开启一个只运行一次的定时器。该代码的作用是让程序运行10秒后自动关闭。*/
    QTimer::singleShot ( 10000, this, SLOT ( close() ) );
}
​
Widget::~Widget() {
    delete ui;
}
​
void Widget::timerEvent ( QTimerEvent *event ) {
    /* 判断是哪个定时器 */
    if ( event->timerId() == id1 ) {
        qDebug() << "timer1";
    } else if ( event->timerId() == id2 ) {
        qDebug() << "timer2";
    } else {
        qDebug() << "timer3";
    }
}
​
void Widget::timerUpdate() { /* 定时器溢出处理 */
    QTime time = QTime::currentTime(); /* 获取当前时间 */
    QString text = time.toString ( "hh:mm" ); /* 转换为字符串 */
​
    /* 注意单引号间要输入一个空格。每隔一秒就将“:”显示为空格 */
    if ( ( time.second() % 2 ) == 0 ) {
        text[2] = ' ';
    }
​
    ui->lcdNumber->display ( text );
    int rand = qrand() % 300; /* 产生300以内的正整数 */
    ui->lcdNumber->move ( rand, rand );
}
```