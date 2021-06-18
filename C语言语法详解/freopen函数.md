---
title: freopen函数
categories: C语言语法详解
date: 2018-12-12 19:58:09
---
### 函数声明

&emsp;&emsp;`freopen`用于重定向输入输出流，函数声明如下：<!--more-->

``` cpp
FILE* freopen ( const char* filename, const char* mode, FILE* stream );
```

- `filename`：需要重定向到的文件名或文件路径。
- `mode`：代表文件访问权限的字符串：

1. `r`表示`只读访问`。
2. `w`表示`只写访问`。
3. `a`表示`追加写入`。

- `stream`：需要被重定向的文件流。

### 代码实例

&emsp;&emsp;将输出重定向到一个文件中：

``` cpp
#include <stdio.h>

int main ( void ) {
    if ( freopen ( "./output.txt", "w", stdout ) == NULL ) {
        fprintf ( stderr, "error redirecting stdout\n" );
    }

    printf ( "This will go into a file." );
    fclose ( stdout );
    return 0;
}
```

程序会在当前目录生成`output.txt`，其内容为`This will go into a file.`。
&emsp;&emsp;从文件`in.txt`中读入数据，计算加和并输出到`out.txt`中：

``` cpp
#include <stdio.h>

int main ( void ) {
    int a, b;
    freopen ( "in.txt", "r", stdin );
    freopen ( "out.txt", "w", stdout );

    while ( scanf ( "%d%d", &a, &b ) != EOF ) {
        printf ( "%d", a + b );
    }

    fclose ( stdin );
    fclose ( stdout );
    return 0;
}
```

`in.txt`的内容为`12 23`，生成的`out.txt`的内容为`35`。