---
title: subprocess模块
categories: Python语法
date: 2019-02-09 15:30:45
---
&emsp;&emsp;`subprocess`模块允许我们启动一个新进程，并连接到该进程的管道，从而获取返回值。<!--more-->

### Popen

&emsp;&emsp;`Popen`是`subprocess`的核心，子进程的创建和管理都靠它处理：

``` python
subprocess.Popen(args, stdin=None, stdout=None, stderr=None, close_fds=False, shell=False, cwd=None, env=None, universal_newlines=False)
```

- `args`：可以是字符串或者序列类型，用于指定进程的可执行文件及其参数。
- `stdin`、`stdout`、`stderr`：分别表示程序的标准输入、标准输出、错误句柄。
- `shell`：如果设为`True`，程序将通过`shell`来执行。
- `cwd`：用于设置子进程的当前目录。
- `env`：字典类型，用于指定子进程的环境变量。如果`env`为`None`，子进程的环境变量将从父进程中继承。
- `universal_newlines`：不同操作系统的文本换行符是不一样的，如果设置为`True`，`Python`统一把这些换行符当作`\n`来处理。

### Popen的方法

&emsp;&emsp;`Popen`具有如下方法：

- `poll()`：检查进程是否终止，如果终止，则返回`returncode`，否则返回`None`。
- `wait()`：等待子进程终止。
- `communicate()`：和子进程交互，发送和读取数据。
- `send_signal(singnal)`：发送信号`singnal`到子进程。
- `terminate()`：停止子进程，也就是发送`SIGTERM`信号到子进程。
- `kill()`：杀死子进程，也就是发送`SIGKILL`信号到子进程。

### 代码实例

&emsp;&emsp;代码实例：

``` python
import time
import subprocess

def cmd(command):
    subp = subprocess.Popen(command, shell=True, stdout=subprocess.PIPE,
                            stderr=subprocess.PIPE, encoding="utf-8")
    subp.wait()
    if subp.poll() == 0:
        print(subp.communicate()[0])
    else:
        print("失败")

cmd("node -v")
cmd("exit 1")
```

执行结果：

``` python
v16.4.2

失败
```