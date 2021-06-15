---
title: freopen函数
categories: C语言语法详解
date: 2018-12-12 19:58:09
---
### 基础作用

&emsp;&emsp;`freopen`是`C`标准库头文件`stdio.h`中的一个函数，用于重定向输入输出流。该函数可以在不改变代码原貌的情况下改变输入输出环境，但使用时应当保证流是可靠的。<!--more-->
&emsp;&emsp;`freopen`函数声明如下：

``` cpp
FILE* freopen ( const char* filename, const char* mode, FILE* stream );
```

- `filename`：需要重定向到的文件名或文件路径。
- `mode`：代表文件访问权限的字符串，例如`r`表示`只读访问`、`w`表示`只写访问`、`a`表示`追加写入`。
- `stream`：需要被重定向的文件流。

如果执行成功，则返回该指向该输出流的文件指针，否则返回为`NULL`。
&emsp;&emsp;代码示例`1`如下：

``` cpp
/* 将输出重定向到一个文件中 */
#include <stdio.h>

int main ( void ) {
    if ( freopen ( "./output.txt", "w", stdout ) == NULL ) {
        fprintf ( stderr, "error redirecting stdout\n" );
    }

    printf ( "This will go into a file.\n" );
    fclose ( stdout );
    return 0;
}
```

代码示例`2`如下：

``` cpp
/* 从文件in.txt中读入数据，计算加和并输出到out.txt中 */
#include <stdio.h>

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

### 在竞赛中的使用

&emsp;&emsp;在做`ACM`题目的过程中，需要在本地机器上调试。如果输入数据很庞大的话，就很难忍受一次又一次地重新输入和调试。使用`freopen`函数可以解决测试数据输入问题，避免重复输入：

``` cpp
#include <stdio.h>
#include <iostream>

using namespace std;

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

在本地机器调试时，因为没有定义过`ONLINE_JUDGE`，所以会执行`freopen( "in.txt", "r", stdin );`方便本机上的调试；当提交到`OJ`上后，因为有了`ONLINE_JUDGE`的定义，所以跳过语句`freopen( "in.txt", "r", stdin );`，从`int a, b;`处开始执行。