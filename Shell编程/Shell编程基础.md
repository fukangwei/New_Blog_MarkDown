---
title: Shell编程基础
categories: Shell编程
abbrlink: 4036ce8c
date: 2019-01-20 06:58:03
---
&emsp;&emsp;`Shell`是一个用`C`语言编写的程序，它是用户使用`Linux`的桥梁。`Shell`既是一种命令语言，又是一种程序设计语言。`shell`是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

### Shell环境

&emsp;&emsp;`Shell`编程跟`java`、`php`编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。`Linux`的`Shell`种类众多，常见的有：

- `Bourne Shell`(`/usr/bin/sh`或`/bin/sh`)
- `Bourne Again Shell`(`/bin/bash`)
- `C Shell`(`/usr/bin/csh`)
- `K Shell`(`/usr/bin/ksh`)
- `Shell for Root`(`/sbin/sh`)

&emsp;&emsp;由于易用和免费，`Bash`在日常工作中被广泛使用，它也是大多数`Linux`系统默认的`Shell`。在一般情况下，人们并不区分`Bourne Shell`和`Bourne Again Shell`，所以`#!/bin/sh`同样也可以改为`#!/bin/bash`。可以使用如下命令来查看用户当前使用的`Shell`类型：

``` bash
echo $SHELL
```

`$SHELL`是一个环境变量，它记录用户所使用的`Shell`类型。

### 第一个shell脚本

&emsp;&emsp;打开文本编辑器，新建一个文件`test.sh`(`sh`代表`shell`)：

``` bash
#!/bin/bash
echo "Hello World!"
```

`#!`是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，也就是使用哪一种`Shell`。`echo`命令用于向窗口输出文本。
&emsp;&emsp;运行`Shell`脚本的方法如下：

``` bash
chmod +x ./test.sh  # 使脚本具有执行权限
./test.sh  # 执行脚本
```

注意一定要写成`./test.sh`，而不是`test.sh`。如果直接写成`test.sh`，`linux`系统会去`PATH`中的目录里寻找有没有叫做`test.sh`的程序。而一般情况下只有`/bin`、`/sbin`、`/usr/bin`、`/usr/sbin`等在`PATH`里，你的当前目录通常不在`PATH`里，所以写成`test.sh`会找不到。要用`./`告诉系统就在当前目录找。

### shell基本语法

&emsp;&emsp;在`shell`中，用户可以使用`>`和`<`来进行输入输出的重定向：

``` bash
command > file  # 将命令的输出结果重定向到一个文件中
```

&emsp;&emsp;`pipe`可以将一个程序的输出送到另一个程序的输入：

``` bash
command1 | command2 [| command3]
```

&emsp;&emsp;在一个命令的后面加上一个`&`，可以让其在后台执行：

``` bash
command &
```

### Shell注释

&emsp;&emsp;以`#`开头的行就是注释，这行代码就会被解释器忽略。`shell`里没有多行注释，只能在每一行之前加一个`#`：

``` bash
##### 用户配置区开始 #####
#
# 这里可以添加脚本描述信息
#
##### 用户配置区结束 #####
```

### Shell注释多行代码

&emsp;&emsp;最简单的方法：

``` bash
:<<BLOCK
注释内容
BLOCK
```

把输入重定义到前面的命令，但`:`是空命令，所以就相当于注释了。如果注释中有反引号的命令就会报错，反引号部分不会被注释掉，例如<code>var=\`ls -l\`</code>就不会被注释掉。
&emsp;&emsp;解决注释中有反引号的问题：

``` bash
# 方法一
:<<BLOCK'
注释内容
'BLOCK

# 方法二
:<<'BLOCK
注释内容
BLOCK'

# 方法三
:<<'
....注释内容
'
```

`BLOCK`为`Here Documents`中的定义符号，名称任意，只要前后匹配就行。