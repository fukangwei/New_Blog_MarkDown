---
title: datetime模块
categories: Python语法
abbrlink: 7c528cb9
date: 2019-02-09 21:14:50
---
&emsp;&emsp;`datetime`是`Python`处理日期和时间的标准库。<!--more-->

### 获取当前日期和时间

&emsp;&emsp;先看如何获取当前日期和时间：

``` python
>>> from datetime import datetime
>>> now = datetime.now()  # 获取当前datetime
>>> print(now)
2018-04-20 17:28:22.173096
>>> print(type(now))
<class 'datetime.datetime'>
```

### 获取指定日期和时间

&emsp;&emsp;要指定某个日期和时间，我们直接用参数构造一个`datetime`：

``` python
>>> from datetime import datetime
>>> dt = datetime(2015, 4, 19, 12, 20)  # 用指定日期时间创建datetime
>>> print(dt)
2015-04-19 12:20:00
```

### datetime转换为timestamp

&emsp;&emsp;在计算机中，时间实际上是用数字表示的。我们把`1970年1月1日 00:00:00 UTC+00:00时区`的时刻称为`epoch time`，记为`0`(`1970`年以前的时间`timestamp`为负数)。当前时间就是相对于`epoch time`的秒数，称为`timestamp`。你可以认为`timestamp = 0 = 1970-1-1 00:00:00 UTC+0:00`，对应的北京时间是`timestamp = 0 = 1970-1-1 08:00:00 UTC+8:00`。可见`timestamp`的值与时区毫无关系，因为`timestamp`一旦确定，其`UTC`时间就确定了，转换到任意时区的时间也是完全确定的，这就是为什么计算机存储的当前时间是以`timestamp`表示的，因为全球各地的计算机在任意时刻的`timestamp`都是完全相同的(假定时间已校准)。
&emsp;&emsp;把一个`datetime`类型转换为`timestamp`只需要简单调用`timestamp`方法：

``` python
>>> from datetime import datetime
>>> dt = datetime(2015, 4, 19, 12, 20)  # 用指定日期时间创建datetime
>>> dt.timestamp()  # 把datetime转换为timestamp
1429417200.0
```

注意`Python`的`timestamp`是一个浮点数。如果有小数位，小数位表示毫秒数。某些编程语言(如`Java`和`JavaScript`)的`timestamp`使用整数表示毫秒数，这种情况下只需要把`timestamp`除以`1000`就得到`Python`的浮点表示方法。

### timestamp转换为datetime

&emsp;&emsp;要把`timestamp`转换为`datetime`，使用`datetime`提供的`fromtimestamp`方法：

``` python
>>> from datetime import datetime
>>> t = 1429417200.0
>>> print(datetime.fromtimestamp(t))
2015-04-19 12:20:00
```

注意到`timestamp`是一个浮点数，它没有时区的概念，而`datetime`是有时区的。上述转换是在`timestamp`和本地时间做转换。
&emsp;&emsp;本地时间是指当前操作系统设定的时区。例如北京时区是东`8`区，则本地时间`2015-04-19 12:20:00`，实际上就是`UTC+8:00时区`的时间`2015-04-19 12:20:00 UTC+8:00`。
&emsp;&emsp;`timestamp`也可以直接被转换到`UTC`标准时区的时间：

``` python
>>> from datetime import datetime
>>> t = 1429417200.0
>>> print(datetime.fromtimestamp(t))  # 本地时间
2015-04-19 12:20:00
>>> print(datetime.utcfromtimestamp(t))  # UTC时间
2015-04-19 04:20:00
```

### str转换为datetime

&emsp;&emsp;很多时候，用户输入的日期和时间是字符串，要处理日期和时间，首先必须把`str`转换为`datetime`。转换方法是通过`datetime.strptime`实现，需要一个日期和时间的格式化字符串：

``` python
>>> from datetime import datetime
>>> cday = datetime.strptime('2015-6-1 18:19:59', '%Y-%m-%d %H:%M:%S')
>>> print(cday)
2015-06-01 18:19:59
```

