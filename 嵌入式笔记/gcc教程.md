---
title: gcc教程
categories: 嵌入式笔记
date: 2019-01-18 09:42:36
---
### gcc(或g++)的用法

&emsp;&emsp;以文件`example.c`为例说明`gcc`的用法：

- `gcc -o example example.c`：不加`-c`、`-S`、`-E`参数，编译器将执行预处理、编译、汇编、连接操作直接生成可执行代码。`-o`参数用于指定输出的文件，输出文件名为`example`。如果不指定输出文件，则默认输出`a.out`。
- `gcc -c -o example.o example.c`：`-c`参数将对源程序`example.c`进行预处理、编译、汇编操作，生成`example.o`文件。去掉指定输出选项`-o example.o`自动输出为`example.o`，所以在这里`-o`加和不加都可以。
- `gcc -S -o example.s example.c`：`-S`参数将对源程序`example.c`进行预处理、编译，生成`example.s`文件。`-o`选项同上。
- `gcc -E -o example.i example.c`：`-E`参数将对源程序`example.c`进行预处理，生成`example.i`文件(不同版本不一样，有的将预处理后的内容打印到屏幕上)。就是将`include`、`define`等进行文件插入及宏扩展等操作。
- `gcc -v -o example example.c`：加上`-v`参数，显示编译时的详细信息、编译器的版本、编译过程等。
- `gcc -g -o example example.c`：`-g`选项，加入`GDB`能够使用的调试信息，使用`GDB`调试时比较方便。
- `gcc -Wall -o example example.c`：`-Wall`选项打开了所有需要注意的警告信息，像是在声明之前就使用的函数，声明后却没有使用的变量等。
- `gcc -Ox -o example example.c`：`-Ox`使用优化选项，`X`的值为`空`、`0`、`1`、`2`、`3`。`0`为不优化，优化的目的是减少代码空间和提高执行效率等，但相应的编译过程时间将较长并占用较大的内存空间。
- `gcc -I /home/include -o example example.c`：`-I dirname`将`dirname`所指出的目录加入到程序头文件目录列表中。如果在预设系统及当前目录中没有找到需要的文件，就到指定的`dirname`目录中去寻找。
- `gcc -L /home/lib -o example example.c`：`-L dirname`将`dirname`所指出的目录加入到库文件的目录列表中。在默认状态下，连接程序`ld`在系统的预设路径中(如`/usr/lib`)寻找所需要的库文件。这个选项告诉连接程序，首先到`-L`指定的目录中去寻找，然后再到系统预设路径中寻找。
- `gcc –static -o libexample.a example.c`：编译静态链接库文件，其库文件名为`example.a`。

### gcc(或g++)常用的选项

&emsp;&emsp;语言标准相关：

- `-ansi`：`ANSI`标准。
- `-std=c99`：`C99`标准。
- `-std=gnu89`：`ISO/IEC 9899:1990`以及`GNU`扩充。
- `-std=gnu99`：`ISO/IEC 9899:1999`以及`GNU`扩充。
- `-trigraphs`：支持`ISO C`三字符组。
- `-std=c++11`：支持`C++11`标准。

&emsp;&emsp;链接相关：

- `-llibrary`：进行链接时加载名为`library.a`的静态库。该静态库位于系统的预设目录，或者由`-L`选项确定的目录下。

### gcc的-D选项

&emsp;&emsp;`gcc`的`-D`选项可以定义宏：

- `-Dname`或`-D name`：定义宏`name`，默认定义内容为`1`。
- `-Dname=defn`或`-D name=defn`：定义宏`name`，并且内容为`defn`。

``` bash
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