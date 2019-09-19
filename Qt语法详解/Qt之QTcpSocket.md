---
title: Qt之QTcpSocket
date: 2019-01-02 15:33:43
categories: Qt语法详解
---
&emsp;&emsp;The `QTcpSocket` class provides a TCP socket.

Header       | Inherits          | Inherited By
-------------|-------------------|-------------
`QTcpSocket` | `QAbstractSocket` | `QSslSocket`

**Note**: All functions in this class are reentrant.

### Public Functions

- `QTcpSocket(QObject * parent = 0)`
- `virtual ~QTcpSocket()`

### Detailed Description

&emsp;&emsp;The `QTcpSocket` class provides a `TCP` socket.
&emsp;&emsp;`TCP` (`Transmission Control Protocol`) is a `reliable`, `stream-oriented`, `connection-oriented` transport protocol. It is especially well suited for continuous transmission of data.
&emsp;&emsp;`QTcpSocket` is a convenience subclass of `QAbstractSocket` that allows you to establish a TCP connection and transfer streams of data.
&emsp;&emsp;**Note**: `TCP` sockets cannot be opened in `QIODevice::Unbuffered` mode.

### Symbian Platform Security Requirements

&emsp;&emsp;On `Symbian`, processes which use this class must have the `NetworkServices` platform security capability. If the client process lacks this capability, it will result in a panic.
&emsp;&emsp;Platform security capabilities are added via the `TARGET.CAPABILITY` qmake variable.

### Member Function Documentation

- `QTcpSocket::QTcpSocket(QObject * parent = 0)`: Creates a `QTcpSocket` object in state UnconnectedState. `parent` is passed on to the `QObject` constructor.
- `QTcpSocket::~QTcpSocket() [virtual]`: Destroys the socket, closing the connection if necessary.