---
title: Qt之QHostInfo
categories: Qt语法详解
date: 2019-01-03 18:40:10
---
&emsp;&emsp;`QHostInfo`类为主机名查找提供了静态函数。`QHostInfo`利用操作系统提供的查询机制来查询与特定主机名相关联的主机的`IP`地址，或者与一个`IP`地址相关联的主机名。这个类提供了两个静态的函数：一个以异步方式工作，一旦找到主机就发射一个信号；另一个以阻塞方式工作，并且最终返回一个`QHostInfo`对象。<!--more-->
&emsp;&emsp;要使用异步方式查询主机的`IP`地址，调用`lookupHost`即可。该函数包含`3`个参数，依次是主机名或`IP`地址、接收的对象、接收的槽函数，并返回一个查询`ID`。以查询`ID`为参数，通过调用`abortHostLookup`函数的来中止查询。当获得查询结果后就会调用槽函数，查询结果被存储到`QHostInfo`对象中。可通过调用`addresses`函数来获得主机的`IP`地址列表，同时可通过调用`hostName`函数来获得查询的主机名。

### 查询本机主机名

&emsp;&emsp;代码如下：

``` cpp
QString strLocalHostName = QHostInfo::localHostName();
qDebug() << "Local Host Name:" << strLocalHostName;
```

执行结果：

``` cpp
Local Host Name:"Wang-PC"
```

### 查询主机信息

&emsp;&emsp;1. 异步方式。使用`lookupHost`，实际的查询在一个单独的线程中完成，利用操作系统的方法来执行名称查找。

- 根据主机名查询主机信息：

``` cpp
int nID = QHostInfo::lookupHost ( "qt-project.org", this, SLOT ( lookedUp ( QHostInfo ) ) );

void MainWindow::lookedUp ( const QHostInfo &host ) {
    if ( host.error() != QHostInfo::NoError ) {
        qDebug() << "Lookup failed:" << host.errorString();
        return;
    }

    foreach ( const QHostAddress &address, host.addresses() ) {
        /* 输出IPV4、IPv6地址 */
        if ( address.protocol() == QAbstractSocket::IPv4Protocol ) {
            qDebug() << "Found IPv4 address:" << address.toString();
        } else if ( address.protocol() == QAbstractSocket::IPv6Protocol ) {
            qDebug() << "Found IPv6 address:" << address.toString();
        } else {
            qDebug() << "Found other address:" << address.toString();
        }
    }
}
```

执行结果：

``` cpp
Found IPv4 address: "5.254.113.102"
Found IPv4 address: "178.32.152.214"
```

- 根据`IP`地址查询主机信息：

``` cpp
int nID = QHostInfo::lookupHost ( "5.254.113.102", this, SLOT ( lookedUp ( QHostInfo ) ) );

void MainWindow::lookedUp ( const QHostInfo &host ) {
    if ( host.error() != QHostInfo::NoError ) {
        qDebug() << "Lookup failed:" << host.errorString();
        return;
    }

    qDebug() << "Found hostName:" << host.hostName();
}
```

执行结果：

``` cpp
Found hostName: "webredirects.cloudns.NET"
```

&emsp;&emsp;2. 阻塞方式。如果要使用阻塞查找，则使用`QHostInfo::fromName`函数：

``` cpp
QHostInfo host = QHostInfo::fromName ( "5.254.113.102" );

if ( host.error() != QHostInfo::NoError ) {
    qDebug() << "Lookup failed:" << host.errorString();
    return;
}

qDebug() << "Found hostName:" << host.hostName();
```

这种情况下，名称查询的执行与调用者处于相同的线程中。这对于非`GUI`应用程序或在一个单独的、非`GUI`线程中做名称查找是比较有用的(在`GUI`线程中调用这个函数可能会导致用户界面冻结)。

### 中止查询

&emsp;&emsp;`lookupHost`查询主机信息时，会返回一个查询`ID`。以此`ID`为参数，通过调用`abortHostLookup`来中止查询：

``` cpp
QHostInfo::abortHostLookup ( nId );
```

### 错误处理

&emsp;&emsp;如果查询失败，`error`返回发生错误的类型，`errorString`返回一个能够读懂的查询错误描述。枚举变量`QHostInfo::HostInfoError`如下：

常量                      | 值   | 描述
--------------------------|-----|------
`QHostInfo::NoError`      | `0` | 查找成功
`QHostInfo::HostNotFound` | `1` | 没有发现主机对应的`IP`地址
`QHostInfo::UnknownError` | `2` | 未知错误