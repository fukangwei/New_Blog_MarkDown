---
title: freopen函数
date: 2018-12-12 19:58:09
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[freopen](https://baike.baidu.com/item/freopen)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;freopen是C标准库头文件`stdio.h`中的一个函数，用于重定向输入输出流。该函数可以在不改变代码原貌的情况下改变输入输出环境，但使用时应当保证流是可靠的。C89函数声明为：

``` c
FILE* freopen ( const char* filename, const char* mode, FILE* stream );
```

C99函数声明为：

``` c
FILE* freopen ( const char* restrict filename, const char* restrict mode, FILE* restrict stream );
```

- filename：需要重定向到的文件名或文件路径。
- mode：代表文件访问权限的字符串，例如`r`表示`只读访问`、`w`表示`只写访问`、`a`表示`追加写入`。
- stream：需要被重定向的文件流。

&emsp;&emsp;返回值为：如果成功，则返回该指向该输出流的文件指针，否则返回为NULL。
&emsp;&emsp;代码示例1如下所示：

``` c
/* 将输出重定向到一个文件中 */
#include <stdio.h>

int main ( void ) {
    /* redirect standard output to a file */
    if ( freopen ( "./output.txt", "w", stdout ) == NULL ) {
        fprintf ( stderr, "error redirecting stdout\n" );
    }

    /* this output will go to a file */
    printf ( "This will go into a file.\n" );
    fclose ( stdout ); /* close the standard output stream */
    return 0;
}
```

代码示例2如下所示：

``` c
/* 从文件in.txt中读入数据，计算加和并输出到out.txt中 */
#include <stdio.h>
​
int main ( void ) {
    int a, b;
    freopen ( "in.txt", "r", stdin );
    freopen ( "out.txt", "w", stdout );

    while ( scanf ( "%d%d", &a, &b ) != EOF ) {
        printf ( "%d\n", a + b );
    }

    fclose ( stdin );
    fclose ( stdout );
    return 0;
}
```

### 恢复文件流

&emsp;&emsp;当标准输出stdout被重定向到指定文件后，如何把它重定向回原来`默认`的输出设备(即显示器)呢？C标准库的回复是`不支持`，没有任何方法可以恢复原来的输出流。那是否存在依赖具体平台的实现呢？的确存在这个方法。
&emsp;&emsp;在操作系统中，命令行控制台(即键盘或者显示器)被视为一个文件，既然是文件，那么就有`文件名`。由于历史原因，命令行控制台文件在DOS操作系统和Windows操作系统中的文件名为`CON`，在其它的操作系统(例如`Unix`、`Linux`、`Mac OS X`、`Androi`d等)中的文件名为`/dev/tty`。因此，在Windows中可以使用`freopen( "CON", "w", stdout );`，其它操作系统中使用`freopen( "/dev/tty", "w", stdout );`。
&emsp;&emsp;Windows代码如下所示：

``` c
#include <stdio.h>
#include <stdlib.h>
​
int main ( void ) {
    FILE* stream;

    if ( ( stream = freopen ( "file.txt", "w", stdout ) ) == NULL ) {
        exit ( -1 );
    }

    printf ( "this is stdout output\n" );
    stream = freopen ( "CON", "w", stdout ); /* stdout是向程序的末尾的控制台重定向 */
    printf ( "And now back to the console once again\n" );
    return 0;
}
```

&emsp;&emsp;类Unix代码如下所示：

``` c
#include <stdio.h>
#include <stdlib.h>
​
int main ( void ) {
    FILE* stream;

    if ( ( stream = freopen ( "file.txt", "w", stdout ) ) == NULL ) {
        exit ( -1 );
    }

    printf ( "this is stdout output\n" );
    stream = freopen ( "/dev/tty", "w", stdout ); /* stdout是向程序的末尾的控制台重定向 */
    printf ( "And now back to the console once again\n" );
    return 0;
}
```

&emsp;&emsp;在使用上诉方法在输入输出流间进行反复的重定向时，极有可能导致流指针得到不被期待的结果，即输入输出发生异常。所以，如果需要在文件的输入输出和标准输入输出流之间进行切换，建议使用fopen或者是`C++`标准的ifstream以及ofstream。

---

### freopen函数在竞赛中的使用

&emsp;&emsp;在做ACM题目的过程中，我们需要在本地机器上调试。如果输入数据很庞大的话，就很难忍受一次又一次地重新输入和调试了。使用freopen函数可以解决测试数据输入问题，避免重复输入。示例如下所示：

``` c
#include <stdio.h>
#include <iostream>

using namespace std;​

int main() {
#ifdef ONLINE_JUDGE
#else
    freopen ( "in.txt", "r", stdin );
#endif
    int a, b;

    while ( cin >> a >> b ) {
        cout << a + b << endl;
    }

    return 0;
}
```

在本地机器调试时，因为没有定义过`ONLINE_JUDGE`，所以会执行`freopen( "in.txt", "r", stdin );`方便本机上的调试；当提交到OJ上后，因为有了`ONLINE_JUDGE`的定义，所以跳过语句`freopen( "in.txt", "r", stdin );`，从`int a, b;`处开始执行。