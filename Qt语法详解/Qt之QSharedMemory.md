---
title: Qt之QSharedMemory
categories: Qt语法详解
date: 2019-01-03 12:28:04
---
&emsp;&emsp;`Qt`提供了`QSharedMemory`类来访问共享内存，实现共享内存的操作。<!--more-->

### 创建QSharedMemory类对象

&emsp;&emsp;利用`QSharedMemory`类创建实例对象时，必须为该共享内存指定关键字(即为该共享内存起一个名字)。只有当共享内存被设置了关键字之后，才可以执行创建(`create`)、关联(`attach`)等操作。为共享内存指定关键字有两种方法：

- 通过构造函数`QSharedMemory::QSharedMemory ( const QString & key, QObject * parent =0 )`为实例对象传入关键字：

``` cpp
SharedMemory *sharememory;
sharememory = newQSharedMemory ( "QSharedMemoryExample" );
```

- 通过构造函数`QSharedMemory::QSharedMemory (QObject * parent = 0 )`构造实例对象，之后调用`setKey`函数为该实例对象设置关键字：

``` cpp
QSharedMemory *sharememory;
sharememory = new QSharedMemory();
sharememory->setKey ( "QSharedMemoryExample" );
```

### 创建共享内存

&emsp;&emsp;如下代码为`QSharedMemory`类实例对象创建一个空间大小为`size`的共享内存，该内存空间默认的访问方式为可读可写。共享内存创建成功返回`true`，否则返回`false`：

``` cpp
bool QSharedMemory::create ( int size, AccessMode mode = ReadWrite );
```

`QSharedMemory`类定义一个枚举类变量`AccessMode`，指定了两种共享内存的访问方式：

``` cpp
QSharedMemory::ReadOnly /* 只读方式访问共享内存 */
QSharedMemory::ReadWrite /* 读写方式访问共享内存 */
```

### 关联共享内存

&emsp;&emsp;如下代码将以关键字`key`命名的共享内存和当前程序进行关联，共享内存默认的访问方式为可读可写。如果程序和共享内存关联成功，返回`true`，否则返回`false`：

``` cpp
bool QSharedMemory::attach ( AccessMode mode = ReadWrite );
```

### 分离共享内存

&emsp;&emsp;如下代码解除共享内存和程序的关联，即调用该函数后，程序不可以再访问共享内存：

``` cpp
bool QSharedMemory::detach ();
```

如果该共享内存被多个程序实例所关联，当最后一个程序实例和共享内存解除关联后，该共享内存将由操作系统自动释放掉。分离操作成功，则返回`true`；如果返回`false`，通常意味着该共享内存和程序分离失败，可能其他程序当前正在访问该共享内存。

### 判断共享内存的关联状态

&emsp;&emsp;函数`isAttached`用来判断程序(调用该函数的程序)是否和共享内存进行关联，如果是，就返回`true`，否则返回`false`：

``` cpp
bool QSharedMemory::isAttached () const;
```

### 设置/获取共享内存的关键字

&emsp;&emsp;`Qt`应用程序通过关键字来辨识共享内存。`key`函数用来获取共享内存的关键字，如果没有指定实例对象的关键字，或者共享内存的关键字是由`nativeKey`函数指定的话，则返回`NULL`：

``` cpp
QString QSharedMemory::key () const; /* 获取共享内存关键字 */
```

&emsp;&emsp;`setKey`函数用来为共享内存段设定关键字(为共享内存命名)，如果参数`key`的值和构造函数或者之前指定的关键字相同的话，则该函数将不做任何操作，直接返回：

``` cpp
void QSharedMemory::setKey ( const QString &key ); /* 设定共享内存关键字 */
```

### 锁定/解锁共享内存

&emsp;&emsp;为了保证共享内存中数据的完整性，当一个进程在读写共享内存的时候，其他进程不允许对该共享区域进行访问。`QSharedMemory`类提供了`lock`函数和`unlock`函数来实现这一共享内存访问机制。某一程序对共享内存进行读写操作之前，需要调用`lock`函数锁定该共享内存，之后独享共享内存中的数据，并对数据进行读写等操作；共享内存访问完毕，调用`unlock`函数，释放共享内存的使用权限。
&emsp;&emsp;如果共享内存资源当前处于释放状态，进程调用`lock`函数将共享内存中的资源锁定，并返回`true`，其他进程将不能访问该共享内存：

``` cpp
bool QSharedMemory::lock (); /* 锁定共享内存 */
```

如果共享内存被其他进程占用时，则该函数会一直处于阻塞状态，直到其他进程使用完毕，并且释放共享内存资源。
&emsp;&emsp;如果共享内存资源被当前进程所占有，调用`unlock`函数将解锁该共享资源，并返回`true`。如果当前进程没有占用该资源，或者共享内存被其他进程访问，则不做任何操作，并返回`false`：

``` cpp
bool QSharedMemory::unlock (); /* 解锁共享内存 */
```

### 错误原因

&emsp;&emsp;当共享内存出错时，调用`error`函数显示相应的错误代码：

``` cpp
SharedMemoryError QSharedMemory::error () const;
```

&emsp;&emsp;当共享内存出错时，调用`errorString`函数，以文本形式显示错误原因：

``` cpp
QString QSharedMemory::errorString () const;
```

### 获取共享内存的地址

&emsp;&emsp;在程序关联共享内存的前提下，调用`constData`或`data`函数返回共享内存中数据的起始地址。如果没有关联共享内存，则返回`0`：

``` cpp
const void *QSharedMemory::constData () const;
void *QSharedMemory::data ();
const void *QSharedMemory::data () const; /* 重载函数 */
```

### 获取共享内存的大小

&emsp;&emsp;调用`size`函数将返回程序所关联的共享内存的大小(字节)。如果没有关联的共享内存，则返回`0`：

``` cpp
int QSharedMemory::size () const;
```