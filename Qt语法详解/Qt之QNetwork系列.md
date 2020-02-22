---
title: Qt之QNetwork系列
categories: Qt语法详解
date: 2019-01-26 21:42:05
---
### QNetworkAccessManager

&emsp;&emsp;`QNetworkAccessManager`类允许应用程序发送网络请求和接收网络应答。`Network Access API`都是围绕着一个`QNetworkAccessManager`对象构造的，这个对象包含着发送请求的一些通用配置和设置。它包含着代理和缓存的配置，以及和这些事物相关的一些信号，并且应答信号可以作为我们检测一个网络操作的进度。一个`QNetworkAccessManager`对于一整个`Qt`应用程序来说已经足够了！<!--more-->
&emsp;&emsp;一旦一个`QNetworkAccessManager`对象被创建了，那么应用程序就可以使用它在网络上发送请求。它提供了一组标准的函数，可以承载网络请求和一些可选的数据，并且每一个请求返回一个`QNetworkReply`对象。该返回的对象包含着返回的请求应带的所有数据。
&emsp;&emsp;一个简单的从网络下载的例子可如下完成：

``` cpp
QNetworkAccessManager *manager = new QNetworkAccessManager ( this );
connect ( manager, SIGNAL ( finished ( QNetworkReply * ) ), \
          this, SLOT ( replyFinished ( QNetworkReply * ) ) );
manager->get ( QNetworkRequest ( QUrl ( "http://qt.nokia.com" ) ) );
```

`QNetworkAccessManager`有一个异步的`API`。当上面的`replyFinished`槽被调用的时候，它带的参数就是包含有下载的数据的`QNetworkReply`对象。注意，当请求完成的时候，程序员需要在适当的时候删除`QNetworkReply`对象。不要在连接到信号`finished`的槽函数中直接删除掉，你可以使用`deleteLater`函数。
&emsp;&emsp;注意，`QNetworkAccessManager`将会把它收到的请求排队，并行执行的请求数量是依赖于协议的。目前对于桌面平台的`HTTP`协议，对于一个`主机/端口`的组合，可并行执行`6`个请求。
&emsp;&emsp;一个更加复杂的例子如下所示，假设`manager`已经存在：

``` cpp
QNetworkRequest request;
request.setUrl ( QUrl ( "http://qt.nokia.com" ) );
request.setRawHeader ( "User-Agent", "MyOwnBrowser 1.0" );
QNetworkReply *reply = manager->get ( request );
connect ( reply, SIGNAL ( readyRead() ), this, SLOT ( slotReadyRead() ) );
connect ( reply, SIGNAL ( error ( QNetworkReply::NetworkError ) ), \
          this, SLOT ( slotError ( QNetworkReply::NetworkError ) ) );
connect ( reply, SIGNAL ( sslErrors ( QList<QSslError> ) ), \
          this, SLOT ( slotSslErrors ( QList<QSslError> ) ) );
```

#### 网络和漫游支持

&emsp;&emsp;在`Qt 4.7`版本中，`QNetworkAccessManager`有了额外的`Bearer Management API`支持，使得`QNetworkAccessManager`具有了管理管理网络连接的能力。`QNetworkAccessManager`可以在设备离线的时候启用网络接口，并且如果当前进程是最后一个使用网络时，`QNetworkAccessManager`可以停止网络接口。每一个入队/挂起的网络请求可以自动地传输到一个新的接入点。客户希望不作出任何改变就可以利用这个特性。实际上它就像把与特定平台相关的网络连接的代码从应用程序中删除。

#### 成员类型文档

- enum `QNetworkAccessManager::NetworkAccessibility`：表明是否可以通过网络管理接入网络。

Constant                                      | Value | Description
----------------------------------------------|-------|-----------------
`QNetworkAccessManager::UnknownAccessibility` | `-1`  | The network accessibility cannot be determined.
`QNetworkAccessManager::NotAccessible`        | `0`   | The network is not currently accessible, either because there is currently no network coverage or network access has been explicitly disabled by a call to `setNetworkAccessible()`.
`QNetworkAccessManager::Accessible`           | `1`   | The network is accessible.

- enum `QNetworkAccessManager::Operation`表明这个对于一个应答的处理过程。

