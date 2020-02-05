---
title: logging模块
categories: Python语法
abbrlink: 80af0fa2
date: 2019-02-10 14:17:18
---
&emsp;&emsp;将日志打印到屏幕：<!--more-->

``` python
import logging
​
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

默认情况下，`logging`将日志打印到屏幕，日志级别为`WARNING`(只有日志级别高于`WARNING`的日志信息才会输出)。日志级别大小关系为`CRITICAL > ERROR > WARNING > INFO > DEBUG > NOTSET`，当然也可以自己定义日志级别。日志级别如下：

级别       | 何时使用
-----------|-------------
`DEBUG`    | 详细信息，调试问题时会感兴趣
`INFO`     | 表明事情按预期工作
`WARNING`  | 表明发生了一些意外，或者不久的将来会发生问题(例如磁盘满了)，软件还是在正常工作
`ERROR`    | 由于发生了严重的问题，软件已不能执行一些功能了
`CRITICAL` | 严重错误，表明软件已不能继续运行了

日志格式如下：

``` python
WARINING : root           : warn message
日志级别  : logger实例名称 : 日志消息内容
```

&emsp;&emsp;通过`logging.basicConfig`函数对日志的输出格式及方式做相关配置：

``` python
import logging
​
logging.basicConfig(
    level=logging.DEBUG, format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',
    datefmt='%a, %d %b %Y %H:%M:%S', filename='myapp.log', filemode='w')
logging.debug('This is debug message')
logging.info('This is info message')
logging.warning('This is warning message')
```

在执行目录下生成`myapp.log`文件，其内容为：

``` python
Fri, 08 Dec 2017 15:46:59 hello.py[line:11] DEBUG This is debug message
Fri, 08 Dec 2017 15:46:59 hello.py[line:12] INFO This is info message
Fri, 08 Dec 2017 15:46:59 hello.py[line:13] WARNING This is warning message
```

`logging.basicConfig`函数各参数如下：

- `filename`：指定日志文件名。
- `filemode`：和`file`函数意义相同，指定日志文件的打开模式，即`w`或`a`。
- `format`：指定输出的格式和内容，`format`可以输出很多有用信息：

格式             | 说明
-----------------|-----
`%(levelno)s`    | 打印日志级别的数值
`%(levelname)s`  | 打印日志级别名称
`%(pathname)s`   | 打印当前执行程序的路径，其实就是`sys.argv[0]`
`%(filename)s`   | 打印当前执行程序名
`%(funcName)s`   | 打印日志的当前函数
`%(lineno)d`     | 打印日志的当前行号
`%(asctime)s`    | 打印日志的时间
`%(thread)d`     | 打印线程`ID`
`%(threadName)s` | 打印线程名称
`%(process)d`    | 打印进程`ID`
`%(message)s`    | 打印日志信息

- `datefmt`：指定时间格式，同`time.strftime`。
- `level`：设置日志级别，默认为`logging.WARNING`。
- `stream`：指定将日志的输出流，可以指定输出到`sys.stderr`、`sys.stdout`或者文件，默认输出到`sys.stderr`。当`stream`和`filename`同时指定时，`stream`被忽略。

&emsp;&emsp;几个重要的概念如下：

- `Logger`：记录器，显示了应用程序代码能直接使用的接口。
- `Handler`：处理器，将(记录器产生的)日志记录发送至合适的目的地。
- `Filter`：过滤器，提供了更好的粒度控制，它可以决定输出哪些日志记录。
- `Formatter`：格式化器，指明了最终输出中日志记录的布局。

### Logger记录器

&emsp;&emsp;`Logger`是一个树形层级结构，在使用接口`debug`、`info`、`warn`、`error`、`critical`之前必须创建`Logger`实例，即创建一个记录器。如果没有显式地进行创建，则默认创建一个`root logger`，并应用默认的日志级别(`WARN`)、处理器`Handler`(`StreamHandler`，就是将日志信息打印输出在标准输出上)和格式化器`Formatter`(默认的格式即为第一个简单使用程序中输出的格式)。
&emsp;&emsp;创建方法如下：

``` python
logger = logging.getLogger(logger_name)
```

创建`Logger`实例后，可以使用以下方法进行日志级别设置、增加处理器`Handler`。

- `logger.setLevel(logging.ERROR)`：设置日志级别为`ERROR`，即只有日志级别大于等于`ERROR`的日志才会输出。
- `logger.addHandler(handler_name)`：为`Logger`实例增加一个处理器。
- `logger.removeHandler(handler_name)`：为`Logger`实例删除一个处理器

### Handler处理器

&emsp;&emsp;`Handler`处理器类型有很多种，比较常用的有三个：`StreamHandler`、`FileHandler`和`NullHandler`。
&emsp;&emsp;`StreamHandler`的创建方法如下：

``` python
sh = logging.StreamHandler(stream=None)
```

创建`StreamHandler`后，可以通过使用以下方法设置日志级别、设置格式化器`Formatter`、增加或删除过滤器`Filter`：

- `ch.setLevel(logging.WARN)`：指定日志级别，低于`WARN`级别的日志将被忽略。
- `ch.setFormatter(formatter_name)`：设置一个格式化器`formatter`。
- `ch.addFilter(filter_name)`：增加一个过滤器，可以增加多个。
- `ch.removeFilter(filter_name)`：删除一个过滤器。

&emsp;&emsp;`FileHandler`的创建方法如下：

``` python
fh = logging.FileHandler(filename, mode='a', encoding=None, delay=False)
```

&emsp;&emsp;`NullHandler`类位于核心`logging`包，不做任何的格式化或者输出。本质上它是个`什么都不做`的`handler`，由库开发者使用。

### Formatter格式化器

&emsp;&emsp;使用`Formatter`对象设置日志信息最后的规则、结构和内容，默认的时间格式为`%Y-%m-%d %H:%M:%S`。创建方法如下：

``` python
formatter = logging.Formatter(fmt=None, datefmt=None)
```

其中`fmt`是消息的格式化字符串，`datefmt`是日期字符串。如果不指明`fmt`，将使用`%(message)s`；如果不指明`datefmt`，将使用`ISO8601`日期格式。

---

&emsp;&emsp;将日志同时输出到文件和屏幕：

``` python
import logging
​
logging.basicConfig(
    level=logging.DEBUG, format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',
    datefmt='%a, %d %b %Y %H:%M:%S', filename='myapp.log', filemode='w')
