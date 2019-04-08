---
title: find命令
date: 2019-01-20 20:53:42
tags:
---
&emsp;&emsp;`find`命令如下：

``` bash
find path -option [-print] [-exec -ok command {} \;]
```

- `path`：所查找的目录路径，例如用`.`来表示当前目录，用`/`来表示系统根目录。
- `-print`：将匹配的文件输出到标准输出。
- `-exec`：对匹配的文件执行该参数所给出的`shell`命令。相应命令的形式为`'command' {} \;`，注意`{}`和`\;`之间的空格。
- `-ok`：和`-exec`的作用相同，只不过以一种更为安全的模式来执行该参数所给出的`shell`命令，在执行每一个命令之前，都会给出提示，让用户来确定是否执行。

选项                | 说明
--------------------|-----
`-name filename`    | 查找名为`filename`的文件
`-perm`             | 按执行权限来查找
`-user username`    | 按文件属主来查找
`-group groupname`  | 按组来查找
`-mtime -n/+n`      | 按文件更改时间来查找文件，`-n`指`n`天以内，`+n`指`n`天以前
`-atime -n/+n`      | 按文件访问时间来查找文件，`-n`指`n`天以内，`+n`指`n`天以前
`-ctime -n/+n`      | 按文件创建时间来查找文件，`-n`指`n`天以内，`+n`指`n`天以前
`-nogroup`          | 查无有效属组的文件，即文件的属组在`/etc/groups`中不存在
`-nouser`           | 查无有效属主的文件，即文件的属主在`/etc/passwd`中不存在
`-newer f1 !f2`     | 查更改时间比`f1`新但比`f2`旧的文件
`-type b/d/c/p/l/f` | 查是块设备、目录、字符设备、管道、符号链接、普通文件。
`-size n`           | 查长度为`n`块(`1`块等于`512`字节)的文件
`-depth`            | 使查找在进入子目录前先行查找完本目录
`-fstype`           | 查位于某一类型文件系统中的文件，这些文件系统类型通常可在`/etc/fstab`中找到
`-mount`            | 查文件时不跨越文件系统`mount`点
`-follow`           | 如果遇到符号链接文件，就跟踪链接所指的文件
`-cpio`             | 对匹配的文件使用`cpio`命令，将它们备份到磁带设备中
`-prune`            | 忽略某个目录

示例如下：

- `find ~ -name "*.txt" -print`：在`$HOME`中查找`.txt`文件并显示。
- `find . -name "[A-Z]*" -print`：查以大写字母开头的文件。
- `find /etc -name "host*" -print`：查以`host`开头的文件。
- `find . -name "[a-z][a-z][0–9][0–9].txt" -print`：查以两个小写字母和两个数字开头的`txt`文件。
- `find . -perm -007 -exec ls -l {} \;`：查所有用户都可读写执行的文件，同`-perm 777`。
- `find . -size +1000000c -print`：查长度大于`1MB`的文件。
- `find /etc -name "passwd*" -exec grep "cnscn" {} \;`：看是否存在`cnscn`用户。
- `find -name april*`：在当前目录下查找以`april`开始的文件。
- `find /mnt -name tom.txt -ftype vfat`：在`/mnt`下查找名称为`tom.txt`，且文件系统类型为`vfat`的文件。
- `find /mnt -name t.txt ! -ftype vfat`：在`/mnt`下查找名称为`tom.txt`，且文件系统类型不为`vfat`的文件。
- `find /tmp -name wa* -type l`：在`/tmp`下查找名为`wa`开头，且类型为符号链接的文件。
- `find /home -mtime -2`：在`/home`下查最近两天内改动过的文件。
- `find /home -atime -1`：查`1`天之内被存取过的文件。
- `find /home -mmin +60`：在`/home`下查`60`分钟前改动过的文件。
- `find /home -amin +30`：查最近`30`分钟前被存取过的文件。
- `find /home -newer tmp.txt`：在`/home`下查更新时间比`tmp.txt`近的文件或目录。
- `find /home -anewer tmp.txt`：在`/home`下查存取时间比`tmp.txt`近的文件或目录。
- `find /home -used -2`：列出文件或目录被改动过之后，在`2`日内被存取过的文件或目录。
- `find /home -user cnscn`：列出`/home`目录内属于用户`cnscn`的文件或目录。
- `find /home -uid +501`：列出`/home`目录内用户的识别码大于`501`的文件或目录。
- `find /home -group cnscn`：列出`/home`内组为`cnscn`的文件或目录。
- `find /home -gid 501`：列出`/home`内组`id`为`501`的文件或目录。
- `find /home -nouser`：列出`/home`内不属于本地用户的文件或目录。
- `find /home -nogroup`：列出`/home`内不属于本地组的文件或目录。
- `find /home -name tmp.txt -maxdepth 4`：列出`/home`内的`tmp.txt`，查时深度最多为`3`层。
- `find /home -name tmp.txt -mindepth 3`：从第`2`层开始查。
- `find /home -empty`：查找大小为`0`的文件或空目录。
- `find /home -size +512k`：查找大于`512k`的文件。
- `find /home -size -512k`：查找小于`512k`的文件。
- `find /home -links +2`：查找硬连接数大于`2`的文件或目录。
- `find /home -perm 0700`：查找权限为`700`的文件或目录。
- `find / -amin -10`：查找在系统中最后10分钟访问的文件。
- `find / -mmin -5`：查找在系统中最后`5`分钟里修改过的文件。
- `find / -mtime -1`：查找在系统中最后`24`小时里修改过的文件。