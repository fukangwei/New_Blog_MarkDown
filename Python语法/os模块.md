---
title: os模块
categories: Python语法
date: 2019-01-11 09:54:42
---
&emsp;&emsp;`os`模块提供了很多方法用来处理文件和目录。<!--more-->

### os.chdir

&emsp;&emsp;`os.chdir`用于改变当前工作目录到指定的路径。

``` python
os.chdir(path)
```

参数`path`是要切换到的新路径。如果该路径允许访问，则返回`True`，否则返回`False`。

``` python
import os

path = "/tmp"
retval = os.getcwd()
print("当前工作目录为 %s" % retval)  # 输出“当前工作目录为 /www”
os.chdir(path)
retval = os.getcwd()
print("目录修改成功 %s" % retval)  # 输出“目录修改成功 /tmp”
```

### os.close

&emsp;&emsp;`os.close`关闭指定的文件描述符`fd`。

``` python
os.close(fd)
```

该方法没有返回值。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
os.write(fd, b"This is test")  # 写入字符串
os.close(fd)
```

### os.closerange

&emsp;&emsp;`os.closerange`方法用于关闭所有文件描述符`fd`，从`fd_low`(包含)到`fd_high`(不包含)，错误会忽略。

``` python
os.closerange(fd_low, fd_high)
```

- `fd_low`：最小文件描述符。
- `fd_high`：最大文件描述符。

该方法类似于：

``` python
for fd in range(fd_low, fd_high):
    try:
        os.close(fd)
    except OSError:
        pass
```

该方法没有返回值。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
os.write(fd, b"This is test")
os.closerange(fd, fd)
```

### os.dup

&emsp;&emsp;`os.dup`用于复制文件描述符`fd`。

``` python
os.dup(fd)
```

参数`fd`是文件描述符。该方法返回复制的文件描述符。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
d_fd = os.dup(fd)  # 复制文件描述符
os.write(d_fd, b"This is test")  # 使用复制的文件描述符写入文件
os.closerange(fd, d_fd)
```

### os.dup2

&emsp;&emsp;`os.dup2`用于将一个文件描述符`fd`复制到`fd2`。

``` python
os.dup2(fd, fd2)
```

- `fd`：要被复制的文件描述符。
- `fd2`：复制的文件描述符。

该方法没有返回值。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
os.write(fd, "This is test".encode())
fd2 = 1000  # 文件描述符为1000
os.dup2(fd, fd2)
# 在新的文件描述符上插入数据
os.lseek(fd2, 0, 0)
str = os.read(fd2, 100)
print("读取的字符串是:", str.decode())  # 输出“读取的字符串是: This is test”
os.close(fd)
```

### os.fchdir

&emsp;&emsp;`os.fchdir`通过文件描述符改变当前工作目录。

``` python
os.fchdir(fd)
```

参数`fd`是文件描述符。该方法没有返回值。

``` python
import os

os.chdir("/var/www/html")  # 首先到目录“/var/www/html”
print("当前工作目录为: %s" % os.getcwd())  # 输出“当前工作目录为: /var/www/html”
fd = os.open("/tmp", os.O_RDONLY)  # 打开新目录“/tmp”
os.fchdir(fd)  # 使用os.fchdir方法修改到新目录
print("当前工作目录为: %s" % os.getcwd())  # 输出“当前工作目录为: /tmp”
os.close(fd)
```

### os.fdatasync

&emsp;&emsp;`os.fdatasync`强制将文件写入磁盘，但是不强制更新文件的状态信息。如果你需要刷新缓冲区，可以使用该方法。

``` python
os.fdatasync(fd)
```

参数`fd`是文件描述符。该方法没有返回值。

``` python
import os, sys

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
os.write(fd, "This is test".encode())  # 写入字符串
os.fdatasync(fd)  # 使用fdatasync方法
os.lseek(fd, 0, 0)
str = os.read(fd, 100)
print("读取的字符是:", str.decode())  # 输出“读取的字符是: This is test”
os.close(fd)
```

### os.fdopen

&emsp;&emsp;`os.fdopen`通过文件描述符`fd`创建一个文件对象，并返回这个文件对象。

``` python
os.fdopen(fd, [, mode [, bufsize]])
```

- `fd`：文件描述符。
- `mode`：可选项。和`Python`内建的`open`函数一样，`mode`参数可以指定为`r`、`w`、`a`、`r+`、`w+`、`a+`、`b`等。
- `bufsize`：可选项，指定返回的文件对象是否带缓冲。如果这个参数没有指定，则使用系统默认的缓冲设定。

