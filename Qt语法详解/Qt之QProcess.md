---
title: Qt之QProcess
categories: Qt语法详解
date: 2019-01-24 16:31:18
---
### QProcess进程类

&emsp;&emsp;`Qt`提供了`QProcess`类用于启动外部程序并与之通信。启动一个新的进程的操作非常简单，只需要将待启动的程序名称和启动参数传递给`start`函数即可：<!--more-->

``` cpp
QObject *parent;
QString program = "tar";
QStringList arguments;
arguments << "czvf" << "backup.tar.gz" << "/home";
QProcess *myProcess = new QProcess ( parent );
QProcess->start ( program, arguments );
```

当调用`start`函数后，`myProcess`进程立即进入启动状态，但`tar`程序尚未被调用，不能读写标准输入输出设备。当进程完成启动后就进入`运行状态`，并向外发出`started`信号。
&emsp;&emsp;在输入输出方面，`QProcess`将一个进程看做一个流类型的`I/O`设备，可以像使用`QTcpSocket`读写流类型的网络连接一样来读写一个进程。可以通过`QIODevice::write`函数向所启动进程的标准输入写数据，也可以通过`QIODevice::read`、`QIODevice::readLine`和`QIODevice::getChar`函数从这个进程的标准输出读数据。由于`QProcess`是从`QIODevice`类继承而来的，因此它也可以作`QXmlReader`的数据来源，或者为`QFtp`产生上传数据。最后当进程退出时，`QProcess`进入起始状态(即`非运行状态`)，并发出`finished`信号。

``` cpp
void finished ( int exitCode, QProcess::ExitStatus exitStatus );
```

信号在参数中返回了进程退出的退出码和退出状态，可以调用`exitCode`函数和`exitStatus`函数分别获取最后退出进程的这两个值。`Qt`定义的进程`退出状态`只有正常退出和进程崩溃两种，分别对应值`QProcess::NormalExit`(值为`0`)和`QProcess::CrashExit`(值为`1`)。当进程在运行中产生错误时，`QProcess`将发出`error`信号，可以通过调用`error`函数返回最后一次产生错误的类型，并通过`state`找出此时进程所处的状态。`Qt`定义了如下的进程错误代码：

错误常量                   | 值  | 描述
--------------------------|-----|------
`QProcess::FailedToStart` | `0` | 进程启动失败
`QProcess::Crashed`       | `1` | 进程成功启动后崩溃
`QProcess::Timedout`      | `2` | 最后一次调用`waitFor`函数超时，此时`QProcess`状态不变，并可以再次调用`waitFor`类型的函数
`QProcess::WriteError`    | `3` | 向进程写入时出错，例如进程尚未启动或者输入通道被关闭时
`QProcess::ReadError`     | `4` | 从进程中读取数据时出错，例如进程尚未启动时
`QProcess::UnknownError`  | `5` | 未知错误，这也是`error`函数返回的默认值

&emsp;&emsp;可以通过调用`setReadChanned`函数设置当前的读通道，当有可读数据时，`Qt`将发出`readyRead`信号；如果是从标准输出和标准错误通道中读取数据，还会发出`readyReadStandardOutput`信号；如果是从标准错误读取，也会发出`readyReadStandardError`信号。`readAllStandardOutput`函数从标准输出通道中读取数据，`readAllStandardErrot`函数从标准错误通道中读取数据。在进程启动以前，以`MergedChannels`为参数调用`setReadChannelMode`函数，可以把标准输出通道和标准输错误通道合并。

``` cpp
#include <QApplication>
#include <QProcess>
#include <QString>
#include <iostream>

int main ( int argc, char *argv[] ) {
    QApplication app ( argc, argv );
    QProcess proc;
    QStringList arguments;
    arguments << "-na";
    proc.start ( "netstat", arguments );

    /* 等待进程启动 */
    if ( !proc.waitForStarted() ) {
        std::cout << "启动失败\n";
        return false;
    }

    proc.closeWriteChannel(); /* 关闭写通道，因为没有向进程写数据 */
    QByteArray procOutput; /* 用于保存进程的控制台输出 */

    /* 等待进程结束 */
    while ( false == proc.waitForFinished() ) {
        std::cout << "结束失败\n";
        return 1;
    }

    procOutput = proc.readAll(); /* 读取进程输出到控制台的数据 */
    std::cout << procOutput.data() << std::endl; /* 输出读到的数据 */
    return EXIT_SUCCESS; /* 返回EXIT_SUCCESS */
}
```

