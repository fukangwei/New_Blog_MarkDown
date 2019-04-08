---
title: shutil模块
date: 2018-12-27 19:21:58
categories: Python语法
---
&emsp;&emsp;`shutil`是高级的文件、文件夹、压缩包处理模块。

### copyfileobj

&emsp;&emsp;函数原型如下：

``` python
shutil.copyfileobj(fsrc, fdst[, length])
```

用于将文件内容拷贝到另一个文件中。

``` python
import shutil
shutil.copyfileobj(open('hello.py', 'r'), open('hello.txt', 'w'))
```

### copyfile

&emsp;&emsp;函数原型如下所示：

``` python
shutil.copyfile(src, dst)
```

用于拷贝文件。示例如下：

``` python
shutil.copyfile('f1.log', 'f2.log')
```

### copymode

&emsp;&emsp;函数原型如下所示：

``` python
shutil.copymode(src, dst)
```

仅拷贝权限，内容、组、用户均不变。示例如下：

``` python
shutil.copymode('f1.log', 'f2.log')
```

### copystat

&emsp;&emsp;函数原型如下所示：

``` python
shutil.copystat(src, dst)
```

仅拷贝状态的信息，包括`mode bits`、`atime`、`mtime`和`flags`。示例如下：

``` python
shutil.copystat('f1.log', 'f2.log')
```

### copy

&emsp;&emsp;函数原型如下所示：

``` python
shutil.copy(src, dst)
```

拷贝文件和权限。示例如下：

``` python
shutil.copy('f1.log', 'f2.log')
```

### copy2

&emsp;&emsp;函数原型如下所示：

``` python
shutil.copy2(src, dst)
```

拷贝文件和状态信息。示例如下：

``` python
shutil.copy2('f1.log', 'f2.log')
```

### copytree和ignore_patterns

&emsp;&emsp;函数原型如下所示：

``` python
shutil.ignore_patterns(*patterns)
shutil.copytree(src, dst, symlinks=False, ignore=None)
```

递归地去拷贝文件夹。示例如下：

``` python
shutil.copytree('folder1', 'folder2', ignore=shutil.ignore_patterns('*.pyc', 'tmp*'))
shutil.copytree('f1', 'f2', symlinks=True, ignore=shutil.ignore_patterns('*.pyc', 'tmp*'))
```

### rmtree

&emsp;&emsp;函数原型如下所示：

``` python
shutil.rmtree(path[, ignore_errors[, onerror]])
```

递归地删除文件。示例如下：

``` python
shutil.rmtree('folder1')
```

### move

&emsp;&emsp;函数原型如下所示：

``` python
shutil.move(src, dst)
```

递归地移动文件，类似于`mv`命令，其实就是重命名。示例如下：

``` python
shutil.move('folder1', 'folder3')
```

### make_archive

&emsp;&emsp;函数原型如下所示：

``` python
shutil.make_archive(base_name, format, root_dir, owner, group, logger)
```

创建压缩包并返回文件路径，例如`zip`和`tar`。

- `base_name`：压缩包的文件名，也可以是压缩包的路径。只是文件名时，则保存至当前目录，否则保存至指定路径。
- `format`：压缩包种类，主要有`zip`、`tar`、`bztar`和`gztar`。
- `root_dir`：要压缩的文件夹路径(默认当前目录)。
- `owner`：用户，默认当前用户。
- `group`：组，默认当前组。
- `logger`：用于记录日志，通常是`logging.Logger`对象。

示例如下所示：

``` python
import shutil
​
# 将“/Users/wupeiqi/Downloads/test”下的文件打包，放置于当前程序目录
ret = shutil.make_archive("wwwwwwwwww", 'gztar', root_dir='/Users/wupeiqi/Downloads/test')
# 将“/Users/wupeiqi/Downloads/test”下的文件打包，放置于“/Users/wupeiqi/”目录
ret = shutil.make_archive("/Users/wupeiqi/wwwwwwwwww", 'gztar', root_dir='/Users/wupeiqi/Downloads/test')
```