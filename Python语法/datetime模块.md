---
title: datetime模块
categories: Python语法
date: 2019-02-09 21:14:50
---
### 获取当前日期和时间

&emsp;&emsp;获取当前日期和时间的方法如下：<!--more-->

``` python
>>> from datetime import datetime
>>> now = datetime.now()  # 获取当前datetime
>>> print(now)
2018-04-20 17:28:22.173096
>>> print(type(now))
<class 'datetime.datetime'>
```

### 获取指定日期和时间

&emsp;&emsp;指定某个日期和时间的方法如下：

``` python
>>> from datetime import datetime
>>> dt = datetime(2015, 4, 19, 12, 20)
>>> print(dt)
2015-04-19 12:20:00
```

### datetime转换为timestamp

&emsp;&emsp;我们把`1970年1月1日 00:00:00 UTC+00:00时区`的时刻称为`epoch time`，记为`0`。当前时间相对于`epoch time`的秒数，称为`timestamp`。
&emsp;&emsp;把`datetime`转换为`timestamp`的方法如下：

``` python
>>> from datetime import datetime
>>> dt = datetime(2015, 4, 19, 12, 20)
>>> dt.timestamp()  # 把datetime转换为timestamp
1429417200.0
```

`Python`的`timestamp`是一个浮点数，如果有小数位，则小数位表示毫秒数。

### timestamp转换为datetime

&emsp;&emsp;把`timestamp`转换为`datetime`的方法如下：

``` python
>>> from datetime import datetime
>>> t = 1429417200.0
>>> print(datetime.fromtimestamp(t))
2015-04-19 12:20:00
```

&emsp;&emsp;`timestamp`也可以直接转换到`UTC`标准时区的时间：

``` python
>>> from datetime import datetime
>>> t = 1429417200.0
>>> print(datetime.fromtimestamp(t))  # 本地时间
2015-04-19 12:20:00
>>> print(datetime.utcfromtimestamp(t))  # UTC时间
2015-04-19 04:20:00
```

### str转换为datetime

&emsp;&emsp;用户输入的日期和时间常常是字符串，这时必须要把`str`转换为`datetime`：

``` python
>>> from datetime import datetime
>>> cday = datetime.strptime('2015-6-1 18:19:59', '%Y-%m-%d %H:%M:%S')
>>> print(cday)
2015-06-01 18:19:59
```

字符串`%Y-%m-%d %H:%M:%S`规定了日期和时间部分的格式。

### datetime转换为str

&emsp;&emsp;如果已经有了`datetime`对象，要把它格式化为字符串显示给用户，就需要转换为`str`：

``` python
>>> from datetime import datetime
>>> now = datetime.now()
>>> print(now.strftime('%a, %b %d %H:%M'))
Mon, May 05 16:28
```

### datetime加减

&emsp;&emsp;对日期和时间进行加减，实际上就是把`datetime`往后或往前计算，得到新的`datetime`：

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

### 本地时间转换为UTC时间

&emsp;&emsp;本地时间是指系统设定时区的时间，例如北京时间是`UTC+8:00时区`，而`UTC`时间指`UTC+0:00时区`。
&emsp;&emsp;`datetime`类型有一个时区属性`tzinfo`，默认为`None`，可以为其设置一个时区：

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

### 时区转换

&emsp;&emsp;可以先通过`utcnow`拿到当前的`UTC`时间，再转换为任意时区的时间：

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