Constant                                 | Value | Description
-----------------------------------------|-------|-------------
`QNetworkAccessManager::HeadOperation`   | `1`   | retrieve headers operation (created with `head()`)
`QNetworkAccessManager::GetOperation`    | `2`   | retrieve headers and download contents (created with `get()`)
`QNetworkAccessManager::PutOperation`    | `3`   | upload contents operation (created with `put()`)
`QNetworkAccessManager::PostOperation`   | `4`   | send the contents of an `HTML` form for processing via `HTTP` `POST` (created with `post()`)
`QNetworkAccessManager::DeleteOperation` | `5`   | delete contents operation (created with `deleteResource()`)
`QNetworkAccessManager::CustomOperation` | `6`   | custom operation (created with `sendCustomRequest()`)

#### 属性文档

&emsp;&emsp;`NetworkAccessibility`这个属性表明当前是否可以通过网络管理接入网络。如果网络不可接入，那么`network access manager`将不会处理任何新的网络请求，所有这些请求都会发生错误而失败。那些以`file://scheme`作为`URLs`的请求仍然会被处理。这个属性的默认值反应了设备的物理状态。应用程序可以通过如下操作来覆盖它的值以禁止任何网络请求：

``` cpp
networkAccessManager->setNetworkAccessible ( QNetworkAccessManager::NotAccessible );
```

可以通过如下调用来再次使能网络：

``` cpp
networkAccessManager->setNetworkAccessible ( QNetworkAccessManager::Accessible );
```

调用`setNetworkAccessible`并不会改变网络状态。
&emsp;&emsp;Access functions:

``` cpp
NetworkAccessibility networkAccessible () const
void setNetworkAccessible ( NetworkAccessibility accessible )
```

&emsp;&emsp;Notifier signal:

``` cpp
void networkAccessibleChanged ( QNetworkAccessManager::NetworkAccessibility accessible )
```

---

### QNetworkRequest

&emsp;&emsp;这个类是从`Qt 4.4`开始引入进来的。

Return              | Function
--------------------|---------
                    | `QNetworkRequest ( const QUrl &url = QUrl() )`
                    | `QNetworkRequest ( const QNetworkRequest &other )`
                    | `~QNetworkRequest ()`
`QVariant`          | `attribute ( Attribute code, const QVariant &defaultValue = QVariant() ) const`
`bool`              | `hasRawHeader ( const QByteArray &headerName ) const`
`QVariant`          | `header ( KnownHeaders header ) const`
`QObject *`         | `originatingObject () const`
`Priority`          | `priority () const`
`QByteArray`        | `rawHeader ( const QByteArray &headerName ) const`
`QList<QByteArray>` | `rawHeaderList () const`
`void`              | `setAttribute ( Attribute code, const QVariant &value )`
`void`              | `setHeader ( KnownHeaders header, const QVariant &value )`
`void`              | `setOriginatingObject ( QObject *object )`
`void`              | `setPriority ( Priority priority )`
`void`              | `setRawHeader ( const QByteArray &headerName, const QByteArray &headerValue )`
`void`              | `setSslConfiguration ( const QSslConfiguration &config )`
`void`              | `setUrl ( const QUrl &url )`
`QSslConfiguration` | `sslConfiguration () const`
`QUrl`              | `url () const`
`bool`              | `operator!= ( const QNetworkRequest &other ) const`
`QNetworkRequest &` | `operator= ( const QNetworkRequest &other )`
`bool`              | `operator== ( const QNetworkRequest &other ) const`

&emsp;&emsp;`QNetworkRequest`类包含一个和`QNetworkAccessManager`一起发送的请求。`QNetworkRequest`是`Network Access API`的一部分，并且这个类包含着在网络上发送请求的必要信息。它包含了一个`URL`和一些可以用来修改请求的附加信息。

#### 成员类型文档

- enum `QNetworkRequest::Attribute`：`QNetworkRequest`和`QNetworkReply`的属性编码。属性是额外的`meta`数据，可以用来控制请求的行为，并且可以通过应答传递更多的信息到应用程序中。属性都是可扩展的，允许自定义实现来传递自定义的值。下面的表格说明默认属性值，都是和`QVariant`类型相关，指明属性的默认值是否丢失，是否在请求和应答中使用。
- enum `QNetworkRequest::CacheLoadControl`：控制了`QNetworkAccessManager`的缓冲机制。
- enum `QNetworkRequest::KnownHeaders`：列出了`QNetworkRequest`解析的已知的首部。每一个已知的首部都用完整的`HTTP`名字以原始类型的形式呈现。
- enum `QNetworkRequest::LoadControl`：表明请求的缓存机制的一个方面是否被人为的覆盖了，例如被`QtWebKit`。
- enum `QNetworkRequest::Priority`：这个表枚举了可能的网络请求的优先级。