1. `bufsize = 0`表示没有带缓冲。
2. `bufsize = 1`表示该文件对象是行缓冲的。
3. `bufsize = 正数`表示使用一个指定大小的缓冲，单位为`byte`，但是这个大小不是精确的。
4. `bufsize = 负数`表示使用一个系统默认大小的缓冲。对于`tty`等字符设备，一般是行缓冲；而对于其他文件，一般是全缓冲。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
fo = os.fdopen(fd, "w+")  # 获取以上文件的对象
print("Current I/O pointer position: %d" % fo.tell())  # 获取当前位置
fo.write("Python is a great language.\nYeah its great!")  # 写入字符串
fo.seek(0, 0)
str = os.read(fd, 100)  # 读取内容
print("Read String is:", str.decode())
print("Current I/O pointer position: %d" % fo.tell())  # 获取当前位置
os.close(fd)
```

执行结果：

``` python
Current I/O pointer position: 0
Read String is: Python is a great language.
Yeah its great!
Current I/O pointer position: 44
```

### os.fstat

&emsp;&emsp;`os.fstat`返回文件描述符`fd`的状态，类似于`stat`。`fstat`返回的结构如下：

结构成员      | 说明
-------------|-----
`st_dev`     | 设备信息
`st_ino`     | 文件的`i_node`值
`st_mode`    | 文件信息的掩码，包含了文件的权限信息、类型信息(是普通文件还是管道文件，或者其他类型的文件)
`st_nlink`   | 硬连接数
`st_uid`     | 用户`ID`
`st_gid`     | 用户组`ID`
`st_rdev`    | 设备`ID`
`st_size`    | 文件大小，以`byte`为单位
`st_blksize` | 系统`I/O`块大小
`st_blocks`  | 文件的是由多少个`512 byte`块构成的
`st_atime`   | 文件最近的访问时间
`st_mtime`   | 文件最近的修改时间
`st_ctime`   | 文件状态信息的修改时间(不是文件内容的修改时间)

&emsp;&emsp;`os.fstat`语法如下：

``` python
os.fstat(fd)
```

实例如下：

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
info = os.fstat(fd)  # 获取元组
print("文件信息:", info)
print("文件UID: %d" % info.st_uid)  # 获取文件uid
print("文件GID: %d" % info.st_gid)  # 获取文件gid
os.close(fd)
```

执行结果：

``` python
文件信息: os.stat_result(st_mode=33206, st_ino=39406496739494228, \
    st_dev=853940426, st_nlink=1, st_uid=0, st_gid=0, \
    st_size=44, st_atime=1509690521, st_mtime=1509690819, st_ctime=1509682525)
文件UID: 0
文件GID: 0
```

### os.fsync

&emsp;&emsp;`os.fsync`方法强制将文件描述符为`fd`的文件写入硬盘。

``` python
os.fsync(fd)
```

该方法没有返回值。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
os.write(fd, "This is test".encode())  # 写入字符串
os.fsync(fd)  # 使用fsync方法
os.lseek(fd, 0, 0)
str = os.read(fd, 100)
print("读取的字符串为:", str.decode())  # 输出“读取的字符串为: This is test”
os.close(fd)
```

### os.ftruncate

&emsp;&emsp;`os.ftruncate`裁剪文件描述符`fd`对应的文件。

``` python
os.ftruncate(fd, length)
```

- `fd`：文件的描述符。
- `length`：需要裁剪的大小，其值不能超过文件大小。

该方法没有返回值。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
os.write(fd, "This is test - This is test".encode())  # 写入字符串
os.ftruncate(fd, 10)  # 使用ftruncate方法
os.lseek(fd, 0, 0)
str = os.read(fd, 100)
print("读取的字符串是:", str.decode())  # 输出“读取的字符串是: This is te”
os.close(fd)
```

### os.getcwd

&emsp;&emsp;`os.getcwd`返回当前工作目录。

``` python
os.getcwd()
```

实例如下：

``` python
import os

os.chdir("/var/www/html")  # 切换到“/var/www/html”目录
print("当前工作目录: %s" % os.getcwd())  # 输出“当前工作目录: /var/www/html”
fd = os.open("/tmp", os.O_RDONLY)  # 打开“/tmp”
os.fchdir(fd)  # 使用os.fchdir方法修改目录
print("当前工作目录: %s" % os.getcwd())  # 输出“当前工作目录: /tmp”
os.close(fd)
```

