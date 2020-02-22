---
title: Qt之QTime
categories: Qt语法详解
date: 2019-01-03 14:16:18
---
&emsp;&emsp;在`Qt`中，可以利用`QTime`类来控制时间，这里介绍一下`QTime`的成员函数的用法：<!--more-->

- `QTime::QTime()`：默认构造函数，构造一个时、分、秒都为`0`的时间，例如`00:00:00.000`(午夜)。
- `QTime::QTime(int h, int m, int s = 0, int ms = 0)`：构造一个用户指定时、分、秒的时间，参数有效值范围：`h`为`0`至`23`，`m`为`0`至`59`，`ms`为`0`至`999`。
- `QTime QTime::addMSecs(int ms) const`：返回一个当前时间对象之后或之前`ms`毫秒的时间对象(之前还是之后要看`ms`的符号，如果为正，就是之后，否则是之前)：

``` cpp
QTime time ( 3, 0, 0 );
QTime newTime1 = time.addMSecs ( 1000 );
QTime newTime2 = time.addMSecs ( -1000 );
```

`newTime1`是一个比`time`所指定时间(`03:00:00.000`)延后`1000`毫秒(即`1`秒)的时间(`03:00:01.000`)，而`newTime2`则提前`1000`毫秒(`02:59:59.000`)。

- `QTime QTime::addSecs(int nsecs) const`：与`addMSecs`相同，只是`nsecs`单位为秒，即返回一个当前时间对象之前或之后的时间对象。
- `int QTime::elapsed() const`：返回最后一次调用`start`或`restart`到现在已经经过的毫秒数。如果超过`24`小时，则计数器置`0`。
- `int QTime::hour() const`：返回时间对象的小时，取值范围为`0`至`23`。
- `int QTime::minute() const`：返回时间对象的分钟，取值范围为`0`至`59`。
- `int QTime::second() const`：返回时间对象的秒，取值范围为`0`至`59`。
- `int QTime::msec() const`：返回时间对象的毫秒，取值范围为`0`至`999`。
- `bool QTime::isNull() const`：如果时间对象等于`00:00:00.000`，就返回`true`，否则返回`false`。
- `bool QTime::isValid() const`：如果时间对象是有效的，就返回`true`，否则返回`false`(就是说时、分、秒、毫秒的数值都在其取值范围之内)。
- `int QTime::msecsTo(const QTime &t) const`：返回当前时间对象到`t`所指定的时间之间的毫秒数。如果`t`早于当前时间对象的时间，则返回的值是负值。因为一天的时间是`86400000`毫秒，所以返回值范围是`-86400000`至`86400000`。
- `int QTime::secsTo(const QTime &t) const`：与`msecsTo`基本相同，只是返回的是秒数，返回值的有效范围是`-86400`至`86400`。
- `int QTime::restart()`：设置当前时间对象的值为当前系统时间，并且返回从最后一次调用`start`或`restart`到现在的毫秒数。如果超过`24`小时，则计数器置`0`。
- `bool QTime::setHMS(int h, int m, int s, int ms = 0)`：设置当前时间对象的时、分、秒和毫秒。如果给定的参数值有效，就返回`true`，否则返回`false`。
- `void QTime::start()`：设置当前时间对象的值为当前系统时间，这个函数实际上是结合`restart`和`elapsed`来计数的。
- `QString QTime::toString(const QString &format) const`：按照参数`format`指定的格式用字符串形式输出当前时间对象的时间。参数`format`用来指定时、分、秒、毫秒的输出格式，例如`hh:mm:ss.zzz AP/ap`。

1. `h`表示小时，范围是`0`至`23`；`hh`用两位数表示小时，不足两位的前面用`0`补足，例如`0`点为`00`，`3`点为`03`，`11`点为`11`。
2. `m`表示分钟，范围是`0`至`59`；`mm`用两位数表示分钟，不足两位的前面用`0`补足。
3. `s`表示秒，范围是`0`至`59`；`ss`用两位数表示秒，不足两位的前面用`0`补足。
4. `z`表示毫秒，范围是`0`至`999`；`zzz`用三位数表示毫秒，不足三位的前面用`0`补足。
5. `AP`表示用`AM/PM`显示，`ap`表示用`ap/pm`显示。

``` cpp
QTime time ( 14, 3, 9, 42 ); /* 设置时间为“14:03:09.042” */
QString i = time.toString ( "hh:mm:ss.zzz" ); /* 结果为“14:03:09.042” */
QString j = time.toString ( "h:m:s.z" ); /* 结果为“14:3:9.42” */
QString m = time.toString ( "h:m:s.z AP" ); /* 结果为“2:3:9.42 PM” */
QString n = time.toString ( "h:m:s.z ap" ); /* 结果为“2:3:9.42 pm” */
```

- `QString QTime::toString(Qt::DateFormat f = Qt::TextDate) const`：按照参数`format`指定的格式用字符串形式输出当前时间对象的时间。`f`可选值如下：

可选值           | 说明
----------------|-----
`Qt::TextDate`  | 格式为`HH:MM:SS`
`Qt::ISODate`   | `ISO 8601`标准的时间表示格式，同样也为`HH:MM:SS`
`Qt::LocalDate` | 字符串格式依赖系统本地设置

&emsp;&emsp;静态成员函数如下：

- `QTime QTime::currentTime()`：返回当前的系统时间。
- `QTime QTime::fromString(const QString &string, Qt::DateFormat format = Qt::TextDate)`：使用参数`format`指定的格式，根据参数`string`指定的时间返回一个时间对象。如果`string`指定的时间不合法，则返回一个无效的时间对象。`format`可选值如下：

可选值           | 说明
----------------|-----
`Qt::TextDate`  | 格式为`HH:MM:SS`
`Qt::ISODate`   | `ISO 8601`标准的时间表示格式，同样也为`HH:MM:SS`
`Qt::LocalDate` | 字符串格式依赖系统本地设置

- `QTime QTime::fromString(const QString &string, const QString &format)`：使用参数`format`指定的格式，根据参数`string`指定的时间返回一个时间对象。如果`string`指定的时间不合法，则返回一个无效的时间对象。`format`的格式参考`QString QTime::toString(const QString &format) const`。
- `bool QTime::isValid(int h, int m, int s, int ms = 0)`：如果参数所指定的时间是合法的，就返回`true`，否则返回`false`。

&emsp;&emsp;静态成员函数不依赖于对象，可以通过类直接调用。例如获取当前系统时间的小时部分不需要定义`QTime`对象：

``` cpp
int hour = QTime::currentTime().hour();
```