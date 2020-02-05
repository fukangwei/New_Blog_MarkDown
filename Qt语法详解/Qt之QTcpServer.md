---
title: Qt之QTcpServer
categories: Qt语法详解
abbrlink: e7b2bf0e
date: 2019-01-23 13:09:25
---
&emsp;&emsp;The `QTcpServer` class provides a `TCP-based` server.<!--more-->

Header       | Inherits
-------------|----------
`QTcpServer` | `QObject`

**Note**: All functions in this class are reentrant.

### Public Functions

Return                         | Function
-------------------------------|---------
                               | `QTcpServer(QObject * parent = 0)`
`virtual`                      | `~QTcpServer()`
`void`                         | `close()`
`QString`                      | `errorString() const`
`virtual bool`                 | `hasPendingConnections() const`
`bool`                         | `isListening() const`
`bool`                         | `listen(const QHostAddress & address = QHostAddress::Any, quint16 port = 0)`
`int`                          | `maxPendingConnections() const`
`virtual QTcpSocket *`         | `nextPendingConnection()`
`QNetworkProxy`                | `proxy() const`
`QHostAddress`                 | `serverAddress() const`
`QAbstractSocket::SocketError` | `serverError() const`
`quint16`                      | `serverPort() const`
`void`                         | `setMaxPendingConnections(int numConnections)`
`void`                         | `setProxy(const QNetworkProxy & networkProxy)`
`bool`                         | `setSocketDescriptor(int socketDescriptor)`
`int`                          | `socketDescriptor() const`
`bool`                         | `waitForNewConnection(int msec = 0, bool * timedOut = 0)`

### Signals

- `void newConnection()`

### Protected Functions

- `void addPendingConnection(QTcpSocket * socket)`
- `virtual void incomingConnection(int socketDescriptor)`

### Detailed Description

&emsp;&emsp;The `QTcpServer` class provides a `TCP-based` server.
&emsp;&emsp;This class makes it possible to accept incoming `TCP` connections. You can specify the port or have `QTcpServer` pick one automatically. You can listen on a specific address or on all the machine's addresses.
&emsp;&emsp;Call `listen()` to have the server listen for incoming connections. The `newConnection()` signal is then emitted each time a client connects to the server.
&emsp;&emsp;Call `nextPendingConnection()` to accept the pending connection as a connected `QTcpSocket`. The function returns a pointer to a `QTcpSocket` in `QAbstractSocket::ConnectedState` that you can use for communicating with the client.
&emsp;&emsp;If an error occurs, `serverError()` returns the type of error, and `errorString()` can be called to get a human readable description of what happened.
&emsp;&emsp;When listening for connections, the address and port on which the server is listening are available as `serverAddress()` and `serverPort()`.
&emsp;&emsp;Calling `close()` makes `QTcpServer` stop listening for incoming connections.
&emsp;&emsp;Although `QTcpServer` is mostly designed for use with an event loop, it's possible to use it without one. In that case, you must use `waitForNewConnection()`, which blocks until either a connection is available or a timeout expires.

### Member Function Documentation