### os.listdir

&emsp;&emsp;`os.listdir`返回指定路径包含的文件和文件夹列表，这个列表是按照字母顺序排列，并且不包括`.`和`..`。

``` python
os.listdir(path)
```

参数`path`是指定路径。

``` python
import os

path = "./"
dirs = os.listdir(path)

for file in dirs:  # 输出所有文件和文件夹
    print(file)
```

执行结果：

``` python
.idea
foo.txt
hello.py
```

### os.lseek

&emsp;&emsp;`os.lseek`设置文件描述符`fd`的当前位置。

``` python
os.lseek(fd, pos, how)
```

- `fd`：文件描述符。
- `pos`：这是相对于参数`how`的位置。
- `how`：文件内部的参考位置：

1. `SEEK_SET`(或者`0`)设置从文件开始的计算的`pos`。
2. `SEEK_CUR`(或者`1`)从当前位置计算。
3. `SEEK_END`(或者`2`)从文件尾部开始。

该方法没有返回值。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
os.write(fd, "This is test".encode())
os.fsync(fd)
os.lseek(fd, 0, os.SEEK_SET)
str = os.read(fd, 100)
print("Read String is:", str.decode())  # 输出“Read String is: This is test”
os.close(fd)
```

### os.mkdir

&emsp;&emsp;`os.mkdir`使用数字权限创建目录，默认的模式为`0777`(八进制)。

``` python
os.mkdir(path [, mode])
```

- `path`：创建的目录。
- `mode`：为目录设置的权限。

该方法没有返回值。

``` python
import os

path = "./tmp/monthly"
os.mkdir(path, 0o755)
```

### os.makedirs

&emsp;&emsp;`os.makedirs`递归地创建目录。

``` python
os.makedirs(path, mode=0o777)
```

- `path`：需要递归创建的目录。
- `mode`：权限模式。

该方法没有返回值。

``` python
import os

path = "./tmp/daily"  # 创建的目录
os.makedirs(path, 0o755)
```

### os.open

&emsp;&emsp;`os.open`打开一个文件，并且设置打开选项。参数`mode`是可选的，默认为`0777`。

``` python
os.open(file, flags [, mode])
```

- `file`：要打开的文件。
- `flags`：该参数有以下选项，多个则使用`|`隔开：

选项            | 说明
----------------|-----
`os.O_RDONLY`   | 以只读的方式打开
`os.O_WRONLY`   | 以只写的方式打开
`os.O_RDWR`     | 以读写的方式打开
`os.O_NONBLOCK` | 打开时不阻塞
`os.O_APPEND`   | 以追加的方式打开
`os.O_CREAT`    | 创建并打开一个新文件
`os.O_TRUNC`    | 打开一个文件，并截断它的长度为零(必须有写权限)
`os.O_EXCL`     | 如果指定的文件存在，就返回错误
`os.O_SHLOCK`   | 自动获取共享锁
`os.O_EXLOCK`   | 自动获取独立锁
`os.O_DIRECT`   | 消除或减少缓存效果
`os.O_FSYNC`    | 同步写入
`os.O_NOFOLLOW` | 不追踪软链接

该方法返回新打开的文件描述符。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR | os.O_CREAT)
os.write(fd, "This is test".encode())  # 写入字符串
os.close(fd)
```

### os.read

&emsp;&emsp;`os.read`从文件描述符`fd`中读取最多`n`个字节，并返回包含读取字节的字符串。如果文件指针达到文件尾，则返回一个空字符串。

``` python
os.read(fd, n)
```

参数`fd`是文件描述符，`n`是读取的字节。

``` python
import os

fd = os.open("foo.txt", os.O_RDWR)
ret = os.read(fd, 12)
print(ret.decode())  # 输出“This is test”
os.close(fd)
```

### os.remove

&emsp;&emsp;`os.remove`删除指定路径的文件。如果指定路径是一个目录，将抛出`OSError`。

``` python
os.remove(path)
```

参数`path`是文件路径。该方法没有返回值。

``` python
import os

print("目录为: %s" % os.listdir(os.getcwd()))
os.remove("foo.txt")  # 移除
print("移除后: %s" % os.listdir(os.getcwd()))
```

执行结果：

``` python
目录为: ['.idea', 'foo.txt', 'hello.py']
移除后: ['.idea', 'hello.py']
```

### os.removedirs

&emsp;&emsp;`os.removedirs`递归删除目录。

