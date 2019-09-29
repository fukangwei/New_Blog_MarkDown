---
title: Qt之QFtp
categories: Qt语法详解
abbrlink: bbd6cbb0
date: 2019-01-27 22:10:59
---
&emsp;&emsp;`QFtp`类提供了一个`FTP`协议的客户端实现，该类提供了一个访问到`FTP`服务器的接口。对于新的应用程序，建议使用`QNetworkAccessManager`和`QNetworkReply`，因为这些类拥有一个更简单、更强大的`API`。
&emsp;&emsp;`QFtp`支持异步工作，因此没有阻塞函数。如果无法立即执行操作，函数仍将立即返回，并且该操作将被操作系统调度，供以后执行。调度操作的结果通过信号报告，这种方法依赖于事件循环操作。可以调度的操作(也被称为`命令`)有`connectToHost`、`login`、`close`、`list`、`cd`、`get`、`put`、`remove`、`mkdir`、`rmdir`、`rename`和`rawCommand`。所有这些命令都会返回一个唯一的标识符，允许程序员跟踪当前正在执行的命令。当命令的执行开始时，发出带有命令标识符的`commandStarted`信号。当命令完成时，会发出`commandFinished`信号，并带有命令标识符和一个`bool`参数，表明该命令在完成时是否出错。
&emsp;&emsp;在某些情况下，可能想要执行一系列命令。例如连接并登录到`FTP`服务器，简单的实现如下：

``` cpp
QFtp *ftp = new QFtp ( parent );
ftp->connectToHost ( "192.168.***.***", 21 );
ftp->login ( "wang", "123456" );
```

在这种情况下，调度了两个`FTP`命令。当最后一个调度命令完成时，会发出`done`信号，并带有一个`bool`参数，告诉你命令序列在完成时是否出错。
&emsp;&emsp;如果命令序列中的某个命令的执行期间发生错误，则所有挂起的命令(即已调度，但尚未执行的命令)会被清除，并且不为它们发射信号。一些命令(例如`list`)会发出额外的信号(`listInfo`)以报告其结果。
&emsp;&emsp;对于文件传输，`QFtp`可以使用主动或被动模式，并且默认使用被动文件传输模式，可使用`setTransferMode`设置。函数`hasPendingCommands`和`clearPendingCommands`允许查询和清除挂起的命令列表。如果你在网络编程方面比较有经验，可以使用`rawCommand`来执行任意的`FTP`命令。注意，当前版本的`QFtp`不完全支持非`Unix`的`FTP`服务器。
&emsp;&emsp;如果要从`FTP`服务器下载`/home/wang/ftp.qdoc`文件，可以分为下面几步：

``` cpp
ftp->connectToHost ( "192.168.***.***", 21 );
ftp->login ( "wang", "123456" );
ftp->cd ( "/home/wang" );
ftp->get ( "ftp.qdoc" );
ftp->close();
```

流程如下：

- `connectToHost`：指定主机和端口号，连接`FTP`服务器。
- `login`：指定用户名和密码，登录到`FTP`服务器。
- `cd`：改变服务器的当前工作目录。
- `get`：从服务器上下载文件`ftp.qdoc`(绝对路径为`/home/wang/ftp.qdoc`)。
- `close`：关闭到`FTP`服务器的连接。

&emsp;&emsp;对于该示例，发射以下序列的信号：

``` cpp
commandStarted ( 1 )
stateChanged ( HostLookup )
stateChanged ( Connecting )
stateChanged ( Connected )
commandFinished ( 1, false )
​
commandStarted ( 2 )
stateChanged ( LoggedIn )
commandFinished ( 2, false )
​
commandStarted ( 3 )
commandFinished ( 3, false )
​
commandStarted ( 4 )
dataTransferProgress ( 0, 8710 )
dataTransferProgress ( 8192, 8710 )
readyRead()
dataTransferProgress ( 8710, 8710 )
readyRead()
commandFinished ( 4, false )
​
commandStarted ( 5 )
stateChanged ( Closing )
stateChanged ( Unconnected )
commandFinished ( 5, false )
​
done ( false )
```

