---
title: Qt之启动进程
categories: Qt应用示例
date: 2018-12-28 16:32:19
---
&emsp;&emsp;`mainwindow.h`如下：<!--more-->

``` cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include <QProcess>

namespace Ui {
    class MainWindow;
}

class MainWindow : public QMainWindow {
    Q_OBJECT
public:
    explicit MainWindow ( QWidget *parent = 0 );
    ~MainWindow();
private slots:
    void on_pushButton_clicked();
    void showResult();
    void showState ( QProcess::ProcessState );
    void showError();
    void showFinished ( int, QProcess::ExitStatus );
private:
    Ui::MainWindow *ui;
    QProcess myProcess;
};

#endif // MAINWINDOW_H
```

&emsp;&emsp;`mainwindow.cpp`如下：

``` cpp
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QDebug>

MainWindow::MainWindow ( QWidget *parent ) : QMainWindow ( parent ), ui ( new Ui::MainWindow ) {
    ui->setupUi ( this );
    connect ( &myProcess, SIGNAL ( readyRead() ), this, SLOT ( showResult() ) );
    connect ( &myProcess, SIGNAL ( stateChanged ( QProcess::ProcessState ) ),
              this, SLOT ( showState ( QProcess::ProcessState ) ) );
    connect ( &myProcess, SIGNAL ( error ( QProcess::ProcessError ) ), this, SLOT ( showError() ) );
    connect ( &myProcess, SIGNAL ( finished ( int, QProcess::ExitStatus ) ),
              this, SLOT ( showFinished ( int, QProcess::ExitStatus ) ) );
}

MainWindow::~MainWindow() {
    delete ui;
}

void MainWindow::on_pushButton_clicked() { /* 启动进程按钮 */
    QString program = "cmd.exe";
    QStringList arguments;
    arguments << "/c dir&pause";
    myProcess.start ( program, arguments );
}

void MainWindow::showResult() { /* 显示运行结果 */
    qDebug() << "showResult: " << endl << QString ( myProcess.readAll() );
}

void MainWindow::showState ( QProcess::ProcessState state ) { /* 显示状态变化 */
    qDebug() << "showState: ";

    if ( state == QProcess::NotRunning ) {
        qDebug() << "Not Running";
    } else if ( state == QProcess::Starting ) {
        qDebug() << "Starting";
    } else {
        qDebug() << "Running";
    }
}

void MainWindow::showError() { /* 显示错误 */
    qDebug() << "showError: " << endl << myProcess.errorString();
}

/* 显示结束信息 */
void MainWindow::showFinished ( int exitCode, QProcess::ExitStatus exitStatus ) {
    qDebug() << "showFinished: " << endl << exitCode << exitStatus;
}
```