- `QTcpServer::QTcpServer(QObject * parent = 0)`: Constructs a `QTcpServer` object. `parent` is passed to the `QObject` constructor.
- `QTcpServer::~QTcpServer() [virtual]`: Destroys the `QTcpServer` object. If the server is listening for connections, the socket is automatically closed. Any client `QTcpSockets` that are still connected must either disconnect or be reparented before the server is deleted.
- `void QTcpServer::addPendingConnection(QTcpSocket * socket) [protected]`: This function is called by `QTcpServer::incomingConnection()` to add the `socket` to the list of pending incoming connections. **Note**: Don't forget to call this member from reimplemented `incomingConnection()` if you do not want to break the Pending Connections mechanism.
- `void QTcpServer::close()`: Closes the server. The server will no longer listen for incoming connections.
- `QString QTcpServer::errorString() const`: Returns a human readable description of the last error that occurred.
- `bool QTcpServer::hasPendingConnections() const [virtual]`: Returns `true` if the server has a pending connection; otherwise returns `false`.
- `void QTcpServer::incomingConnection(int socketDescriptor) [virtual protected]`: This virtual function is called by `QTcpServer` when a new connection is available. The `socketDescriptor` argument is the native socket descriptor for the accepted connection. The base implementation creates a `QTcpSocket`, sets the socket descriptor and then stores the `QTcpSocket` in an internal list of pending connections. Finally `newConnection()` is emitted. Reimplement this function to alter the server's behavior when a connection is available. If this server is using `QNetworkProxy` then the `socketDescriptor` may not be usable with native socket functions, and should only be used with `QTcpSocket::setSocketDescriptor()`. **Note**: If you want to handle an incoming connection as a new `QTcpSocket` object in another thread you have to pass the `socketDescriptor` to the other thread and create the `QTcpSocket` object there and use its `setSocketDescriptor()` method.
- `bool QTcpServer::isListening() const`: Returns `true` if the server is currently listening for incoming connections; otherwise returns `false`.
- `bool QTcpServer::listen(const QHostAddress & address = QHostAddress::Any, quint16 port = 0)`: Tells the server to listen for incoming connections on `address` and `port`. If `port` is `0`, a port is chosen automatically. If `address` is `QHostAddress::Any`, the server will listen on all network interfaces. Returns `true` on success; otherwise returns `false`.
- `int QTcpServer::maxPendingConnections() const`: Returns the maximum number of pending accepted connections. The default is `30`.
- `void QTcpServer::newConnection() [signal]`: This signal is emitted every time a new connection is available.
- `QTcpSocket * QTcpServer::nextPendingConnection() [virtual]`: Returns the next pending connection as a connected `QTcpSocket` object. The socket is created as a child of the server, which means that it is automatically deleted when the `QTcpServer` object is destroyed. It is still a good idea to delete the object explicitly when you are done with it, to avoid wasting memory. `0` is returned if this function is called when there are no pending connections. **Note**: The returned `QTcpSocket` object cannot be used from another thread. If you want to use an incoming connection from another thread, you need to override `incomingConnection()`.
- `QNetworkProxy QTcpServer::proxy() const`: Returns the network proxy for this socket. By default `QNetworkProxy::DefaultProxy` is used.
- `QHostAddress QTcpServer::serverAddress() const`: Returns the server's address if the server is listening for connections; otherwise returns `QHostAddress::Null`.
- `QAbstractSocket::SocketError QTcpServer::serverError() const`: Returns an error code for the last error that occurred.
- `quint16 QTcpServer::serverPort() const`: Returns the server's port if the server is listening for connections; otherwise returns `0`.
- `void QTcpServer::setMaxPendingConnections(int numConnections)`: Sets the maximum number of pending accepted connections to `numConnections`. `QTcpServer` will accept no more than `numConnections` incoming connections before `nextPendingConnection()` is called. By default, the limit is `30` pending connections. Clients may still able to connect after the server has reached its maximum number of pending connections (i.e., `QTcpSocket` can still emit the `connected()` signal). `QTcpServer` will stop accepting the new connections, but the operating system may still keep them in queue.
- `void QTcpServer::setProxy(const QNetworkProxy & networkProxy)`: Sets the explicit network proxy for this socket to `networkProxy`. To disable the use of a proxy for this socket, use the `QNetworkProxy::NoProxy` proxy type:

``` cpp
server->setProxy ( QNetworkProxy::NoProxy );
```

- `bool QTcpServer::setSocketDescriptor(int socketDescriptor)`: Sets the socket descriptor this server should use when listening for incoming connections to `socketDescriptor`. Returns `true` if the socket is set successfully; otherwise returns `false`. The socket is assumed to be in listening state.
- `int QTcpServer::socketDescriptor() const`: Returns the native socket descriptor the server uses to listen for incoming instructions, or `-1` if the server is not listening. If the server is using `QNetworkProxy`, the returned descriptor may not be usable with native socket functions.
- `bool QTcpServer::waitForNewConnection(int msec = 0, bool * timedOut = 0)`: Waits for at most `msec` milliseconds or until an incoming connection is available. Returns `true` if a connection is available; otherwise returns `false`. If the operation timed out and `timedOut` is not `0`, `*timedOut` will be set to `true`. This is a blocking function call. Its use is disadvised in a `single-threaded` `GUI` application, since the whole application will stop responding until the function returns. `waitForNewConnection()` is mostly useful when there is no event loop available. The `non-blocking` alternative is to connect to the `newConnection()` signal. If `msec` is `-1`, this function will not time out.