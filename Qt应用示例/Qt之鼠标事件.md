---
title: Qt之鼠标事件
categories: Qt应用示例
abbrlink: a99b52a0
date: 2018-12-28 16:17:19
---
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
    void mousePressEvent ( QMouseEvent *event );
    void mouseReleaseEvent ( QMouseEvent *event );
    void mouseDoubleClickEvent ( QMouseEvent *event );
    void mouseMoveEvent ( QMouseEvent *event );
    void wheelEvent ( QWheelEvent *event );
private:
    Ui::Widget *ui;
    QPoint offset; /* 用来储存鼠标指针位置与窗口位置的差值 */
};
​
#endif // WIDGET_H
```

&emsp;&emsp;`widget.cpp`如下：

``` cpp
#include "widget.h"
#include "ui_widget.h"
#include <QMouseEvent>
​
Widget::Widget ( QWidget *parent ) : QWidget ( parent ), ui ( new Ui::Widget ) {
    ui->setupUi ( this );
    // setMouseTracking ( true ); /* 设置鼠标跟踪 */
    QCursor cursor; /* 创建光标对象 */
    cursor.setShape ( Qt::OpenHandCursor ); /* 设置光标形状 */
    setCursor ( cursor ); /* 使用光标 */
}
​
Widget::~Widget() {
    delete ui;
}
​
void Widget::mousePressEvent ( QMouseEvent *event ) { /* 鼠标按下事件 */
    if ( event->button() == Qt::LeftButton ) { /* 如果是鼠标左键按下 */
        QCursor cursor;
        cursor.setShape ( Qt::ClosedHandCursor ); /* 使鼠标指针暂时变为小手抓取的样子 */
        QApplication::setOverrideCursor ( cursor );
        offset = event->globalPos() - pos(); /* 获取指针位置和窗口位置的差值，以便移动时使用 */
    } else if ( event->button() == Qt::RightButton ) { /* 如果是鼠标右键按下 */
        QCursor cursor ( QPixmap ( "../yafeilinux.png" ) ); /* 使用自定义的图片作为鼠标指针 */
        QApplication::setOverrideCursor ( cursor );
    }
}
​
void Widget::mouseMoveEvent ( QMouseEvent *event ) { /* 鼠标移动事件 */
    if ( event->buttons() & Qt::LeftButton ) { /* 这里必须使用buttons */
        QPoint temp; /* 我们使用鼠标指针当前的位置减去差值，就得到了窗口应该移动的位置 */
        temp = event->globalPos() - offset;
        move ( temp );
    }
}
​
void Widget::mouseReleaseEvent ( QMouseEvent *event ) { /* 鼠标释放事件 */
    QApplication::restoreOverrideCursor(); /* 恢复鼠标指针形状 */
}
​
void Widget::mouseDoubleClickEvent ( QMouseEvent *event ) { /* 鼠标双击事件 */
    if ( event->button() == Qt::LeftButton ) { /* 如果是鼠标左键按下 */
        if ( windowState() != Qt::WindowFullScreen ) { /* 如果现在不是全屏，将窗口设置为全屏 */
            setWindowState ( Qt::WindowFullScreen );
        } else {
            setWindowState ( Qt::WindowNoState ); /* 如果现在已经是全屏状态，那么恢复以前的大小 */
        }
    }
}
​
void Widget::wheelEvent ( QWheelEvent *event ) { /* 滚轮事件 */
    /* 当滚轮远离使用者时进行放大，当滚轮向使用者方向旋转时进行缩小 */
    if ( event->delta() > 0 ) {
        ui->textEdit->zoomIn();
    } else {
        ui->textEdit->zoomOut();
    }
}
```

&emsp;&emsp;这里使用`globalPos`函数来获取鼠标指针的位置，这个位置是指针在桌面上的位置，因为窗口的位置就是指它在桌面上的位置。
&emsp;&emsp;鼠标移动时会检测所有按下的按键，而这时使用`QMouseEvent`的`button`函数无法获取哪个按键被按下，只能使用`buttons`函数，所以这里使用`buttons`和`Qt::LeftButton`进行按位与的方法来判断是否是鼠标左键按下。
&emsp;&emsp;在滚轮事件处理函数中，使用`QWheelEvent`类的`delta`函数获取了滚轮移动的距离。当滚轮向远离使用者的方向旋转时，返回正值；当向着靠近使用者的方向旋转时，返回负值。