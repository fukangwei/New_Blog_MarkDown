---
title: VBA日期时间函数
date: 2019-07-22 18:51:08
categories: VBA编程
---
&emsp;&emsp;`VBScript`日期和时间函数帮助开发人员将日期和时间从一种格式转换为另一种格式，或以适合特定条件的格式表示日期或时间值。
<!--more-->
### 日期函数

#### Date函数

&emsp;&emsp;这个函数返回当前的系统日期：

``` vbscript
date()
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    Dim a as Variant
    a = date()
    MsgBox "The Value of a: " & a
End Sub
```

运行结果(当前时间是`2019`年`7`月`18`日)：

``` vbscript
The Value of a: 07/18/19
```

#### CDate函数

&emsp;&emsp;这个函数将有效的日期和时间表达式转换为类型日期:

``` vbscript
CDate(date)
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    Dim a As Variant
    Dim b As Variant

    a = CDate("Jan 01 2020")
    MsgBox("The Value of a : " & a)

    b = CDate("31 Dec 2050")
    MsgBox("The Value of b : " & b)

    c = CDate("2018-12-20")
    MsgBox("The Value of c : " & c)
End Sub
```

运行结果：

``` vbscript
The Value of a: 2020/01/01
The Value of b: 2050/12/31
The Value of c: 2018/12/20
```

#### DateDiff函数

&emsp;&emsp;`DateDiff`函数返回两个指定的时间间隔之间的差值：

``` vbscript
DateDiff(interval, date1, date2 [, firstdayofweek [, firstweekofyear]])
```

- `interval`：必需参数，有以下选项：

1. `d`：一年中的一天。
2. `m`：一年中的月份。
3. `y`：一年中的年份。
4. `yyyy`：年份。
5. `w`：工作日。
6. `ww`：星期。
7. `q`：季度。
8. `h`：小时。
9. `m`：分钟。
10. `s`：秒钟。

- `Date1`和`Date2`：必需的日期参数。
- `Firstdayofweek`：可选参数。指定一周的第一天，有以下选项：

1. `vbUseSystemDayOfWeek`：使用国家语言支持(`NLS`)`API`设置。
2. `vbSunday`：星期天。
3. `vbMonday`：星期一。
4. `vbTuesday`：星期二。
5. `vbWednesday`：星期三。
6. `vbThursday`：星期四。
7. `vbFriday`：星期五。
8. `vbSaturday`：星期六。

- `Firstdayofyear`：一个可选参数，指定一年中的第一天，有以下选项：

1. `vbUseSystem`：使用国家语言支持(`NLS`)`API`设置。
2. `vbFirstJan1`：从`1月1日`发生的那一周开始(默认)。
3. `vbFirstFourDays`：从新年至少有四天的一周开始。
4. `vbFirstFullWeek`：从新年的第一个整周开始。

``` vbscript
Private Sub Constant_Click()
    Dim fromDate As Variant
    fromDate = "01-Jan-09 00:00:00"

    Dim toDate As Variant
    toDate = "01-Jan-10 23:59:00"

    MsgBox("Line 1: " & DateDiff("yyyy", fromDate, toDate))
    MsgBox("Line 2: " & DateDiff("q", fromDate, toDate))
    MsgBox("Line 3: " & DateDiff("m", fromDate, toDate))
    MsgBox("Line 4: " & DateDiff("y", fromDate, toDate))
    MsgBox("Line 5: " & DateDiff("d", fromDate, toDate))
    MsgBox("Line 6: " & DateDiff("w", fromDate, toDate))
    MsgBox("Line 7: " & DateDiff("ww", fromDate, toDate))
    MsgBox("Line 8: " & DateDiff("h", fromDate, toDate))
    MsgBox("Line 9: " & DateDiff("n", fromDate, toDate))
    MsgBox("Line 10: " & DateDiff("s", fromDate, toDate))
End Sub
```

运行结果：

``` vbscript
Line 1: 1
Line 2: 4
Line 3: 12
Line 4: 365
Line 5: 365
Line 6: 52
Line 7: 52
Line 8: 8783
Line 9: 527039
Line 10: 31622340
```

#### DatePart函数

&emsp;&emsp;`DatePart`函数返回给定日期的特定部分：

``` vbscript
DatePart(interval, date [, firstdayofweek [, firstweekofyear]])
```

- `interval`：必需参数，有以下选项：

1. `d`：一年中的一天。
2. `m`：一年中的月份。
3. `y`：一年中的年份。
4. `yyyy`：年份。
5. `w`：工作日。
6. `ww`：星期。
7. `q`：季度。
8. `h`：小时。
9. `m`：分钟。
10. `s`：秒钟。

