---
title: subprocess模块
categories: Python语法
date: 2019-02-09 15:30:45
---
&emsp;&emsp;`subprocess`的目的就是启动一个新的进程并且与之通信。`subprocess`模块中只定义了一个类`Popen`，可以使用`Popen`来创建进程，并与进程进行复杂的交互。<!--more-->

``` python
subprocess.Popen(
    args, bufsize=0, executable=None, stdin=None, stdout=None, stderr=None,
    preexec_fn=None, close_fds=False, shell=False, cwd=None, env=None,
    universal_newlines=False, startupinfo=None, creationflags=0)
```

- `args`：可以是字符串或者序列类型，用于指定进程的可执行文件及其参数。如果是序列类型，第一个元素通常是可执行文件的路径。我们也可以显式地使用`executeable`参数来指定可执行文件的路径。
- `bufsize`：指定缓冲区的大小。`0`表示无缓冲，`1`表示行缓冲，负值表示系统缓冲(全缓冲)，其他数值表示缓冲区大小
- `stdin`、`stdout`、`stderr`：分别表示程序的标准输入、输出、错误句柄。它们可以是`PIPE`、文件描述符或文件对象，也可以设置为`None`，表示从父进程继承。
- `preexec_fn`：只在`Unix`平台下有效，用于指定一个可执行对象(`callable object`)，它将在子进程运行之前被调用。
- `close_sfs`：在`windows`平台下，如果`close_fds`被设置为`True`，则新创建的子进程将不会继承父进程的输入、输出、错误管道。我们不能将`close_fds`设置为`True`的同时，重定向子进程的标准输入、输出与错误(`stdin`、`stdout`和`stderr`)。
- `shell`：如果设为`true`，程序将通过`shell`来执行。
- `cwd`：用于设置子进程的当前目录。
- `env`：字典类型，用于指定子进程的环境变量。如果`env`为`None`，子进程的环境变量将从父进程中继承。
- `universal_newlines`：不同操作系统下，文本的换行符是不一样的。例如`Windows`下用`\r\n`表示换，而`Linux`下用`\n`。如果将此参数设置为`True`，`Python`统一把这些换行符当作`\n`来处理。
- `startupinfo`与`createionflags`：只在`Windows`下有效，它们将被传递给底层的`CreateProcess`函数，用于设置子进程的一些属性，例如主窗口的外观、进程的优先级。

&emsp;&emsp;`subprocess.PIPE`：在创建`Popen`对象时，`subprocess.PIPE`可以初始化`stdin`、`stdout`或`stderr`参数，表示与子进程通信的标准流。
&emsp;&emsp;`subprocess.STDOUT`：创建`Popen`对象时，用于初始化`stderr`参数，表示将错误通过标准输出流输出。
&emsp;&emsp;`subprocess.call(*popenargs, **kwargs)`：运行命令。该函数将一直等待到子进程运行结束，并返回进程的`returncode`。如果子进程不需要进行交互，就可以使用该函数来创建。
&emsp;&emsp;`subprocess.check_call(*popenargs, **kwargs)`：与`subprocess.call`功能一样，只是如果子进程返回的`returncode`不为`0`的话，将触发`CalledProcessError`异常。在异常对象中，包括进程的`returncode`信息。
&emsp;&emsp;`Popen`的方法如下：

- `Popen.poll`：用于检查子进程是否已经结束，设置并返回`returncode`属性。
- `Popen.wait`：等待子进程结束，设置并返回`returncode`属性。
- `Popen.communicate(input=None)`：与子进程进行交互。向`stdin`发送数据，或从`stdout`和`stderr`中读取数据。可选参数`input`指定发送到子进程的参数。`communicate`返回一个元组，即`(stdoutdata, stderrdata)`。注意，如果希望通过进程的`stdin`向其发送数据，在创建`Popen`对象时，参数`stdin`必须被设置为`PIPE`。同样，如果希望从`stdout`和`stderr`获取数据，必须将`stdout`和`stderr`设置为`PIPE`。
- `Popen.send_signal(signal)`：向子进程发送信号。
- `Popen.terminate`：停止(`stop`)子进程。在`Windows`平台下，该方法将调用`Windows`的`TerminateProcess`来结束子进程。
- `Popen.kill`：杀死子进程。
- `Popen.pid`：获取子进程的进程`ID`。
- `Popen.returncode`：获取进程的返回值。如果进程还没有结束，返回`None`。

&emsp;&emsp;在程序中运行其他程序或`shell`：

``` python
subprocess.Popen('shell脚本', shell=True)
subprocess.call('shell脚本', shell=True)
```

两者的区别是前者无阻塞，会和主程序并行运行，后者必须等待命令执行完毕。
&emsp;&emsp;有时候我们需要程序的返回结果，可以这样做：

``` python
>>> s = subprocess.Popen('ls -l', shell=True, stdout=subprocess.PIPE)
>>> s.communicate()
('\xe6\x80\xbb\xe7\x94\xa8\xe9\x87\x8f 152\n
-rw------- 1 limbo limbo   808  7\xe6\x9c\x88  6 17:46 0000-00-00-welcome-to-jekyll.markdown.erb\n
drwx------ 2 limbo limbo  4096  8\xe6\x9c\x88 15 18:43 arg\n
drwx------ 2 limbo limbo  4096  8\xe6\x9c\x88  7 17:37 argv\n
drwxrwxr-x 2 limbo limbo  4096  9\xe6\x9c\x88 10 15:27 c\n
drwxrwxr-x 3 limbo limbo  4096  9\xe6\x9c\x88 11 14:35 d3\n
drwxrwxr-x 3 limbo limbo  4096  9\xe6\x9n', None)
```

它会返回一个元组，即`(stdoutdata, stderrdata)`。`subprocess`还有另一种更简单方法，它会返回`stdout`：

``` python
>>> s = subprocess.check_output('ls -l', shell=True)
>>> s
'\xe6\x80\xbb\xe7\x94\xa8\xe9\x87\x8f 152\n
-rw------- 1 limbo limbo   808  7\xe6\x9c\x88  6 17:46 0000-00-00-welcome-to-jekyll.markdown.erb\n
drwx------ 2 limbo limbo  4096  8\xe6\x9c\x88 15 18:43 arg\n
drwx------ 2 limbo limbo  4096  8\xe6\x9c\x88  7 17:37 argv\n
drwxrwxr-x 2 limbo limbo  4096  9\xe6\x9c\x88 10 15:27 c\n
drwxrwxr-x 3 limbo limbo  4096  9\xe6\x9c\x88 11 14:35 d3\n
drwxrwxr-x 3 limbo limbo  4096  9\xe6\x9n'
```

但是前者可以实现更多的交互(例如`stderr`和`stdin`)：

``` python
import subprocess

child1 = subprocess.Popen(["ls", "-l"], stdout=subprocess.PIPE)
child2 = subprocess.Popen(["wc"], stdin=child1.stdout, stdout=subprocess.PIPE)
out = child2.communicate()
print(out)
```

实际上是这样的过程：

``` python
child1.stdout -> subprocess.PIPE
child2.stdin <- subprocess.PIPE
child2.stdout -> subprocess.PIPE
```

`subprocess.PIPE`实际上为文本流提供一个缓存区，直到`communicate`方法从`PIPE`中读取出数据。