---
title: Shell输入输出重定向
categories: Shell编程
abbrlink: 3a6ee9d0
date: 2019-01-20 09:38:27
---
&emsp;&emsp;大多数`UNIX`系统命令从你的终端接受输入并将所产生的输出发送回到您的终端。一个命令通常从一个叫标准输入的地方读取输入，默认情况下恰好是你的终端。同样，一个命令通常将其输出写入到标准输出，默认情况下也是你的终端。重定向命令列表如下：

命令               | 说明
------------------|------------------
`command > file`  | 将输出重定向到`file`
`command < file`  | 将输入重定向到`file`
`command >> file` | 将输出以追加的方式重定向到`file`
`n > file`        | 将文件描述符为`n`的文件重定向到`file`
`n >> file`       | 将文件描述符为`n`的文件以追加的方式重定向到`file`
`n >& m`          | 将输出文件`m`和`n`合并
`n <& m`          | 将输入文件`m`和`n`合并
`<< tag`          | 将开始标记`tag`和结束标记`tag`之间的内容作为输入

需要注意的是文件描述符`0`通常是标准输入`STDIN`，`1`是标准输出`STDOUT`，`2`是标准错误输出`STDERR`。

### 输出重定向

&emsp;&emsp;重定向一般通过在命令间插入特定的符号来实现：

``` bash
command > file
```

上面这个命令执行`command`，然后将输出的内容存入`file`。注意，任何`file`内的已经存在的内容将被新内容替代。如果要将新内容添加在文件末尾，请使用`>>`操作符。
&emsp;&emsp;`Unix`命令也可以从文件获取输入：

``` bash
command1 < file1
```

本来需要从键盘获取输入的命令会转移到文件读取内容。
&emsp;&emsp;例如我们需要统计`users`文件的行数(`users`的文件行数为`2`)：

``` bash
$ wc -l users
2 users
```

也可以将输入重定向到`users`文件：

``` bash
$ wc -l < users
2
```

上面两个例子的结果不同，第一个例子会输出文件名，第二个不会，因为它仅仅知道从标准输入读取内容：

``` bash
command < infile > outfile
```

同时替换输入和输出，执行`command`，从文件`infile`读取内容，然后将输出写入到`outfile`。

### Here Document

&emsp;&emsp;`Here Document`是`Shell`中的一种特殊的重定向方式，用来将输入重定向到一个交互式`Shell`脚本或程序：

``` bash
command << delimiter
    document
delimiter
```

它的作用是将两个`delimiter`之间的内容`document`作为输入传递给`command`。但是要注意，结尾的`delimiter`一定要顶格写，前面不能有任何字符，后面也不能有任何字符，包括`空格`和`tab`。开始的`delimiter`前后的空格会被忽略掉。
&emsp;&emsp;在命令行中通过`wc -l`命令计算`Here Document`的行数：

``` bash
$ wc -l << EOF
>欢迎来到
>菜鸟教程
>www.runoob.com
>EOF
3  # 输出结果为3行
```

也可以将`Here Document`用在脚本中：

``` bash
#!/bin/bash
​
cat << EOF
欢迎来到
菜鸟教程
www.runoob.com
EOF
```

执行结果：

``` bash
欢迎来到
菜鸟教程
www.runoob.com
```

### /dev/null文件

&emsp;&emsp;如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到`/dev/null`：

``` bash
command > /dev/null
```

`/dev/null`是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是`/dev/null`文件非常有用，将命令的输出重定向到它，会起到`禁止输出`的效果。如果希望屏蔽`stdout`和`stderr`，可以这样写：

``` bash
command > /dev/null 2>&1
```