- `Date1`：必需的日期参数。
- `Firstdayofweek`：可选参数，指定一周的第一天，有以下选项：

1. `vbUseSystemDayOfWeek`：使用国家语言支持(`NLS`)`API`设置。
2. `vbSunday`：星期天。
3. `vbMonday`：星期一。
4. `vbTuesday`：星期二。
5. `vbWednesday`：星期三。
6. `vbThursday`：星期四。
7. `vbFriday`：星期五。
8. `vbSaturday`：星期六。

- `Firstdayofyear`：可选参数，指定一年中的第一天，有以下选项：

1. `vbUseSystem`：使用国家语言支持(`NLS`)`API`设置。
2. `vbFirstJan1`：从`1月1日`发生的那一周开始(默认)。
3. `vbFirstFourDays`：从新年至少有四天的一周开始。
4. `vbFirstFullWeek`：从新年的第一个整周开始。

``` vbscript
Private Sub Constant_demo_Click()
    Dim Quarter As Variant
    Dim DayOfYear As Variant
    Dim WeekOfYear As Variant

    Date1 = "2013-01-15"
    Quarter = DatePart("q", Date1)

    MsgBox("Line 1: " & Quarter)
    DayOfYear = DatePart("y", Date1)

    MsgBox("Line 2: " & DayOfYear)
    WeekOfYear = DatePart("ww", Date1)

    MsgBox("Line 3: " & WeekOfYear)
    MsgBox("Line 4: " & DatePart("m", Date1))
End Sub
```

运行结果：

``` vbscript
Line 1: 1
Line 2: 15
Line 3: 3
Line 4: 1
```

#### DateSerial函数

&emsp;&emsp;`DateSerial`函数返回指定的日期、月份和年份参数的日期：

``` vbscript
DateSerial(year, month, day)
```

- `year`：必需参数，介于`100`和`9999`之间的数字或数字表达式。`0`到`99`之间的值被解释为`1900`到`1999`年。对于所有其他年份的参数，使用完整的四位数年份。
- `month`：必需参数，它也可以是表达式的形式，其范围是`1`至`12`。
- `day`：必需参数，它也可以是一个表达式的形式，其范围是`1`至`31`。

``` vbscript
Private Sub Constant_deme()
    MsgBox(DateSerial(2018, 5, 10))
End Sub
```

运行结果：

``` vbscript
2018/5/10
```

#### FormatDateTime函数

&emsp;&emsp;`FormatDateTime`函数可以帮助开发人员格式化并返回有效的日期和时间表达式：

``` vbscript
FormatDateTime(date, format)
```

- `date`：必需参数。
- `format`：可选参数，指定要使用的日期或时间格式的值，有以下选项：

1. `vbGeneralDate`：默认值。
2. `vbLongDate`：返回长日期。
3. `vbShortDate`：返回短日期。
4. `vbLongTime`：返回长时间。
5. `vbShortTime`：返回短时间。

``` vbscript
Private Sub Constant_demo()
    d = ("2018-08-15 20:25")
    MsgBox("Line 1: " & FormatDateTime(d))
    MsgBox("Line 2: " & FormatDateTime(d, 1))
    MsgBox("Line 3: " & FormatDateTime(d, 2))
    MsgBox("Line 4: " & FormatDateTime(d, 3))
    MsgBox("Line 5: " & FormatDateTime(d, 4))
End Sub
```

运行结果：

``` vbscript
Line 1: 15/08/2018 8:25:00 PM
Line 2: Thursday, 15 August 2018
Line 3: 15/08/2018
Line 4: 8:25:00 PM
Line 5: 20:25
```

#### IsDate函数

&emsp;&emsp;`IsDate`函数无论给定的输入是否为日期，它都会返回一个布尔值：

``` vbscript
IsDate(expression)
```

示例如下：

``` vbscript
Private Sub Constant_demo()
    MsgBox("Line 1: " & IsDate("Nov 03, 1950"))
    MsgBox("Line 2: " & IsDate(#1/31/2020#))
    MsgBox("Line 3: " & IsDate(#5/31/2020 10:30:00 PM#))
End Sub
```

运行结果：

``` vbscript
Line 1: True
Line 2: True
Line 3: True
```

#### Day函数

&emsp;&emsp;`Day`函数返回`1`到`31`之间的数字，表示指定日期的一天：

``` vbscript
Day(date)
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox(Day("2018-06-30"))
End Sub
```

运行结果：

``` vbscript
30
```

#### Month函数

&emsp;&emsp;`Month`函数返回`1`到`12`之间的数字，表示指定日期的月份：

