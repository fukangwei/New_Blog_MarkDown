---
title: Perl时间日期
date: 2018-12-19 11:28:44
categories: Perl
---
&emsp;&emsp;`Perl`中处理时间的函数有如下几种：`time`函数(返回从`1970`年`1`月`1`日起累计的秒数)；`localtime`函数(获取本地时区时间)；`gmtime`函数(获取格林威治时间)。
&emsp;&emsp;`localtime`函数在没有参数的情况下返回当前的时间和日期。以下`9`个符号代表不同的时间日期参数：

``` perl
sec   # 秒，0到61
min   # 分钟，0到59
hour  # 小时，0到24
mday  # 天，1到31
mon   # 月，0到11
year  # 年，从1900开始
wday  # 星期几，0至6，0表示周日
yday  # 一年中的第几天
isdst # 如果夏令时有效，则为真
```

示例如下：

``` perl
@months = qw(一月 二月 三月 四月 五月 六月 七月 八月 九月 十月 十一月 十二月);
@days = qw(星期天 星期一 星期二 星期三 星期四 星期五 星期六);
($sec, $min, $hour, $mday, $mon, $year, $wday, $yday, $isdst) = localtime();
print "$mday $months[$mon] $days[$wday]\n"; # 输出“25 十月 星期四”
```

&emsp;&emsp;如果直接调用`localtime`，它返回系统当前设置时区的时间：

``` perl
$datestring = localtime();
# 输出“时间日期为：Thu Oct 25 14:54:59 2018”
print "时间日期为：$datestring\n";
```

#### 格林威治时间(GMT)

&emsp;&emsp;函数`gmtime`与`localtime`类似，但它返回标准格林威治时间：

``` perl
$local_datestring = localtime();
# 输出“本地时间日期为：Thu Oct 25 14:56:52 2018”
print "本地时间日期为：$local_datestring\n";
$gmt_datestring = gmtime();
# 输出“GMT时间日期为：Thu Oct 25 05:56:52 2018”
print "GMT时间日期为：$gmt_datestring\n";
```

### 格式化日期和时间

&emsp;&emsp;可以使用`localtime`函数的`9`个时间元素来输出需要制定的格式时间。格式化输出使用`printf`函数：

``` perl
($sec, $min, $hour, $mday, $mon, $year, $wday, $yday, $isdst) = localtime();
printf("格式化时间：HH:MM:SS\n");  # 输出“格式化时间：HH:MM:SS”
printf("%02d:%02d:%02d", $hour, $min, $sec);  # 输出“14:58:43”
```

### 新纪元时间(Epoch Time)

&emsp;&emsp;可以使用`time`函数来获取新纪元时间，该函数返回从`1970`年`1`月`1`日起累计的秒数：

``` perl
$epoc = time();
# 输出“从1970年1月1日起累计的秒数为：1540447256”
print "从1970年1月1日起累计的秒数为：$epoc\n";
```

可以输出一个想要的时间格式：

``` perl
($sec, $min, $hour, $mday, $mon, $year, $wday, $yday, $isdst) = localtime();
print "当期时间和日期：";
printf("%d-%d-%d %d:%d:%d", $year + 1900, $mon + 1, $mday, $hour, $min, $sec);
​
print "\n";
​
$epoc = time();
$epoc = $epoc - 24 * 60 * 60;
($sec, $min, $hour, $mday, $mon, $year, $wday, $yday, $isdst) = localtime($epoc);
print "昨天时间和日期：";
printf("%d-%d-%d %d:%d:%d", $year + 1900, $mon + 1, $mday, $hour, $min, $sec);
```

执行结果：

``` bash
当期时间和日期：2018-10-25 15:2:36
昨天时间和日期：2018-10-24 15:2:36
```

### strftime

&emsp;&emsp;函数`strftime`可以将时间格式化为我们想要的格式。下表列出了一些格式化的符号，`*`号表示该项依赖本地时间：

符号 | 描述                                                   | 实例
-----|-------------------------------------------------------|----
`%a` | 星期几的简称(`Sun` to `Sat`)`*`                            | Thu
`%A` | 星期几的全称(`Sunday` to `Saturday`)`*`                    | Thursday
`%b` | 月的简称(`Jan` to `Dec`)`*`                                | Aug
`%B` | 月的全称(`January` to `December`)`*`                       | August
`%c` | 日期和时间`*`                                          | Thu Aug 23 14:55:02 2001
`%C` | 年份除于`100`，并取整(`00-99`)                            | 20
`%d` | 一个月的第几天(`01-31`)                                 | 23
`%D` | 日期，`MM/DD/YY`相等于`%m/%d/%y`                        | 08/23/01
`%e` | 一个月的第几天，使用空格填充个位数(`1-31`)                | 23
`%F` | `YYYY-MM-DD`的简写，类似于`%Y-%m-%d`                    | 2001-08-23
`%g` | 年份的最后两位数(`00-99`)                               | 01
`%G` | 年                                                     | 2001
`%h` | 月的简称(和`%b`选项相同)`*`                             | Aug
`%H` | `24`小时制(`00-23`)                                      | 14
`%I` | `12`小时制(`01-12`)                                      | 02
`%j` | 一年的第几天(`001-366`)                                | 235
`%m` | 月(`01-12`)                                           | 08
`%M` | 分钟(`00-59`)                                         | 55
`%n` | 新行(`\n`)                                            |
`%p` | 显示出`AM`或`PM`                                      | PM
`%r` | 时间(`hh:mm:ss AM(或PM)`)，`12`小时`*`                   | 02:55:02 pm
`%R` | 时间(`HH:MM`)，`24`小时，相等于`%H:%M`                        | 14:55
`%S` | 秒数(`00-61`)                                                | 02
`%t` | 水平制表符(`\t`)                                              |
`%T` | 时间(`24`小时制)(`hh:mm:ss`)，相等于`%H:%M:%S`                 | 14:55
`%u` | (`ISO 8601`标准)星期几，星期一为`1`(`1-7`)                     | 4
`%U` | 一年中的第几周，星期天为第一天(`00-53`)                         | 33
`%V` | (`ISO 8601`标准)第几周(`00-53`)                               | 34
`%w` | 一个星期的第几天(`0`代表星期天)(`0-6`)                          | 4
`%W` | 一年的第几个星期，星期一为第一天(`00-53`)                       | 34
`%x` | 显示日期的格式(`mm/dd/yy`)`*`                                  | 08/23/01
`%X` | 显示时间格式`*`                                                | 14:55:02
`%y` | 年，两位数(`00-99`)                                            | 01
`%Y` | 年                                                             | 2001
`%z` | (`ISO 8601`标准)与`UTC`的时区偏移(`1 minute = 1, 1 hour = 100`) | +100
`%Z` | 当前时区的名称，例如中国标准时间`*`                              | CDT
`%%` | `%`符号                                                        | %

示例如下：

``` perl
use POSIX qw(strftime);
​
$datestring = strftime "%Y-%m-%d %H:%M:%S", localtime;
printf("时间日期 - $datestring\n"); # 输出“时间日期 - 2018-10-25 16:49:18”
$datestring = strftime "%Y-%m-%d %H:%M:%S", gmtime;
printf("时间日期 - $datestring\n"); # 输出“时间日期 - 2018-10-25 07:49:18”
```