---
title: sys模块
date: 2019-01-11 19:09:18
categories: Python语法
---
&emsp;&emsp;`sys`模块提供了一系列有关`Python`运行环境的变量和函数。

### sys.argv

&emsp;&emsp;可以用`sys.argv`获取当前正在执行的命令行参数的列表：

变量           | 解释
--------------|-----
`sys.argv[0]` | 当前程序名
`sys.argv[1]` | 第一个参数
`sys.argv[2]` | 第二个参数

``` python
import sys
​
# 获取脚本名字
print('The name of this program is: %s' % (sys.argv[0]))
print('The command line arguments are:')

for i in sys.argv:  # 获取参数列表
    print(i)
​
# 统计参数个数
print('There are %s arguments.' % (len(sys.argv) - 1))
```

### sys.platform

&emsp;&emsp;用于获取当前执行环境的平台，例如`win32`表示`Windows`系统，`linux2`表示是`linux`系统：

``` python
# linux
>>> import sys
>>> sys.platform
'linux2'
# windows
>>> import sys
>>> sys.platform
'win32'
```

使用该方法可以写出跨平台的代码：

``` python
import sys, os
​
osType = sys.platform

if osType == 'linux' or osType == 'linux2':
    command = 'clear'
else:
    command = 'cls'
​
os.system(command)
```

### sys.path

&emsp;&emsp;`path`是一个目录列表，供`Python`从中查找第三方扩展模块。在`python`启动时，`sys.path`根据内建规则、`PYTHONPATH`变量进行初始化。当我们导入一个模块时(`import xxx`)，默认情况下`python`解析器会搜索当前目录、已安装的内置模块和第三方模块，搜索路径存放在`sys`模块的`path`中：

``` python
>>> import sys
>>> sys.path
```

当我们要添加自己的搜索目录时，可以使用列表的`append`方法。如果模块和自己写的脚本不在同一个目录下，可以在脚本开头加上`sys.path.append(xxx)`。注意这种方法是在运行脚本时有效的，运行结束后就会失效：

``` python
import sys
sys.path.append('引用模块的地址')
```

### sys.builtin_module_names

&emsp;&emsp;`sys.builtin_module_names`返回一个列表，包含内建模块的名字。

``` python
import sys
​
def find_module(module):  # print sys.builtin_module_names
    if module in sys.builtin_module_names:
        print(module, "=>", "__builtin__")
    else:
        print(module, "=>", __import__(module).__file__)
​
find_module('os')
find_module('sys')
find_module('zlib')
find_module('string')
```

### sys.exit

&emsp;&emsp;调用`sys.exit(n)`可以中途退出程序，当参数非`0`时，会引发一个`SystemExit`异常。

``` python
import sys
​
print('running...')
​
try:
    sys.exit(1)
except SystemExit:
    print('SystemExit exit 1')
​
print('exited')
```

### sys.modules

&emsp;&emsp;使用`sys.modules`模块查找已导入的模块，`This is a dictionary that maps module names to modules which have already been loaded. This can be manipulated to force reloading of modules and other tricks`。`modules`字典包含所有加载的模块，`import`语句在从磁盘导入内容之前会先检查这个字典。

### sys.exc_info

&emsp;&emsp;This function returns a `tuple` of three values that give information about the exception that is currently being handled. The information returned is specific both to the current thread and to the current stack frame. If the current stack frame is not handling an exception, the information is taken from the calling stack frame, or its caller, and so on until a stack frame is found that is handling an exception. Here, `handling an exception` is defined as `executing an except clause`. For any stack frame, only information about the exception being currently handled is accessible.
&emsp;&emsp;If no exception is being handled anywhere on the stack, a tuple containing three `None` values is returned. Otherwise, the values returned are `(type, value, traceback)`. Their meaning is: `type` gets the `type` of the exception being handled (a subclass of `BaseException`); `value` gets the exception `instance` (an instance of the exception type); `traceback` gets a `traceback` object which encapsulates the call stack at the point where the exception originally occurred.

### sys.version

&emsp;&emsp;A string containing the version number of the `Python` interpreter plus additional information on the build number and compiler used. This string is displayed when the interactive interpreter is started. Do not extract version information out of it, rather, use `version_info` and the functions provided by the platform module.

### sys.version_info

&emsp;&emsp;A tuple containing the five components of the version number: `major`, `minor`, `micro`, `releaselevel`, and `serial`. All values except releaselevel are `integers`; the release level is `alpha`, `beta`, `candidate`, or `final`. The `version_info` value corresponding to the `Python version 2.0` is `(2, 0, 0, 'final', 0)`. The components can also be accessed by name, so `sys.version_info[0]` is equivalent to `sys.version_info.major` and so on.

``` python
>>> import sys
>>> sys.version
'3.6.3 (v3.6.3:2c5fed8, Oct  3 2017, 18:11:49) [MSC v.1900 64 bit (AMD64)]'
>>> sys.version_info
sys.version_info(major=3, minor=6, micro=3, releaselevel='final', serial=0)
```