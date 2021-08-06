---
title: gcc教程
categories: 嵌入式笔记
date: 2019-01-18 09:42:36
---
### gcc(或g++)的用法

&emsp;&emsp;以`main.c`为例说明`gcc`的用法：

- `gcc -o main main.c`：编译器将执行预处理、编译、汇编、链接操作，直接生成可执行代码。`-o`用于指定输出的文件，输出文件名为`main`。
- `gcc -E -o main.i main.c`：`-E`将对`main.c`进行预处理，生成`main.i`。预处理就是插入`include`头文件，扩展`define`宏。
- `gcc -g -o main main.c`：`-g`加入`GDB`使用的调试信息，便于程序调试。
- `gcc -Wall -o main main.c`：`-Wall`打开了所有需要注意的警告信息，例如声明后却没有使用变量。
- `gcc -Ox -o main main.c`：`-Ox`是优化选项，`x`的值为`空`、`0`、`1`、`2`、`3`。
- `gcc -I /home/include -o main main.c`：`-I dirname`将`dirname`加入到头文件搜索目录列表中。
- `gcc -L /home/lib -o main main.c`：`-L dirname`将`dirname`加入到库文件搜索目录列表中。

### gcc(或g++)常用选项

&emsp;&emsp;编程语言标准相关：

- `-ansi`：`ANSI`标准。
- `-std=c99`：`C99`标准。
- `-std=c++11`：支持`C++11`标准。

### gcc的-D选项

&emsp;&emsp;`gcc`的`-D`选项可以定义宏：

- `-Dname`或`-D name`：定义宏`name`，默认定义内容为`1`。
- `-Dname=defn`或`-D name=defn`：定义宏`name`，并且内容为`defn`。

``` cpp
#include <stdio.h>

int main() {
#ifdef HELLO
    printf ( "HELLO defined!\n" );
    printf ( "HELLO = %d\n", HELLO );
#else
    printf ( "HELLO not define!\n" );
#endif
    return 0;
}
```

&emsp;&emsp;执行如下命令：

``` bash
$ gcc main.c -o main
$ ./main
HELLO not define!
```

&emsp;&emsp;执行如下命令：

``` bash
$ gcc -DHELLO main.c -o main
$ ./main
HELLO defined!
HELLO = 1
```

&emsp;&emsp;执行如下命令：

``` bash
$ gcc -DHELLO=36 main.c -o main
$ ./main
HELLO defined!
HELLO = 36
```