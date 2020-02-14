---
title: Qt之获取网络接口信息
categories: Qt应用示例
abbrlink: a0a82ff
date: 2018-12-28 16:13:21
---
&emsp;&emsp;`mainwindow.h`如下：<!--more-->

``` cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H
​
#include <QMainWindow>
class QHostInfo;
​
namespace Ui {
    class MainWindow;
}
​
class MainWindow : public QMainWindow {
    Q_OBJECT
public:
    explicit MainWindow ( QWidget *parent = 0 );
    ~MainWindow();
private:
    Ui::MainWindow *ui;
private slots:
    void lookedUp ( const QHostInfo &host );
};
​
#endif // MAINWINDOW_H
```

&emsp;&emsp;`mainwindow.cpp`如下：

``` cpp
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QtNetwork>
#include <QDebug>
​
MainWindow::MainWindow ( QWidget *parent ) : QMainWindow ( parent ), ui ( new Ui::MainWindow ) {
    ui->setupUi ( this );
    QString localHostName = QHostInfo::localHostName();
    QHostInfo info = QHostInfo::fromName ( localHostName );
    qDebug() << "localHostName: " << localHostName << endl
             << "IP Address: " << info.addresses();
​
    foreach ( QHostAddress address, info.addresses() ) {
        if ( address.protocol() == QAbstractSocket::IPv4Protocol ) {
            qDebug() << address.toString();
        }
    }
​
    QHostInfo::lookupHost ( "www.baidu.com", this, SLOT ( lookedUp ( QHostInfo ) ) );
    QList<QNetworkInterface> list = QNetworkInterface::allInterfaces(); /* 获取所有网络接口的列表 */
​
    foreach ( QNetworkInterface interface, list ) { /* 遍历每一个网络接口 */
        qDebug() << "Name: " << interface.name(); /* 接口名称 */
        qDebug() << "HardwareAddress: " << interface.hardwareAddress(); /* 硬件地址 */
        /* 获取IP地址条目列表，每个条目中包含一个IP地址，一个子网掩码和一个广播地址 */
        QList<QNetworkAddressEntry> entryList = interface.addressEntries();
​
        foreach ( QNetworkAddressEntry entry, entryList ) { /* 遍历每一个IP地址条目 */
            qDebug() << "IP Address: " << entry.ip().toString(); /* IP地址 */
            qDebug() << "Netmask: " << entry.netmask().toString(); /* 子网掩码 */
            qDebug() << "Broadcast: " << entry.broadcast().toString(); /* 广播地址 */
        }
    }
}
​
MainWindow::~MainWindow() {
    delete ui;
}
​
void MainWindow::lookedUp ( const QHostInfo &host ) {
    if ( host.error() != QHostInfo::NoError ) {
        qDebug() << "Lookup failed:" << host.errorString();
        return;
    }
​
    foreach ( const QHostAddress &address, host.addresses() ) {
        qDebug() << "Found address:" << address.toString();
    }
}
```