---

### QNetworkReply

&emsp;&emsp;这个类是从`Qt 4.4`引入的，其中的所有函数都是可重入的。
&emsp;&emsp;`QNetworkReply`类包含了发送给`QNetworkManager`的数据和首部。`QNetworkReply`类包含了发送给`QNetworkAccessManager`请求的所有应答数据。和`QNetworkRequest`类似，这些数据包含了一个`URL`和一些首部信息(同时包含解析后的和原始形式的)，以及一些和应答状态相关的信息，再加上应答信息自身的内容。
&emsp;&emsp;`QNetworkReply`是一个顺序访问的`QIODevice`，这也意味着一旦数据从该对象中读取出来，那么该对象就不再持有这些数据。因此当需要保存数据时，这个工作应该由应用程序完成。无论什么时候从网络中获得数据，`readyRead`信号都会被触发。`downloadProgress`信号在接收到数据时也会被发送，但是它所持有的数据量不一定就是真实接收到的数据量。`QNetworkReply`是一个与应答信息关联的`QIODevice`，它同样触发`uploadProgress`信号，这表明`upload`操作拥有这些数据。注意，不要在连接到`error`或者`finished`的槽函数里删除该对象，应该使用`deleteLater`。

#### 成员类型

- `enum QNetworkReply::NetworkError`：表明在处理请求的过程中所有可能的错误情况。
- `typedef QNetworkReply::RawHeaderPair`：`RawHeaderPair`是一个`QPair<QByteArray, QByteArray>`，第一个`QByteArray`代表头部的名字，第二个代表头部信息。
- `void QNetworkReply::finished () [signal]`：当应答信息被处理完毕时，这个信号就会被触发。当这个信号被触发后，就不会再对应答数据或者元数据进行更新。除非`close`被调用，否则应答信息会一直被打开等待读取，可以通过`read`或者`readAll`方法读取数据。特别地，在`readyRead`后如果没有调用`read`，那么调用`readAll`就会将所有的内容都存储在一个`QByteArray`中。这个信号和`QNetworkAccessManager::finished`是串联触发的。注意，不要在与这个信号关联的槽函数中直接删除掉`QNetworkReply`对象，应该使用`deleteLater`。你可以在收到`finished`信号之前使用`isFinished`函数检查一个`QNetworkReply`是否已经结束。

&emsp;&emsp;另外一些重要的函数就是对应答信息的读取函数了：

``` cpp
qint64 read ( char *, qint64 )
QByteArray read ( qint64 )
QByteArray readAll()
qint64 readBufferSize() const
void readChannelFinished()
qint64 readData ( char *, qint64 )
qint64 readLine ( char *, qint64 )
QByteArray readLine ( qint64 )
qint64 readLineData ( char *, qint64 )
void readyRead()
```

- `void QNetworkReply::downloadProgress ( qint64 bytesReceived, qint64 bytesTotal ) [signal]`：这个信号被触发，用来表明该网络请求的下载部分的进度。如果该网络请求没有相关联的下载部分，这个信号在参数`bytesReceived`和`bytesTotal`的值都为`0`时，会被触发一次。参数`bytesReceived`表明已经接收到的数据量，而`bytesTotal`则表明总共期望下载的数据量。如果期望下载的数据量未知，那么`bytesTotal`就为`-1`。当`bytesReceived`和`bytesTotal`相等时，就表明下载完毕，此时`bytesTotal`就不等于`-1`了。注意，`bytesReceived`和`bytesTotal`的值也许都和`size`不同，它是通过`read`或者`readAll`获得的总的数据量，或者表明数据量的头部的值`ContentLengthHeader`。造成这种情况的原因是：协议头部或者是数据在下载的过程总可能被压缩。
- `void QNetworkReply::uploadProgress ( qint64 bytesSent, qint64 bytesTotal ) [signal]`：该信号表示的是网络请求中上传的部分，其它都和上面的`downloadProgress`类似。