字符串`%Y-%m-%d %H:%M:%S`规定了日期和时间部分的格式。注意转换后的`datetime`是没有时区信息的。

### datetime转换为str

&emsp;&emsp;如果已经有了`datetime`对象，要把它格式化为字符串显示给用户，就需要转换为`str`，转换方法是通过`strftime`实现的，同样需要一个日期和时间的格式化字符串：

``` python
>>> from datetime import datetime
>>> now = datetime.now()
>>> print(now.strftime('%a, %b %d %H:%M'))
Mon, May 05 16:28
```

### datetime加减

&emsp;&emsp;对日期和时间进行加减实际上就是把`datetime`往后或往前计算，得到新的`datetime`。加减可以直接用`+`和`-`运算符，不过需要导入`timedelta`这个类：

``` python
>>> from datetime import datetime, timedelta
>>> now = datetime.now()
>>> now
datetime.datetime(2015, 5, 18, 16, 57, 3, 540997)
>>> now + timedelta(hours=10)
datetime.datetime(2015, 5, 19, 2, 57, 3, 540997)
>>> now - timedelta(days=1)
datetime.datetime(2015, 5, 17, 16, 57, 3, 540997)
>>> now + timedelta(days=2, hours=12)
datetime.datetime(2015, 5, 21, 4, 57, 3, 540997)
```

可见，使用`timedelta`可以很容易地算出前几天和后几天的时刻。

### 本地时间转换为UTC时间

&emsp;&emsp;本地时间是指系统设定时区的时间，例如北京时间是`UTC+8:00时区`的时间，而`UTC`时间指`UTC+0:00时区`的时间。
&emsp;&emsp;一个`datetime`类型有一个时区属性`tzinfo`，但是默认为`None`，所以无法区分这个`datetime`到底是哪个时区，除非强行给`datetime`设置一个时区：

``` python
>>> from datetime import datetime, timedelta, timezone
>>> tz_utc_8 = timezone(timedelta(hours=8))  # 创建时区UTC+8:00
>>> now = datetime.now()
>>> now
datetime.datetime(2015, 5, 18, 17, 2, 10, 871012)
>>> dt = now.replace(tzinfo=tz_utc_8)  # 强制设置为UTC+8:00
>>> dt
datetime.datetime(2015, 5, 18, 17, 2, 10, 871012, tzinfo=datetime.timezone(datetime.timedelta(0, 28800)))
```

如果系统时区恰好是`UTC+8:00`，那么上述代码就是正确的，否则不能强制设置为`UTC+8:00时区`。

### 时区转换

&emsp;&emsp;我们可以先通过`utcnow`拿到当前的`UTC`时间，再转换为任意时区的时间：

``` python
>>> # 拿到UTC时间，并强制设置时区为“UTC+0:00”
>>> utc_dt = datetime.utcnow().replace(tzinfo=timezone.utc)
>>> print(utc_dt)
2015-05-18 09:05:12.377316+00:00
>>> # astimezone将转换时区为北京时间
>>> bj_dt = utc_dt.astimezone(timezone(timedelta(hours=8)))
>>> print(bj_dt)
2015-05-18 17:05:12.377316+08:00
>>> # astimezone将转换时区为东京时间
>>> tokyo_dt = utc_dt.astimezone(timezone(timedelta(hours=9)))
>>> print(tokyo_dt)
2015-05-18 18:05:12.377316+09:00
>>> # astimezone将bj_dt转换时区为东京时间
>>> tokyo_dt2 = bj_dt.astimezone(timezone(timedelta(hours=9)))
>>> print(tokyo_dt2)
2015-05-18 18:05:12.377316+09:00
```

时区转换的关键在于，拿到一个`datetime`时，要获知其正确的时区，然后强制设置时区，作为基准时间。利用带时区的`datetime`，通过`astimezone`方法，可以转换到任意时区。
&emsp;&emsp;注意，不是必须从`UTC+0:00时区`转换到其他时区，任何带时区的`datetime`都可以正确转换，例如上述`bj_dt`到`tokyo_dt`的转换。