``` python
os.removedirs(path)
```

参数`path`是目录路径。该方法没有返回值。

``` python
import os

print("目录为: %s" % os.listdir(os.getcwd()))
os.removedirs("./test")
print("移除后目录为: %s" % os.listdir(os.getcwd()))
```

执行结果：

``` python
目录为: [ 'a1.txt','resume.doc','a3.py','test' ]
移除后目录为: [ 'a1.txt','resume.doc','a3.py' ]
```

### os.rename

&emsp;&emsp;`os.rename`重命名文件或目录，从`src`到`dst`。如果`dst`是一个存在的目录，将抛出`OSError`。

``` python
os.rename(src, dst)
```

参数`src`是要修改的目录名，`dst`是修改后的目录名。该方法没有返回值。

``` python
import os

print("目录为: %s" % os.listdir(os.getcwd()))
os.rename("test", "test2")  # 重命名
print("重命名成功。")
print("目录为: %s" % os.listdir(os.getcwd()))
```

执行结果：

``` python
目录为: [ 'a1.txt','resume.doc','a3.py','test' ]
重命名成功。
目录为: [ 'a1.txt','resume.doc','a3.py','test2' ]
```

### os.renames

&emsp;&emsp;`os.renames`递归地重命名目录或文件。

``` python
os.renames(old, new)
```

- `old`：要重命名的目录
- `new`：文件或目录的新名字，甚至可以是包含在目录中的文件，或者完整的目录树。

该方法没有返回值。

``` python
import os

print("当前目录为: %s" % os.getcwd())
print("目录为: %s" % os.listdir(os.getcwd()))
os.renames("aa1.txt", "newdir/aanew.txt")  # 重命名为“aa1.txt”
print("重命名成功。")
print("目录为: %s" % os.listdir(os.getcwd()))
```

执行结果：

``` python
当前目录为: /tmp
目录为: [ 'a1.txt','resume.doc','a3.py','aa1.txt','Administrator','amrood.admin' ]
重命名成功。
目录为:[ 'a1.txt','resume.doc','a3.py','Administrator','amrood.admin' ]
```

### os.rmdir

&emsp;&emsp;`os.rmdir`删除指定路径的目录，条件是这文件夹为空的，否则抛出`OSError`。

``` python
os.rmdir(path)
```

参数`path`是目录路径。该方法没有返回值。

``` python
import os

print("目录为: %s" % os.listdir(os.getcwd()))
os.rmdir("mydir")  # 删除路径
print("目录为: %s" % os.listdir(os.getcwd()))
```

执行结果：

``` python
目录为: [ 'a1.txt','resume.doc','a3.py','mydir' ]
目录为: [ 'a1.txt','resume.doc','a3.py' ]
```

### os.unlink

&emsp;&emsp;`os.unlink`删除文件，如果文件是一个目录，则返回一个错误。

``` python
os.unlink(path)
```

参数`path`是文件路径。该方法没有返回值。

``` python
import os

print("目录为: %s" % os.listdir(os.getcwd()))
os.unlink("aa.txt")
print("删除后的目录为: %s" % os.listdir(os.getcwd()))
```

执行结果：

``` python
目录为: [ 'a1.txt','aa.txt','resume.doc' ]
删除后的目录为: [ 'a1.txt','resume.doc' ]
```

### os.walk

&emsp;&emsp;`os.walk`通过在目录树中游走，输出目录中的文件名(包含设置的根目录`top`)。该函数将产生`3`元组`(dirpath, dirnames, filenames)`。

``` python
os.walk(top[, topdown=True [, onerror=None [, followlinks=False]]])
```

- `top`：设置的根目录。
- `topdown`：可选项，如果为`True`或者没有指定，一个目录的`3`元组将比它的任何子文件夹的`3`元组先产生(即目录`自上而下`)；如果`topdown`为`False`，一个目录的`3`元组将比它的任何子文件夹的`3`元组后产生(即目录`自下而上`)。
- `onerror`：可选项，它是一个函数，一般不设置。
- `followlinks`：如果设置为`True`，则通过软链接访问目录。

该方法没有返回值。

``` python
import os

for root, dirs, files in os.walk(".", topdown=False):
    for name in files:
        print(os.path.join(root, name))

    for name in dirs:
        print(os.path.join(root, name))
```

执行结果：

