---
title: Qt之QCloseEvent
date: 2019-01-02 10:40:04
categories: Qt语法详解
---
&emsp;&emsp;使用软件时常常会遇到这样的情况：点击关闭选项后，程序没有马上退出，而是跳出一个对话框，问是否确定退出软件。`Qt`同样提供了一个函数来实现这个功能，那就是`QCloseEvent`，其函数原型如下所示：

``` cpp
void QMainFrame::closeEvent ( QCloseEvent *event ) [virtual protected]
```

实际上它就是一个虚函数。接下来看它的具体实现：在`.h`文件中加入语句：

``` cpp
private:
    void closeEvent ( QCloseEvent *event );
```

同时不要忘记添加头文件`#include <QCloseEvent>`。在`.cpp`文件中加入：

``` cpp
void MainWindow::closeEvent ( QCloseEvent *event ) {
    int ret = QMessageBox::question (
        this, tr ( "Last Hint" ), tr ( "Are you sure you want to quit?" ),
        QMessageBox::Yes | QMessageBox::Default, QMessageBox::No | QMessageBox::Escape );
​
    if ( ret == QMessageBox::Yes ) {
        event->accept(); /* 接受该信号，窗口关闭 */
    }
    else {
        event->ignore(); /* 忽略该信号，窗口不关闭 */
    }
}
```