``` vbscript
Month(date)
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox("Current month is: " & Month("2018-06-30"))
End Sub
```

运行结果：

``` vbscript
Current month is: 6
```

#### Year函数

&emsp;&emsp;`Year`函数返回一个表示指定日期的年份的整数：

``` vbscript
Year(date)
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox(Year("2018-06-30"))
End Sub
```

运行结果：

``` vbscript
2018
```

#### MonthName函数

&emsp;&emsp;`MonthName`函数返回指定日期的月份名称：

``` vbscript
MonthName(month [, toabbreviate])
```

- `month`：必需参数，指定了月份的数字值。
- `toabbreviate`：可选参数，布尔类型，指示月份名称是否缩写，默认为`False`。

``` vbscript
Private Sub Constant_demo()
    MsgBox("Line 1: " & MonthName(1, True))
    MsgBox("Line 2: " & MonthName(1, False))
End Sub
```

运行结果：

``` vbscript
Line 1: Jan
Line 2: January
```

### 时间函数

#### Now函数

&emsp;&emsp;`Now`函数现在返回当前的系统日期和时间：

``` vbscript
Now()
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    Dim a as Variant
    a = Now()
    MsgBox("当前系统的时间是：" & a)
End Sub
```

运行结果：

``` vbscript
当前系统的时间是：07/19/19 4:21:57 PM
```

#### Hour函数

&emsp;&emsp;`Hour`函数返回`0`到`23`之间的数字，表示指定时间戳的一天中的小时：

``` vbscript
Hour(time)
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox("Line 1: " & Hour("3:13:45 PM"))
    MsgBox("Line 2: " & Hour("23:13:45"))
    MsgBox("Line 3: " & Hour("2:20 PM"))
End Sub
```

运行结果：

``` vbscript
Line 1: 15
Line 2: 23
Line 3: 14
```

#### Minute函数

&emsp;&emsp;`Minute`函数返回`0`到`59`之间的数字，表示指定时间戳的分钟数值：

``` vbscript
Minute(time)
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox ("Line 1: " & Minute("3:13:45 PM"))
    MsgBox ("Line 2: " & Minute("23:43:45"))
    MsgBox ("Line 3: " & Minute("2:20 PM"))
End Sub
```

运行结果：

``` vbscript
Line 1: 13
Line 2: 43
Line 3: 20
```

#### Second函数

&emsp;&emsp;`Second`函数返回`0`到`59`之间的数字，表示指定时间戳的秒钟数值：

``` vbscript
Second(time)
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox ("Line 1: " & Second("3:13:25 PM"))
    MsgBox ("Line 2: " & Second("23:13:45"))
    MsgBox ("Line 3: " & Second("2:20 PM"))
End Sub
```

运行结果：

``` vbscript
Line 1: 25
Line 2: 45
Line 3: 0
```

#### Time函数

&emsp;&emsp;`Time`函数返回当前的系统时间：

``` vbscript
Time()
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox ("当前的系统时间是：" & Time())
End Sub
```

执行结果：

``` vbscript
当前的系统时间是：3:00:00
```

#### Timer函数

&emsp;&emsp;`Timer`函数返回自凌晨`00:00`起的秒数和毫秒数：

``` vbscript
Timer()
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox ("Time is: " & Now())
    MsgBox ("Timer is: " & Timer())
End Sub
```

运行结果：

``` vbscript
Time is: 2017/11/24 3:05:15
Timer is: 11124.53
```

#### TimeSerial函数

&emsp;&emsp;`TimeSerial`函数返回指定小时，分钟和秒值的时间：

``` vbscript
TimeSerial(hour, minute, second)
```

- `hour`：必需参数，它是介于`0`和`23`之间的整数或任何数字表达式。
- `minute`：必需参数，它是介于`0`和`59`之间的整数或任何数字表达式。
- `second`：必需参数，它是介于`0`和`59`之间的整数或任何数字表达式。

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox (TimeSerial(20, 1, 2))
    MsgBox (TimeSerial(0, 59, 59))
    MsgBox (TimeSerial(7 * 2, 60 / 3, 15 + 3))
End Sub
```

运行结果：

``` vbscript
8:01:02 PM
12:59:59 AM
2:20:18 PM
```

#### TimeValue函数

&emsp;&emsp;`TimeValue`函数将给定的输入字符串转换为有效的时间：

``` vbscript
TimeValue(StringTime)
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox (TimeValue("20:30"))
    MsgBox (TimeValue("5:15"))
    MsgBox (TimeValue("2:30:58"))
End Sub
```

执行结果：

``` vbscript
8:30:00 PM
5:15:00 AM
2:30:58 AM
```