``` python
.\.idea\scopes\scope_settings.xml
.\.idea\.name
.\.idea\encodings.xml
.\.idea\misc.xml
.\.idea\modules.xml
.\.idea\untitled1.iml
.\.idea\vcs.xml
.\.idea\workspace.xml
.\.idea\scopes
.\hello.py
.\.idea
```

### os.write

&emsp;&emsp;`os.write`写入字符串到文件描述符`fd`中，返回实际写入的字符串长度。

``` python
os.write(fd, str)
```

参数`fd`是文件描述符，`str`是写入的字符串。

``` python
import os

fd = os.open("f1.txt", os.O_RDWR | os.O_CREAT)
str = "This is runoob.com site"
ret = os.write(fd, str.encode())
print("写入的位数为:", ret)  # 输出“写入的位数为: 23”
os.close(fd)
```

### os.urandom

&emsp;&emsp;随机产生`n`个字节(`0-255`)的字符串，可以作为随机加密`key`使用：

``` python
>>> import os
>>> os.urandom(2)
b'5\xcb'
>>> os.urandom(2)
b'2\\'
>>> os.urandom(2)
b'\xbe2'
```


---

### 环境变量

&emsp;&emsp;获取所有环境变量的名字：

``` python
import os

for key in os.environ.keys():
    print(key)
```

&emsp;&emsp;获取指定名字的环境变量对应的值：

``` python
import os

# 参数“path”可以换成任意存在的环境变量名。如果不存在，则输出None
dir = os.environ.get('path')
print(dir)
```

&emsp;&emsp;设置环境变量(注意，当前设置的环境变量只是在本程序中生效，并不是永久更新)：

``` python
import os

dir = "D:\LearnTool"
os.environ['datapath'] = dir
print(os.environ.get('datapath'))
```


---

### os模块中的常量

&emsp;&emsp;`os`模块中具有如下常量：

- `os.curdir`：指代当前目录(`.`)。
- `os.pardir`：指代上一级目录(`..`)。
- `os.sep`：输出操作系统特定的路径分隔符(`Win`下为`\\`，`Linux`下为`/`)。
- `os.linesep`：当前平台使用的行终止符(`Win`下为`\r\n`，`Linux`下为`\n`)。
- `os.name`：指代当前使用的操作系统(包括`posix`、`nt`、`mac`、`java`)。

---

### os.path模块

&emsp;&emsp;相关函数如下：

- `os.path.abspath(path)`：返回绝对路径。
- `os.path.basename(path)`：返回文件名。
- `os.path.commonprefix(list)`：返回`list`(多个路径)中，所有`path`共有的最长路径。
- `os.path.dirname(path)`：返回文件路径。
- `os.path.exists(path)`：路径存在则返回`True`，路径损坏返回`False`。
- `os.path.expanduser(path)`：把`path`中包含的`~`转换成用户目录。
- `os.path.expandvars(path)`：根据环境变量的值替换`path`中包含的`$name`和`${name}`。
- `os.path.getatime(path)`：返回最后一次进入此`path`的时间。
- `os.path.getmtime(path)`：返回在此`path`下最后一次修改的时间。
- `os.path.getsize(path)`：返回文件大小，如果文件不存在，就返回错误。
- `os.path.isabs(path)`：判断是否为绝对路径。
- `os.path.isfile(path)`：判断路径是否为文件。
- `os.path.isdir(path)`：判断路径是否为目录。
- `os.path.islink(path)`：判断路径是否为链接。
- `os.path.ismount(path)`：判断路径是否为挂载点。
- `os.path.join(path1 [, path2 [, ...]])`：把目录和文件名合成一个路径。
- `os.path.normcase(path)`：转换`path`的大小写和斜杠。
- `os.path.normpath(path)`：规范`path`字符串形式。
- `os.path.realpath(path)`：返回`path`的真实路径。
- `os.path.relpath(path [, start])`：从`start`开始计算相对路径。
- `os.path.samefile(path1, path2)`：判断目录或文件是否相同。
- `os.path.sameopenfile(fp1, fp2)`：判断`fp1`和`fp2`是否指向同一文件
- `os.path.samestat(stat1, stat2)`：判断`stat1`和`stat2`是否指向同一个文件。
- `os.path.split(path)`：把路径分割成`dirname`和`basename`，返回一个元组。
- `os.path.splitdrive(path)`：一般用在`Windows`下，返回驱动器名和路径组成的元组。
- `os.path.splitext(path)`：分割路径，返回路径名和文件扩展名的元组。
- `os.path.splitunc(path)`：把路径分割为加载点与文件。