如果要显示进度条以通知用户下载进度，上述示例中的`dataTransferProgress`信号就会很有用。`readyRead`信号告诉你有数据准备好被读取，然后可以使用`bytesAvailable`函数查询数据量，并且可以使用`read`或`readAll`函数读取数据量。
&emsp;&emsp;如果上述示例登录失败(例如用户名或密码错误)，信号的流程显示如下：

``` cpp
commandStarted ( 1 )
stateChanged ( HostLookup )
stateChanged ( Connecting )
stateChanged ( Connected )
commandFinished ( 1, false )
​
commandStarted ( 2 )
commandFinished ( 2, true )
​
done ( true )
```

然后可以使用`error`和`errorString`函数获取有关错误的详细信息。
&emsp;&emsp;在进行其他命令操作之前，先一起看看`doc`的树结构：

``` bash
$ pwd
/home/wang/doc
$ tree
.
├── c++
│   └── qt5_cadaques.pdf
├── hello.sh
├── linux
│   └── linux-program.pdf
└── python
    └── hello.py
​
3 directories, 4 files
```

里面包含`3`个目录以及`4`个文件。
&emsp;&emsp;要列出`dir`目录的内容，可以使用`list`。如果`dir`为空，将列出当前目录的内容。

``` cpp
int QFtp::list ( const QString &dir = QString() )
```

对于找到的每个目录条目，都会发出`listInfo`信号。输出文件详细信息的代码如下：

``` cpp
connect ( ftp, &QFtp::listInfo, [ = ] ( const QUrlInfo &urlInfo ) {
    qDebug() << urlInfo.name() << urlInfo.size() << urlInfo.owner() << urlInfo.group() \
             << urlInfo.lastModified().toString ( "MMM dd yyyy" ) << urlInfo.isDir();
} );

ftp->list();
```

这里只列出文件的一部分信息，其他更多信息请参考`QUrlInfo`。输出如下：

``` cpp
"c++"      29 "1000" "1000" "十一月 28 2016" true
"hello.sh" 55 "1000" "1000" "十月 20 2016"   false
"Linux"    30 "1000" "1000" "十一月 28 2016" true
"Python"   21 "1000" "1000" "十一月 28 2016" true
```

可以和服务端比对一下：

``` bash
$ ls -l
总用量 4
drwxrwxr-x. 2 wang wang 29 11月 28 10:41 c++
-rw-rw-r--. 1 wang wang 55 10月 20 15:59 hello.sh
drwxrwxr-x. 2 wang wang 30 11月 28 10:40 linux
drwxrwxr-x. 2 wang wang 21 11月 28 10:39 python
```

要在服务器上创建一个名为`dir`的目录，使用`mkdir`：

``` cpp
ftp->mkdir ( "new_dir" );
```

`remove`是删除文件，`rmdir`则是删除目录。要从服务器中删除文件，使用`remove`：

``` cpp
ftp->remove( "hello.sh" );
```

要从服务器中删除目录，使用`rmdir`：

``` cpp
ftp->rmdir ( "new_dir" ); /* 删除空目录 */
```

注意只能删除空目录，如果目录下有文件，则不能删除。
&emsp;&emsp;如果要对文件进行重命名，使用`rename`：

``` cpp
ftp->rename("c++", "c"); /* c++ -> c */
```

&emsp;&emsp;关于上传文件，有两个重载的函数：

``` cpp
int QFtp::put ( QIODevice *dev, const QString &file, TransferType type = Binary );
```

该函数从`IO`设备`dev`读取数据，并将其写入服务器上名为`file`的文件。从`IO`设备读取数据块，因此此重载允许传输大量数据，而无需立即将所有数据读入内存。注意确保`dev`指针在操作期间有效(在发出`commandFinished`时可以安全地删除它)。

``` cpp
m_file = new QFile ( "E:/Qt.zip" );
ftp->put ( m_file, "Qt.zip" );
```

`put`函数原型如下：

