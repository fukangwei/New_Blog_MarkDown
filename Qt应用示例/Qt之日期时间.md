---
title: Qt之日期时间
date: 2018-12-28 15:56:45
categories: Qt应用示例
---
&emsp;&emsp;获取系统当前时间，并设置显示格式：

``` cpp
QDateTime current_date_time = QDateTime::currentDateTime();
QString current_date = current_date_time.toString ( "yyyy-MM-dd hh:mm:ss ddd" );
```

例如`2013-05-24 13:09:10 周五`。
&emsp;&emsp;获取当前时间`时`、`分`、`秒`的方法如下，其时间范围是：小时为`0`至`23`，分钟为`0`至`59`，秒为`0`至`59`，毫秒为`0`至`999`：

``` cpp
QTime current_time = QTime::currentTime();
int hour = current_time.hour();
int minute = current_time.minute();
int second = current_time.second();
int msec = current_time.msec();
```

&emsp;&emsp;比较日期大小：

``` cpp
/* 获取当前时间及文件缓存时间 */
QDateTime currentDateTime = QDateTime::currentDateTime();
QDateTime dateTime = QDateTime::fromString ( strDate, sDateTimeFormat );
/* 获取文件缓存一个月之后的时间 */
QDateTime afterOneMonthDateTime = dateTime.addMonths ( 1 );
/* 如果缓存时间超过一个月，则删除 */
qint64 nSecs = afterOneMonthDateTime.secsTo ( currentDateTime );
​
if ( nSecs > 0 ) {
    QFile::remove ( strFilePath );
}
```