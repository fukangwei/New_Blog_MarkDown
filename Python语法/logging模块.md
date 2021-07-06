---
title: logging模块
categories: Python语法
date: 2019-02-10 14:17:18
---
### 基础用法

&emsp;&emsp;执行如下代码：<!--more-->

``` python
import logging

logging.debug('debug message')
logging.info('info message')
logging.warning('warn message')
logging.error('error message')
logging.critical('critical message')
```

执行结果：

``` python
WARNING:root:warn message
ERROR:root:error message
CRITICAL:root:critical message
```

&emsp;&emsp;日志级别由低到高如下表所示，默认情况下，只有日志级别等于或高于`WARNING`才会输出到屏幕：

级别       | 何时使用
-----------|-------------
`DEBUG`    | 调试信息
`INFO`     | 软件按预期工作
`WARNING`  | 发生了一些意外，软件还是在正常工作
`ERROR`    | 发生了严重的问题，软件已不能执行一些功能了
`CRITICAL` | 严重错误，软件已不能继续运行了

### basicConfig

&emsp;&emsp;通过`logging.basicConfig`函数对日志的输出格式及方式进行相关配置：

``` python
import logging

logging.basicConfig(
    level=logging.DEBUG, format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',
    datefmt='%a, %d %b %Y %H:%M:%S', filename='myapp.log', filemode='w')
logging.debug('This is debug message')
logging.info('This is info message')
logging.warning('This is warning message')
```

在执行目录下生成`myapp.log`文件，其内容为：

``` python
Wed, 07 Jul 2021 06:01:07 test_python.py[line:6] DEBUG This is debug message
Wed, 07 Jul 2021 06:01:07 test_python.py[line:7] INFO This is info message
Wed, 07 Jul 2021 06:01:07 test_python.py[line:8] WARNING This is warning message
```

&emsp;&emsp;`logging.basicConfig`各个参数如下：

- `filename`：指定日志文件名。
- `filemode`：指定日志文件的打开模式，即`w`或`a`。
- `format`：指定输出的格式和内容：

格式             | 说明                  | 格式            | 说明
-----------------|----------------------|-----------------|------
`%(levelno)s`    | 打印日志级别的数值     | `%(levelname)s` | 打印日志级别名称
`%(pathname)s`   | 打印当前执行程序的路径 | `%(filename)s`  | 打印当前执行程序名
`%(funcName)s`   | 打印日志的当前函数     | `%(lineno)d`    | 打印日志的当前行号
`%(asctime)s`    | 打印日志的时间        | `%(thread)d`     | 打印线程`ID`
`%(threadName)s` | 打印线程名称          | `%(process)d`    | 打印进程`ID`
`%(message)s`    | 打印日志信息

- `datefmt`：指定时间格式，同`time.strftime`。
- `level`：设置日志级别，默认为`logging.WARNING`。
- `stream`：指定日志的输出流，可以指定输出到`sys.stderr`、`sys.stdout`或者文件，默认输出到`sys.stderr`。

### 重要概念

&emsp;&emsp;几个重要的概念如下：

- `Logger`：记录器，显示了应用程序代码能直接使用的接口。
- `Handler`：处理器，将记录器产生的日志记录发送至合适的目的地。
- `Filter`：过滤器，提供了更好的粒度控制，它可以决定输出哪些日志记录。
- `Formatter`：格式化器，指明了最终输出中日志记录的布局。

### Logger

&emsp;&emsp;在使用`debug`、`info`、`warn`、`error`、`critical`之前，必须创建`Logger`实例。
&emsp;&emsp;如果没有显式地进行创建，则系统默认创建一个`root logger`，并应用默认的日志级别、处理器和格式化器。
&emsp;&emsp;创建方法如下：

``` python
logger = logging.getLogger(logger_name)
```

&emsp;&emsp;创建`Logger`实例后，可以使用以下方法进行日志级别设置、增加或删除处理器`Handler`：

- `logger.setLevel(logging.ERROR)`：设置日志级别为`ERROR`，即只有日志级别大于等于`ERROR`的日志才会输出。
- `logger.addHandler(handler_name)`：为`Logger`实例增加一个处理器。
- `logger.removeHandler(handler_name)`：为`Logger`实例删除一个处理器

### Handler

&emsp;&emsp;`Handler`类型有很多种，例如`StreamHandler`，它是将日志信息打印输出在标准输出上，其创建方法如下：

``` python
sh = logging.StreamHandler(stream=None)
```

&emsp;&emsp;创建`StreamHandler`后，可以通过使用以下方法设置日志级别、设置格式化器`Formatter`、增加或删除过滤器`Filter`：

- `ch.setLevel(logging.WARN)`：指定日志级别，低于`WARN`级别的日志将被忽略。
- `ch.setFormatter(formatter_name)`：设置一个格式化器。
- `ch.addFilter(filter_name)`：增加一个过滤器，可以增加多个。
- `ch.removeFilter(filter_name)`：删除一个过滤器。

### Formatter

&emsp;&emsp;使用`Formatter`对象设置日志信息最后的规则、结构和内容，默认的时间格式为`%Y-%m-%d %H:%M:%S`。

``` python
formatter = logging.Formatter(fmt=None, datefmt=None)
```

- `fmt`是消息的格式化字符串。如果不指明`fmt`，将使用`%(message)s`。
- `datefmt`是日期字符串。如果不指明`datefmt`，将使用`ISO8601`日期格式。

### 输出到文件和屏幕

&emsp;&emsp;代码实例：

``` python
import logging

logging.basicConfig(
    level=logging.DEBUG, format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',
    datefmt='%a, %d %b %Y %H:%M:%S', filename='myapp.log', filemode='w')
# 定义一个StreamHandler，将INFO级别或更高的日志信息打印到sys.stderr
console = logging.StreamHandler()
console.setLevel(logging.INFO)
formatter = logging.Formatter('%(name)-12s: %(levelname)-8s %(message)s')
console.setFormatter(formatter)
logging.getLogger('').addHandler(console)
logging.debug('This is debug message')
logging.info('This is info message')
logging.warning('This is warning message')
```

执行结果是在屏幕上打印：

``` python
root        : INFO     This is info message
root        : WARNING  This is warning message
```

在执行目录下生成`myapp.log`文件，其内容为：

``` python
Wed, 07 Jul 2021 06:14:39 test_python.py[line:12] DEBUG This is debug message
Wed, 07 Jul 2021 06:14:39 test_python.py[line:13] INFO This is info message
Wed, 07 Jul 2021 06:14:39 test_python.py[line:14] WARNING This is warning message
```

### 日志回滚

&emsp;&emsp;代码实例：

``` python
import logging
from logging.handlers import RotatingFileHandler

# 定义一个RotatingFileHandler，最多备份5个日志文件，每个日志文件最大10M
Rthandler = RotatingFileHandler('myapp.log', maxBytes=10 * 1024 * 1024, backupCount=5)
Rthandler.setLevel(logging.INFO)
formatter = logging.Formatter('%(name)-12s: %(levelname)-8s %(message)s')
Rthandler.setFormatter(formatter)
logging.getLogger('').addHandler(Rthandler)
```