``` cpp
int QFtp::put ( const QByteArray &data, const QString &file, TransferType type = Binary );
```

该函数将给定数据的副本写入服务器上名为`file`的文件。

``` cpp
ftp->put ( "Hello World!\nI'am a Qter.", "readMe.txt" );
```

上传完成后，去服务端查看：

``` bash
$ ls
c  linux  python  readMe.txt
$ cat readMe.txt
Hello World!
I'am a Qter.
```

如果要获取上传的进度，可以关联`dataTransferProgress`信号。
&emsp;&emsp;要从服务器下载文件，使用`get`函数：

``` cpp
int QFtp::get ( const QString &file, QIODevice *dev = 0, TransferType type = Binary );
```

如果`dev`为`0`，则当有可用的数据可读时，发出`readyRead`信号，然后可以使用`read`或`readAll`函数读取数据；如果`dev`不为`0`，则将数据直接写入设备`dev`。
&emsp;&emsp;如果想要在有可用的数据时向用户提供数据，请连接到`readyRead`信号并立即读取数据；如果只想使用完整的数据，则可以连接到`commandFinished`信号，并在`get`命令完成后读取数据：

``` cpp
m_file = new QFile ( "E:/Qt.zip" );
​
if ( !m_file->open ( QIODevice::WriteOnly ) ) {
    m_file->remove();
    delete m_file;
    m_file = NULL;
} else {
    ftp->get ( "Qt.zip", m_file ); /* 下载文件 */
}
```

&emsp;&emsp;当前的状态`QFtp::State`由`state`返回，当状态改变时，发出`stateChanged`信号，参数是连接的新状态。该信号通常用于`connectToHost`或者`close`命令，也可以`自发地`发射，例如当服务器意外关闭连接时。

常量                | 值  | 描述
--------------------|-----|----
`QFtp::Unconnected` | `0` | 没有连接到主机
`QFtp::HostLookup`  | `1` | 正在进行主机名查找
`QFtp::Connecting`  | `2` | 正在尝试连接到主机
`QFtp::Connected`   | `3` | 已实现与主机的连接
`QFtp::LoggedIn`    | `4` | 已实现连接和用户登录
`QFtp::Closing`     | `5` | 连接正在关闭，但尚未关闭(当连接关闭时，状态将为`Unconnected`)

&emsp;&emsp;当连接`TCP`服务器的时候，使用一个`QLabel`显示连接的状态信息：

``` cpp
void FtpWindow::stateChanged ( int state ) {
    switch ( state ) {
        case QFtp::Unconnected:
            stateLabel->setText ( QStringLiteral ( "没有连接到主机" ) );
            break;
        case QFtp::HostLookup:
            stateLabel->setText ( QStringLiteral ( "正在进行主机名查找" ) );
            break;
        case QFtp::Connecting:
            stateLabel->setText ( QStringLiteral ( "正在尝试连接到主机" ) );
            break;
        case QFtp::Connected:
            stateLabel->setText ( QStringLiteral ( "已实现与主机的连接" ) );
            break;
        case QFtp::LoggedIn:
            stateLabel->setText ( QStringLiteral ( "已实现连接和用户登录" ) );
            break;​
        case QFtp::Closing:
            stateLabel->setText ( QStringLiteral ( "连接正在关闭•" ) );
            break;
        default:
            break;
    }
}
```

&emsp;&emsp;`currentId`和`currentCommand`提供了有关当前执行命令的信息。`currentCommand`返回当前`FTP`的命令类型`QFtp::Command`，如果没有命令正在执行，则返回`None`。