# 定义一个StreamHandler，将INFO级别或更高的日志信息打印到标准错误，并将其添加到当前的日志处理对象
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
Fri, 08 Dec 2017 15:53:58 hello.py[line:19] DEBUG This is debug message
Fri, 08 Dec 2017 15:53:58 hello.py[line:20] INFO This is info message
Fri, 08 Dec 2017 15:53:58 hello.py[line:21] WARNING This is warning message
```

&emsp;&emsp;`logging`之日志回滚：

``` python
import logging
from logging.handlers import RotatingFileHandler
​
# 定义一个RotatingFileHandler，最多备份5个日志文件，每个日志文件最大10M
Rthandler = RotatingFileHandler('myapp.log', maxBytes=10 * 1024 * 1024, backupCount=5)
Rthandler.setLevel(logging.INFO)
formatter = logging.Formatter('%(name)-12s: %(levelname)-8s %(message)s')
Rthandler.setFormatter(formatter)
logging.getLogger('').addHandler(Rthandler)
```

从上例和本例可以看出，`logging`有一个日志处理的主对象，其它处理方式都是通过`addHandler`添加进去的。`logging`的几种`handle`方式如下：

- `logging.StreamHandler`：日志输出到流，可以是`sys.stderr`、`sys.stdout`或者文件。
- `logging.FileHandler`：日志输出到文件
- `logging.handlers.SocketHandler`：远程输出日志到`TCP/IP sockets`。
- `logging.handlers.DatagramHandler`：远程输出日志到`UDP sockets`。
- `logging.handlers.SMTPHandler`：远程输出日志到邮件地址。
- `logging.handlers.SysLogHandler`：日志输出到`syslog`。
- `logging.handlers.NTEventLogHandler`：远程输出日志到`Windows NT/2000/XP`的事件日志。
- `logging.handlers.MemoryHandler`：日志输出到内存中的指定`buffer`。
- `logging.handlers.HTTPHandler`：通过`GET`或`POST`远程输出到`HTTP`服务器。

以下是日志回滚方式，经常使用`RotatingFileHandler`和`TimedRotatingFileHandler`：

- `logging.handlers.BaseRotatingHandler`
- `logging.handlers.RotatingFileHandler`
- `logging.handlers.TimedRotatingFileHandler`

由于`StreamHandler`和`FileHandler`是常用的日志处理方式，所以直接包含在`logging`模块中，而其他方式则包含在`logging.handlers`模块中。