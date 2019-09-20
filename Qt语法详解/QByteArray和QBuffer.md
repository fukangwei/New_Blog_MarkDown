---
title: QByteArray和QBuffer
date: 2019-01-25 18:41:07
categories: Qt语法详解
---
&emsp;&emsp;`QByteArray`类提供了一个字节数组，既可以存储原始的字节(包括`\0`)，又可以被用来存储以`\0`结尾的字符串(可以理解为字符数组`char str[] = {'h', 'e', 'l', 'l', 'o', '\0'}`或者`char *str = "hello"`)。由于`QByteArray`封装的功能很多，使用起来比`char *`要方便的多，而就其内部实现来讲，它会保证所有的数据以`\0`结尾，使用隐式数据共享(`copy-on-write`)来减少内存消耗以及不必要的数据拷贝。
&emsp;&emsp;有两种情况比较适合使用`QByteArray`，第一就是要存储纯二进制数据(`raw binary data`)或`8bit`编码文本字符串，第二种情况就是在内存资源很珍贵的情况下，例如`Qt for Embedded Linux`。
&emsp;&emsp;一种初始化`QByteArray`方式是给它的构造函数传入一个`const char *`即可。此时，`QByteArray`执行了深拷贝。如果出于效率考虑不想执行深拷贝，使用`QByteArray::fromRawData(const char * data, int siz)`，其返回的`QByteArray`对象将会和数据指针相关联。
&emsp;&emsp;对于语句`QByteArray array("Hello");`，`array`的`size`是`5`，但由于其在最后要存储额外的`\0`，其实际占用空间是`6`。
&emsp;&emsp;常用操作函数如下：
&emsp;&emsp;1. `int QByteArray::size() const`：如果`QByteArray`在从`raw`数据创建时，不包含尾随的终止符，`QByteArray`不会自动添加，除非通过深拷贝进行创建：

``` cpp
QByteArray ba ( "Hello" );
int n = ba.size(); /* n = 5 */
ba.data() [0]; /* returns 'H' */
ba.data() [4]; /* returns 'o' */
ba.data() [5]; /* returns '\0' */
```

&emsp;&emsp;2. 和`C++`的普通数组一样，`QByteArray`也可以使用`[]`来访问其具体下表对应的字节。对于非`const`的`QByteArray`，可以直接进行赋值：

``` cpp
QByteArray array;
array.resize ( 5 );
array [0] = 0x3c;
array [1] = 0xb8;
array [2] = 0x64;
array [3] = 0x18;
array [4] = 0xca;
```

&emsp;&emsp;3. 对于只读操作，请使用`at`，因为它可以避免深拷贝，比使用`[]`要快，效率要高：

``` cpp
for ( int i = 0; i < array.size(); ++i ) {
    if ( array.at ( i ) >= 'a' && array.at ( i ) <= 'f' ) {
        cout << "Found character in range [a - f] " << endl;
    }
}
```

&emsp;&emsp;4. 可以使用`left`、`right`或者`mid`来实现一次取出多个字符：

- `QByteArray QByteArray::left(int len) const`: Returns a byte array that contains the leftmost `len` bytes of this byte array. The entire byte array is returned if `len` is greater than `size()`. Example:

``` cpp
QByteArray x ( "Pineapple" );
QByteArray y = x.left ( 4 ); /* y = "Pine" */
```

- `QByteArray QByteArray::right(int len) const`: Returns a byte array that contains the rightmost `len` bytes of this byte array. The entire byte array is returned if `len` is greater than `size()`.

``` cpp
QByteArray x ( "Pineapple" );
QByteArray y = x.right ( 5 ); /* y = "apple" */
```

- `QByteArray QByteArray::mid(int pos, int len = -1) const`：以`pos`作为起点，返回指定字节长度的`array`，如果`len`为`-1`(默认)，或者`pos + len >= size()`，将返回从指定为`pos`开始直到字节数组尾的所有字节。

``` cpp
QByteArray x ( "Five pineapples" );
QByteArray y = x.mid ( 5, 4 ); /* y = "pine" */
QByteArray z = x.mid ( 5 ); /* z = "pineapples" */
```

