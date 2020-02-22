---
title: REPL交互式解释器
categories: Node.js笔记
date: 2019-02-08 14:05:46
---
&emsp;&emsp;`Node.js`的`REPL`(`Read Eval Print Loop`，交互式解释器)表示一个电脑的环境，类似`Windows`系统的终端或`Unix/Linux shell`。`Node.js`自带了交互式解释器，可以执行以下任务：<!--more-->

- 读取：读取用户输入，解析`javascript`数据结构，并存储在内存中。
- 执行：执行输入的数据结构。
- 打印：输出结果。
- 循环：循环操作以上步骤，直到用户两次按下`Ctrl + c`按钮退出。

&emsp;&emsp;`Node`的交互式解释器可以很好地调试`Javascript`代码。输入以下命令来启动`Node`的终端：

``` bash
$ node
>
```

这时就可以在`>`后输入简单的表达式，并按下回车键来计算结果。
&emsp;&emsp;在`Node.js`的`REPL`命令行窗口中执行简单的数学运算：

``` bash
$ node
> 1 + 4
5
> 5 / 2
2.5
> 3 * 6
18
> 4 - 1
3
> 1 + ( 2 * 3 ) - 4
3
```

### 使用变量

&emsp;&emsp;可以将数据存储在变量中，并在需要的时候使用它。变量声明需要使用`var`关键字，如果没有使用`var`关键字，变量会直接打印出来。使用`var`关键字的变量可以使用`console.log`来输出：

``` bash
$ node
> x = 10
10
> var y = 10
undefined
> x + y
20
> console.log("Hello World")
Hello World
undefined
> console.log("www.runoob.com")
www.runoob.com
undefined
```

### 多行表达式

&emsp;&emsp;`REPL`支持输入多行表达式，这就有点类似`JavaScript`。接下来执行一个`do-while`循环：

``` bash
$ node
> var x = 0
undefined
> do {
... x++;
... console.log("x: " + x);
... } while ( x < 5 );
x: 1
x: 2
x: 3
x: 4
x: 5
undefined
```

`...`是系统自动生成的，回车换行后即可。`Node.js`会自动检测是否为连续的表达式。

### 下划线变量

&emsp;&emsp;可以使用下划线`_`获取上一个表达式的运算结果：

``` bash
$ node
> var x = 10
undefined
> var y = 20
undefined
> x + y
30
> var sum = _
undefined
> console.log(sum)
30
undefined
```

### REPL命令

&emsp;&emsp;命令如下：

- `Ctrl + c`：退出当前终端。
- `Ctrl + c`按下两次：退出`REPL`：

``` bash
$ node
>
(^C again to quit)
>
```

- `Ctrl + d`：退出`REPL`。
- `向上/向下`键：查看输入的历史命令。
- `tab`键：列出当前命令。
- `.help`：列出使用命令。
- `.break`：退出多行表达式。
- `.clear`：退出多行表达式
- `.save filename`：保存当前的`REPL`会话到指定文件
- `.load filename`：载入当前`REPL`会话的文件内容。