常量                    | 值   | 描述
------------------------|------|-----
`QFtp::None`            | `0`  | 未执行任何命令
`QFtp::SetTransferMode` | `1`  | 设置传输模式
`QFtp::SetProxy`        | `2`  | 切换代理打开或关闭
`QFtp::ConnectToHost`   | `3`  | 正在执行`connectToHost`
`QFtp::Login`           | `4`  | 正在执行`login`
`QFtp::Close`           | `5`  | 正在执行`close`
`QFtp::List`            | `6`  | 正在执行`list`
`QFtp::Cd`              | `7`  | 正在执行`cd`
`QFtp::Get`             | `8`  | 正在执行`get`
`QFtp::Put`             | `9`  | 正在执行`put`
`QFtp::Remove`          | `10` | 正在执行`remove`
`QFtp::Mkdir`           | `11` | 正在执行`mkdir`
`QFtp::Rmdir`           | `12` | 正在执行`rmdir`
`QFtp::Rename`          | `13` | 正在执行`rename`
`QFtp::RawCommand`      | `14` | 正在执行`rawCommand`

这允许你对特定命令执行特定操作，例如在FTP客户端中，可能需要在启动`list`命令时清除目录视图。在这种情况下，可以简单地检查连接到`commandStarted`信号的槽函数中的`currentCommand`是否为`List`。

``` cpp
void FtpWindow::commandStarted ( int id ) {
    QFtp::Command command = ftp->currentCommand();
​
    switch ( command ) {
        case QFtp::List: /* 正在执行list：列出目录下的文件 */
            fileListTree->clear(); /* 清除目录视图QTreeWidget */
            break;
        default:
            break;
    }
​
    qDebug() << "commandStarted " << id;
}
```

&emsp;&emsp;通过`error`和`errorString`返回最后一次发生的错误。当接收到`commandFinished`或者`done`信号时，如果标识`error`的`bool`参数为`true`，这就非常有用了。`error`返回的是一个`QFtp::Error`枚举类型，用来标识发生的错误：

常量                      | 值  | 描述
--------------------------|-----|----
`QFtp::NoError`           | `0` | 没有发生错误
`QFtp::HostNotFound`      | `2` | 主机名查找失败
`QFtp::ConnectionRefused` | `3` | 服务器拒绝连接
`QFtp::NotConnected`      | `4` | 尝试发送命令，但没有到服务器的连接
`QFtp::UnknownError`      | `1` | 除了以上指定的错误发生

注意，如果启动一个新命令，错误的状态会被重置为`NoError`。
&emsp;&emsp;`errorString`返回的是一个人类可读的字符串。通常是(但不总是)来自服务器的回复，因此并不总是可以翻译成字符串。如果消息来自`Qt`，则字符串已经通过`tr`函数的处理。

``` CPP
void FtpWindow::commandFinished ( int id, bool error ) {
    Q_UNUSED ( id );
    QFtp::Command command = ftp->currentCommand();
​
    switch ( command ) {
        case QFtp::ConnectToHost: /* 连接FTP服务器 */
            if ( error ) { /* 发生错误 */
                qDebug() << "Error " << ftp->error() << "ErrorString " << ftp->errorString();
                QMessageBox::information (
                    this, "FTP", QStringLiteral ( "无法连接到FTP服务器，请检查主机名是否正确！" ) );
                ftp->abort();
                ftp->deleteLater();
                ftp = NULL;
            } else {
                qDebug() << QStringLiteral ( "登录FTP服务器" );
            }
​
            break;
        default:
            break;
    }
}
```

&emsp;&emsp;设置文件传输模式是枚举变量`QFtp::TransferMode`。`FTP`使用两个套接字连接：一个用于命令，另一个用于发送数据。虽然命令连接始终由客户端发起，但第二个连接可以由客户端或服务器发起。此枚举定义客户端(被动模式)还是服务器(活动模式)应设置数据连接。

常量            | 值  | 描述
----------------|-----|-----
`QFtp::Passive` | `1` | 客户端连接到服务器以传输其数据
`QFtp::Active`  | `0` | 服务器连接到客户端以传输其数据

&emsp;&emsp;设置数据传输类型是使用枚举变量`QFtp::TransferType`，此枚举标识使用`get`和`put`命令进行数据传输的类型。

常量           | 值  | 描述
---------------|-----|-----
`QFtp::Binary` | `0` | 数据将以二进制模式传输
`QFtp::Ascii`  | `1` | 数据将以`ASCII`模式传输，换行符将转换为本地格式