&emsp;&emsp;5. `char * QByteArray::data()`: Returns a pointer to the data stored in the byte array; `const char * QByteArray::constData() const`: Returns a pointer to the data stored in the byte array：通过`data`或者`constData`可以获得`QByteArray`的真实数据的指针，获得的数据指针在调用`QByteArray`的`non-const`函数之前都是有效的。

&emsp;&emsp;6. `uint qstrlen(const char * str)`: Returns the number of characters that precede(先于) the terminating `\0`, or `0` if `str` is `0`.

``` cpp
QByteArray array = "hello world!";
printf ( "%d\n", array.size() - 1 );
printf ( "%c\n", array.data() [array.size()] );
printf ( "%d\n", qstrlen ( ( const char * ) array.data() ) );
```

&emsp;&emsp;7. `QByteArray`提供了很多修改字节的方法，例如`append`、`prepend`、`insert`、`replace`和`remove`。

``` cpp
QByteArray x ( "and" );
x.prepend ( "rock " ); /* x = "rock and" */
x.append ( " roll" ); /* x = "rock and roll" */
x.replace ( 5, 3, "&" ); /* x = "rock & roll" */
```

&emsp;&emsp;8. `QBuffer`类是一个操作`QByteArray`的输入输出设备的接口，其构造函数为：

``` cpp
QBuffer ( QByteArray *byteArray, QObject *parent = 0 );
```

`QBuffer`类用来读写内存缓存。在使用之前，用`open`来打开缓存并且设置模式(`只读`、`只写`等)。
&emsp;&emsp;`QDataStream`和`QTextStream`也可以使用一个`QByteArray`参数来构造，这些构造函数创建并且打开一个内部的`QBuffer`。

---

### QBuffer Class Reference

&emsp;&emsp;该类一个`QByteArray`提供一个`QIODevice`接口类，其头文件为`QBuffer`，继承自`QIODevice`。注意，该类所以的函数是可重入的。
&emsp;&emsp;公共函数如下：

Return               | Function
---------------------|---------
                     | `QBuffer ( QObject * parent = 0 )`
                     | `QBuffer ( QByteArray * byteArray, QObject * parent = 0 )`
                     | `~QBuffer ()`
`QByteArray &`       | `buffer ()`
`const QByteArray &` | `buffer () const`
`const QByteArray &` | `data () const`
`void`               | `setBuffer ( QByteArray * byteArray )`
`void`               | `setData ( const QByteArray & data )`
`void`               | `setData ( const char * data, int size )`

重新实现的公共函数：

Return           | Function
-----------------|--------
`virtual bool`   | `atEnd () const`
`virtual bool`   | `canReadLine () const`
`virtual void`   | `close ()`
`virtual bool`   | `open ( OpenMode flags )`
`virtual qint64` | `pos () const`
`virtual bool`   | `seek ( qint64 pos )`
`virtual qint64` | `size () const`

重新实现的受保护的函数：

Return           | Function
-----------------|---------
`virtual qint64` | `readData ( char * data, qint64 len )`
`virtual qint64` | `writeData ( const char * data, qint64 len )`

### 详细描述

&emsp;&emsp;`QBuffer`允许你通过使用`QIODevice`接口来存取一个`QByteArray`。`QByteArray`被视为一个标准的随机存取文件。

``` cpp
QBuffer buffer;
char ch;
buffer.open ( QBuffer::ReadWrite );
buffer.write ( "Qt rocks!" );
buffer.seek ( 0 );
buffer.getChar ( &ch ); /* ch = 'Q' */
buffer.getChar ( &ch ); /* ch = 't' */
buffer.getChar ( &ch ); /* ch = ' ' */
buffer.getChar ( &ch ); /* ch = 'r' */
```

缺省情况下，当你创造一个`QBuffer`时，一个内部的`QByteArray`缓存被建立。你能通过调用`buffer`函数存取这个`buffer`，也能用一个存在的`QByteArray`通过调用`setBuffer`使用`QBuffer`，或者通过传递你的数组到`QBuffer`的构造函数调用`open`来打开`buffer`，然后调用`write`或者`putChar`来写`buffer`，`read`、`readLine`、`readALL`或者`getChar`来读`buffer`。`Size`返回目前`buffer`的大小，你能通过调用`seek`定位在这个`buffer`中的任意位置。当你退出时，应该调用`close`。下面的代码演示使用`QDataStream`和`QBuffer`写数据到一个`QByteArray`：