### Qt之进程间交互

&emsp;&emsp;`mainwindow.h`如下：

``` cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QtGui>

class MainWindow : public QMainWindow {
    Q_OBJECT
public:
    MainWindow ( QWidget *parent = 0 );
    ~MainWindow();
private slots:
    void openProcess();
private:
    QProcess *p;
};

#endif // MAINWINDOW_H
```

&emsp;&emsp;`mainwindow.cpp`如下：

``` cpp
#include "mainwindow.h"

MainWindow::MainWindow ( QWidget *parent ) : QMainWindow ( parent ) {
    p = new QProcess ( this );
    QPushButton *bt = new QPushButton ( "execute notepad", this );
    connect ( bt, SIGNAL ( clicked() ), this, SLOT ( openProcess() ) );
}

MainWindow::~MainWindow() {
}

void MainWindow::openProcess() {
    p->start ( "notepad.exe" );
}
```

这个窗口只有一个按钮，当你点击按钮之后，程序会调用`Windows`的记事本。`QProcess::start`接收两个参数，第一个是要执行的命令或者程序，这里就是`notepad.exe`；第二个是`QStringList`类型的数据，也就是需要传递给这个程序的运行参数。注意，这个程序能够被系统找到。
&emsp;&emsp;调用一个系统命令，这里使用的是`Windows`，因此需要调用`dir`；如果你是在`Linux`进行编译，就需要改成`ls`。
&emsp;&emsp;`mainwindow.h`如下：

``` cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QtGui>

class MainWindow : public QMainWindow {
    Q_OBJECT
public:
    MainWindow ( QWidget *parent = 0 );
    ~MainWindow();
private slots:
    void openProcess();
    void readResult ( int exitCode );
private:
    QProcess *p;
};

#endif // MAINWINDOW_H
```

&emsp;&emsp;`mainwindow.cpp`如下：

``` cpp
#include "mainwindow.h"

MainWindow::MainWindow ( QWidget *parent ) : QMainWindow ( parent ) {
    p = new QProcess ( this );
    QPushButton *bt = new QPushButton ( "execute notepad", this );
    connect ( bt, SIGNAL ( clicked() ), this, SLOT ( openProcess() ) );
}

MainWindow::~MainWindow() {
}

void MainWindow::openProcess() {
    p->start ( "cmd.exe", QStringList() << "/c" << "dir" );
    connect ( p, SIGNAL ( finished ( int ) ), this, SLOT ( readResult ( int ) ) );
}

void MainWindow::readResult ( int exitCode ) {
    if ( exitCode == 0 ) {
        QTextCodec *gbkCodec = QTextCodec::codecForName ( "GBK" );
        QString result = gbkCodec->toUnicode ( p->readAll() );
        QMessageBox::information ( this, "dir", result );
    }
}
```

在按钮点击的`slot`函数中，通过`QProcess::start`函数运行了指令`cmd.exe /c dir`，意思是打开系统的`cmd`程序，然后运行`dir`指令。然后将`process`的`finished`信号连接到新增加的`slot`函数。`signal`函数有一个`int`型参数，我们知道，对于`C/C++`程序而言，`main`函数总是返回一个`int`，也就是退出代码，用于指示程序是否正常退出，这里的`int`参数就是这个退出代码。`slot`函数首先检查退出代码是否为`0`，如果退出代码为`0`，说明是正常退出。然后把结果显示在`QMessageBox`中。`QProcess::readAll`函数可以读出程序输出内容，由于它的返回结果是`QByteArray`型，所以再转换成`QString`显示出来。中文版`Windows`使用的是`GBK`编码，而`Qt`使用的是`Unicode`编码，因此需要做一下转换，否则会出现乱码。