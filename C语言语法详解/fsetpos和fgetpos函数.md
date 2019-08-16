---
title: fsetpos和fgetpos函数
date: 2018-12-13 12:33:40
categories: C语言语法详解
---
### fgetpos函数

&emsp;&emsp;`fgetpos`的功能是依据当前文件的句柄，获取当前访问指针位置信息：

``` cpp
#include <stdio.h>
int fgetpos ( FILE* stream, fpos_t* pos );
```

函数功能是取得当前文件的指针所指的位置，并把该指针所指的位置数存放到`pos`所指的对象中。`pos`值以内部格式存储，仅由`fgetpos`和`fsetpos`使用，其中`fsetpos`的功能与`fgetpos`相反。返回值为如果成功返回`0`，失败返回`非0值`，并设置`errno`。

``` cpp
#include <string.h>
#include <stdio.h>

int main ( void ) {
    FILE* fp;
    char string[] = "This is a test";
    fpos_t pos;
    fp = fopen ( "test.txt", "w+" );
    fwrite ( string, strlen ( string ), 1, fp );
    fgetpos ( fp, &pos );
    printf ( "The file pointer is at byte %ld\n", pos );
    fseek ( fp, 3, 0 );
    fgetpos ( fp, &pos );
    printf ( "The file pointer is at byte %ld\n", pos );
    fclose ( fp );
    return 0;
}
```

&emsp;&emsp;首先，程序以读写方式打开一个名为`test.txt`的文件，并把字符串`This is a test`写入文件。注意，字符串共`14`个字节，地址为`0`至`13`。用`fwrite`函数写入后，文件指针自动指向文件最后一个字节的下一个位置，即这时的`fp`的指针值应该是`14`。再用`fgetpos`函数取得指针位置并存入`pos`，此时`pos`中的内容为`14`。然后在屏幕上显示出`The file pointer is at byte 14`。再用`fseek`函数重设文件指针的位置，让`fp`的值为`3`，即指向文件中第`4`个字节。再次取得指针位置并存入`pos`，然后在屏幕上显示出`The file pointer is at byte 3`。

### fsetpos函数

&emsp;&emsp;`fsetpos`函数将文件指针定位在`pos`指定的位置上，该函数的功能与`fgetpos`相反，是将文件指针`fp`按照`pos`指定的位置在文件中定位：

``` cpp
#include <stdio.h>
int fsetpos ( FILE* fp, const fpos_t* pos );
```

函数执行成功则返回`0`，否则返回`非0值`。

``` cpp
#include <stdio.h>

void main ( void ) {
    FILE* fp;
    fpos_t pos;
    char buffer[50];

    if ( ( fp = fopen ( "test.txt", "rb" ) ) == NULL ) {
        printf ( "Trouble opening file\n" );
    } else {
        pos = 10;

        if ( fsetpos ( fp, &pos ) != 0 ) {
            perror ( "fsetpos error" );
        } else {
            fread ( buffer, sizeof ( char ), 16, fp );
            printf ( "16 bytes at byte %ld: %.16s\n", pos, buffer );
        }
    }

    fclose ( fp );
}
```

&emsp;&emsp;首先，程序以只读方式打开名为`test.txt`的文件。在这里，`test.txt`文件中已存入字符串`This is a test for testing the function of fsetpos.`。将`pos`设置为`10`，使用`fsetpos`函数将文件指针`fp`按照`pos`指定的位置在文件中定位。这样文件指针`fp`指向字符串中`test`的字母`t`。再从新定位的文件指针开始读取`16`个字符到`buffer`缓冲区，也就是说读取字符串`test for testing`到缓冲区`buffer`。最后显示结果`16 bytes at byte 10: test for testing`。