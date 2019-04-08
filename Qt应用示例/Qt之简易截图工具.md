---
title: Qt之简易截图工具
date: 2018-12-28 16:08:14
categories: Qt应用示例
---
&emsp;&emsp;`widget.h`如下：

``` cpp
#ifndef WIDGET_H
#define WIDGET_H
​
#include <QWidget>
#include "QPixmap.h"
#include "QTimer.h"
​
namespace Ui {
    class Widget;
}
​
class Widget : public QWidget {
    Q_OBJECT
​
public:
    explicit Widget ( QWidget *parent = 0 );
    ~Widget();
​
private slots:
    void on_cut_screen_clicked();
    void changeValue();
​
private:
    Ui::Widget *ui;
    QPixmap pixmap;
    QTimer *timer;
};
​
#endif // WIDGET_H
```

&emsp;&emsp;`widget.cpp`如下所示：

``` cpp
#include "widget.h"
#include "ui_widget.h"
#include "QDesktopWidget.h"
#include "QPixmap.h"
#include "QFileDialog.h"
#include "QDesktopServices.h"
#include "QClipboard.h"
​
Widget::Widget ( QWidget *parent ) : QWidget ( parent ),
    ui ( new Ui::Widget ) {
    ui->setupUi ( this );
    timer = new QTimer();
    timer->setInterval ( 1000 ); /* 设置超时时间为1秒 */
    connect ( timer, SIGNAL ( timeout() ), this, SLOT ( changeValue() ) );
}
​
Widget::~Widget() {
    delete ui;
}
​
void Widget::on_cut_screen_clicked() {
    timer->start();
    this->hide(); /* 将当前窗口进行隐藏 */
    pixmap = QPixmap::grabWindow ( QApplication::desktop()->winId() );
    ui->label->setPixmap ( pixmap.scaled ( ui->label->size() ) );
    QString fileName = QFileDialog::getSaveFileName (
        this, "save file",
        QDesktopServices::storageLocation ( QDesktopServices::PicturesLocation )
    );
    pixmap.save ( fileName );
    QClipboard *clipboard = QApplication::clipboard();
    clipboard->setPixmap ( pixmap );
}
​
void Widget::changeValue() {
    this->show(); /* 显示主窗口 */
    timer->stop();
}
```

该代码实现了截图、保存截图文件以及剪切板的功能。