``` cpp
QByteArray byteArray;
QBuffer buffer ( &byteArray );
buffer.open ( QIODevice::WriteOnly );
​
QDataStream out ( &buffer );
out << QApplication::palette();
```

我们也可以转换`QPalette`到一个字节数组，下面是读数据：

``` cpp
QByteArray byteArray;
QPalette palette;
QBuffer buffer ( &byteArray );
buffer.open ( QIODevice::ReadOnly );
​
QDataStream in ( &buffer );
in >> palette;
```

&emsp;&emsp;`QTextStream`和`QDataStream`也提供方便的构造函数来在幕后构建`QByteArray`来创造一个`QBuffer`。当新的数据到达时，`QBuffer`发送信号`readRead`，通过连接这个信号，你能使用`QBuffer`来缓存即将被处理的数据。例如当从一个`FTP`服务器下载一个文件时，你能传递`buffer`到`QFtp`。当一个新的数据负荷已经被下载时，`readRead`信号被发出，当数据到达后，你能处理该数据。每当新数据已经被写入`buffer`时，`QBuffer`发出`bytesWritten`信号。
&emsp;&emsp;构造函数为：

``` cpp
QBuffer::QBuffer ( QObject *parent = 0 );
QBuffer::QBuffer ( QByteArray *byteArray, QObject *parent = 0 );
```

实例如下：

``` cpp
QByteArray byteArray ( "abc" );
QBuffer buffer ( &byteArray );
buffer.open ( QIODevice::WriteOnly );
buffer.seek ( 3 );
buffer.write ( "def", 3 );
buffer.close(); /* byteArray = "abcdef" */
```

&emsp;&emsp;析构函数为：

``` cpp
QBuffer::~QBuffer ()
```

&emsp;&emsp;其它函数如下：

Return               | Function
---------------------|--------
`bool`               | `QBuffer::atEnd () const [virtual]`
`QByteArray &`       | `QBuffer::buffer ()`
`const QByteArray &` | `QBuffer::buffer () const`
`bool`               | `QBuffer::canReadLine () const [virtual]`
`void`               | `QBuffer::close () [virtual]`
`const QByteArray &` | `QBuffer::data () const`
`bool`               | `QBuffer::open ( OpenMode flags ) [virtual]`
`qint64`             | `QBuffer::pos () const [virtual]`
`qint64`             | `QBuffer::readData ( char *data, qint64 len ) [virtual protected]`
`bool`               | `QBuffer::seek ( qint64 pos ) [virtual]`
`void`               | `QBuffer::setBuffer ( QByteArray *byteArray )`
`void`               | `QBuffer::setData ( const QByteArray &data )`
`void`               | `QBuffer::setData ( const char *data, int size )`
`qint64`             | `QBuffer::size () const [virtual]`
`qint64`             | `QBuffer::writeData ( const char *data, qint64 len ) [virtual protected]`

&emsp;&emsp;示例如下：

``` cpp
QByteArray byteArray ( "abc" );
QBuffer buffer;
buffer.setBuffer ( &byteArray );
buffer.open ( QIODevice::WriteOnly );
buffer.seek ( 3 );
buffer.write ( "def", 3 );
buffer.close(); /* byteArray = "abcdef" */
```

---

### Qt中QByteArray与byte之间转换

&emsp;&emsp;1. `byte`数组到`QByteArray`的转换
&emsp;&emsp;推荐使用如下方法进行初始化，即使数组中有`0`也能完整赋值进去，因为`QByteArray`不认为`\0`就是结尾：

``` cpp
QByteArray byArr;
byte cmd[5] = {'1', '2', '\0', '3', '4'};
byArr.resize ( 5 );
​
for ( int i = 0; i < 5; i++ ) {
    byArr[i] = cmd[i];
}
```

如果使用下面的转换方法，当数组中包含`0x00`(即`\0`)，就会丢失后面的数据：

``` cpp
QByteArray byArr = QByteArray ( ( const char * ) cmd );
```

&emsp;&emsp;2. `QByteArray`到`byte`数组的转换

``` cpp
int isize = byArr.size();
byte *pby = ( byte * ) ( byArr.data() );
```