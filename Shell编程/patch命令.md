---
title: patch命令
categories: Shell编程
date: 2019-01-20 19:55:04
---
&emsp;&emsp;`patch`指令让用户利用修补文件更新原始文件。倘若一次仅修改一个文件，可直接在指令列中下达指令依序执行；如果配合修补文件的方式，则能一次修补大批文件，这也是`Linux`系统核心的升级方法之一。<!--more-->

``` bash
patch [-bceEflnNRstTuvZ][-B <备份字首字符串>][-d <工作目录>][-D <标示符号>]
      [-F <监别列数>][-g <控制数值>][-i <修补文件>][-o <输出文件>][-p <剥离层级>]
      [-r <拒绝文件>][-V <备份方式>][-Y <备份字首字符串>][-z <备份字尾字符串>]
      [--backup-if -mismatch][--binary][--help][--nobackup-if-mismatch]
      [--verbose][原始文件 <修补文件>]
```

或者如下：

``` bash
patch [-p <剥离层级>] < [修补文件]
```

参数                                                    | 说明
--------------------------------------------------------|-----
`-b`或`--backup`                                        | 备份每一个原始文件
`-B <备份字首字符串>`或`--prefix=<备份字首字符串>`        | 设置文件备份时，附加在文件名称前面的字首字符串，该字符串可以是路径名称
`-d <工作目录>`或`--directory=<工作目录>`                | 设置工作目录
`-D <标示符号>`或`--ifdef=<标示符号>`                    | 用指定的符号把改变的地方标示出来
`-E`或`--remove-empty-files`                            | 若修补过后输出的文件其内容是一片空白，则移除该文件
`-f`或`--force`                                         | 此参数的效果和指定`-t`参数类似，但会假设修补数据的版本为新版本
`-F <监别列数>`或`--fuzz=<监别列数>`                      | 设置监别列数的最大值
`-g <控制数值>`或`--get=<控制数值>`                       | 设置以`RSC`或`SCCS`控制修补作业
`-i <修补文件>`或`--input=<修补文件>`                     | 读取指定的修补文件
`-l`或`--ignore-whitespace`                              | 忽略修补数据与输入数据的跳格、空格字符
`-n`或`--normal`                                         | 把修补数据解译成一般性的差异
`-N`或`--forward`                                        | 忽略修补的数据较原始文件的版本更旧的情况，或该版本的修补数据已使用过
`-o <输出文件>`或`--output=<输出文件>`                    | 设置输出文件的名称，修补过的文件会以该名称存放
`-p <剥离层级>`或`--strip=<剥离层级>`                     | 设置欲剥离几层路径名称
`-f <拒绝文件>`或`--reject-file=<拒绝文件>`               | 设置保存拒绝修补相关信息的文件名称，预设的文件名称为`.rej`
`-R`或`--reverse`                                        | 假设修补数据是由新旧文件交换位置而产生
`-s`、`--quiet`或`--silent`                              | 不显示指令执行过程，除非发生错误
`-t`或`--batch`                                          | 自动略过错误，不询问任何问题
`-T`或`--set-time`                                       | 此参数的效果和指定`-Z`参数类似，但以本地时间为主
`-u`或`--unified`                                        | 把修补数据解译成一致化的差异
`-v`或`--version`                                        | 显示版本信息
`-V <备份方式>`或`--version-control=<备份方式>`            | 用`-b`参数备份目标文件后，备份文件的字尾会被加上一个备份字符串，这个字符串不仅可用`-z`参数变更，当使用`-V`参数指定不同备份方式时，也会产生不同字尾的备份字符串
`-Y <备份字首字符串>`或`--basename-prefix=<备份字首字符串>` | 设置文件备份时，附加在文件基本名称开头的字首字符串
`-z <备份字尾字符串>`或`--suffix=<备份字尾字符串>`          | 此参数的效果和指定`-B`参数类似，差别在于修补作业使用的路径与文件名若为`src/linux/fs/super.c`，加上`backup/`字符串后，文件`super.c`会备份于`/src/linux/fs/backup`目录里
`-Z`或`--set-utc`                                         | 把修补过的文件更改，存取时间设为`UTC`
`--backup-if-mismatch`                                    | 在修补数据不完全吻合，且没有刻意指定要备份文件时，才备份文件
`--binary`                                                | 以二进制模式读写数据，而不通过标准输出设备
`--help`                                                  | 在线帮助
`--nobackup-if-mismatch`                                  | 在修补数据不完全吻合，且没有刻意指定要备份文件时，不要备份文件
`--verbose`                                               | 详细显示指令的执行过程

&emsp;&emsp;使用`patch`指令将文件`testfile1`升级，其升级补丁文件为`testfile.patch`，这两个文件的内容如下：

``` bash
$ cat testfile1  # 查看testfile1的内容
Hello, This is the firstfile!
$ cat testfile2  # 查看testfile2的内容
Hello, This is the second file!
```

在需要修改升级的文件与原文件之间使用指令`diff`比较可以生成补丁文件：

``` bash
$ diff testfile1 testfile2  # 比较两个文件
1c1
<Hello, This is the first file!
---
>Hello, This is the second file!
# 将比较结果保存到tetsfile.patch文件
$ diff testfile1 testfile2 > testfile.patch
$ cat testfile.patch  # 查看补丁包的内容
1c1
<Hello, This is the first file!
---
>Hello, This is the second file!
```

用补丁包升级`testfile1`文件：

``` bash
$ patch -p0 testfile1 testfile.patch
patching file testfile1
$ cat testfile1  # 再次查看testfile1的内容
# testfile1文件被修改为与testfile2一样的内容
Hello, This is the second file!
```