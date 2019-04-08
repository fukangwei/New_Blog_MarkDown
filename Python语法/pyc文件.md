---
title: pyc文件
date: 2018-12-28 08:49:26
categories: Python语法
---
### pyc文件是什么？

&emsp;&emsp;`pyc`是一种二进制文件，是由`py`文件经过编译后生成的文件，是一种`byte code`。`py`文件变成`pyc`文件后，加载的速度有所提高，而且`pyc`是一种跨平台的字节码，是由`python`的虚拟机来执行的，这个是类似于`Java`或者`.NET`的虚拟机的概念。`pyc`的内容是和`python`的版本相关的，不同版本编译后的`pyc`文件是不同的，对于`2.5`编译的`pyc`文件，`2.4`版本的`python`是无法执行的。

### 为什么需要pyc文件？

&emsp;&emsp;这个需求太明显了，因为`py`文件是可以直接看到源码的，如果你是开发商业软件的话，不可能把源码也泄漏出去吧？所以就需要编译为`pyc`后，再发布出去。当然，`pyc`文件也是可以反编译的，根据`python`源码中提供的`opcode`，可以根据`pyc`文件反编译出`py`文件源码。

### 生成单个pyc文件

&emsp;&emsp;`python`提供了内置的类库来实现把`py`文件编译为`pyc`文件，这个模块就是`py_compile`。使用方法如下所示(以下代码需要在另一个源文件中执行)：

``` python
import py_compile
py_compile.compile(r'test.py')
```

`compile`函数原型如下所示：

``` python
compile(file[, cfile[, dfile[, doraise]]])
```

- `file`：需要编译的`py`文件的路径。
- `cfile`：编译后的`pyc`文件名称和路径，默认为直接在`file`文件名后加`c`或者`o`，`o`”表示优化的字节码。
- `dfile`：it is used as the name of the source file in error messages instead of file.
- `doraise`：可以是两个值，即`True`或`False`。如果为`True`，则会引发一个`PyCompileError`，否则如果编译文件出错，则会有一个错误，默认显示在`sys.stderr`中，而不会引发异常。

### 批量生成pyc文件

&emsp;&emsp;一般来说，我们的工程都是在一个目录下的，不会仅仅编译一个`py`文件而已，而是需要把整个文件夹下的`py`文件都编译为`pyc`文件。`python`又提供了另一个模块`compileall`，使用方法如下：

``` python
import compileall
compileall.compile_dir(r'H:/game')
```

这样就把`game`目录以及其子目录下的`py`文件编译为`pyc`文件了。`compile_dir`的函数说明如下所示：

``` python
compile_dir(dir[, maxlevels[, ddir[, force[, rx[, quiet]]]]])
```

- `dir`：需要编译的文件夹位置。
- `maxlevels`：需要递归编译的子目录的层数，默认是`10`层，即会把`10`层子目录中的`py`文件编译为`pyc`。
- `ddir`：it is used as the base path from which the filenames used in error messages will be generated.
- `force`：如果为`True`，则会强制编译为`pyc`，即使现在的`pyc`文件是最新的，还会强制编译一次。`pyc`文件中包含有时间戳，`python`编译器会根据时间来决定是否需要重新生成一次`pyc`文件。
- `rx`：一个正则表达式，比如可以排除掉不想要的目录，或者只有符合条件的目录才进行编译。
- `quiet`：如果为`True`，则编译后，不会在标准输出中，打印出信息。

### 执行pyc文件

&emsp;&emsp;使用终端进入`pyc`文件的目录，然后使用如下命令：

``` bash
python 文件名.pyc
```