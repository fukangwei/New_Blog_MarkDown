---
title: sys模块
categories: Python语法
date: 2019-01-11 19:09:18
---
&emsp;&emsp;`sys`模块提供了一系列有关`Python`运行环境的变量和函数。<!--more-->

### sys.argv

&emsp;&emsp;可以用`sys.argv`获取当前正在执行的命令行参数列表：

- `sys.argv[0]`：当前程序名。
- `sys.argv[1]`：第一个参数。

``` python
import sys

# 获取脚本名字
print('The name of this program is: %s' % (sys.argv[0]))
print('The command line arguments are:')

for i in sys.argv:  # 获取参数列表
    print(i)

# 统计参数个数
print('There are %s arguments.' % (len(sys.argv) - 1))
```

### sys.platform

&emsp;&emsp;用于获取当前执行环境的平台，例如`win32`表示`Windows`系统，`linux2`表示`linux`系统。
&emsp;&emsp;使用该方法可以写出跨平台的代码：

``` python
import sys, os

osType = sys.platform

if osType == 'linux' or osType == 'linux2':
    command = 'clear'
else:
    command = 'cls'

os.system(command)
```

### sys.path

&emsp;&emsp;在`python`启动时，`sys.path`根据内建规则、`PYTHONPATH`变量进行初始化，用于`Python`查找第三方扩展模块：

``` python
>>> import sys
>>> sys.path
```

&emsp;&emsp;当需要添加自己的搜索目录时，可以使用`append`：

``` python
import sys
sys.path.append('引用模块的地址')
```

&emsp;&emsp;注意，这种方法只是在运行脚本时有效的，运行结束后就会失效。

### sys.exit

&emsp;&emsp;`sys.exit(n)`用于中途退出程序，当`n`为`非0`时，会引发一个`SystemExit`异常：

``` python
import sys

print('running...')

try:
    sys.exit(1)
except SystemExit:
    print('SystemExit exit 1')

print('exited')
```

### sys.exc_info

&emsp;&emsp;`sys.exc_info`用于获取异常信息：

``` python
import sys

try:
    x = int(input("请输入一个被除数："))
    print("30除以", x, "等于", 30 / x)
except:
    print(sys.exc_info())
```

执行结果：

``` python
请输入一个被除数：0
(<class 'ZeroDivisionError'>, ZeroDivisionError('division by zero',), <traceback object at 0x000001FDEB26DC48>)
```

### sys.version

&emsp;&emsp;`sys.version`和`sys.version_info`用于获取`python`的版本信息：

``` python
>>> import sys
>>> sys.version
'3.6.13 |Anaconda, Inc.| (default, Mar 16 2021, 11:37:27) [MSC v.1916 64 bit (AMD64)]'
>>> sys.version_info
sys.version_info(major=3, minor=6, micro=13, releaselevel='final', serial=0)
>>> sys.version_info[0]
3
>>> sys.version_info.major
3
```