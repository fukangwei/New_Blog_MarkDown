---
title: fsetpos和fgetpos函数
date: 2018-12-13 12:33:40
categories: C语言语法详解
---
&emsp;&emsp;该博客从网上整理了fsetpos和fgetpos函数有关的内容，感谢分享资料的人们！

### fgetpos函数

&emsp;&emsp;fgetpos的功能是依据当前文件的句柄，获取当前访问指针位置信息：

``` c
#include <stdio.h>
int fgetpos ( FILE* stream, fpos_t* pos );
```

函数功能是取得当前文件的指针所指的位置，并把该指针所指的位置数存放到pos所指的对象中。pos值以内部格式存储，仅由fgetpos和fsetpos使用，其中fsetpos的功能与fgetpos相反。返回值为如果成功返回0，失败返回非0值，并设置errno。

``` c
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

&emsp;&emsp;首先，程序以读写方式打开一个名为`test.txt`的文件，并把字符串`This is a test`写入文件。注意，字符串共14个字节，地址为0至13。用fwrite函数写入后，文件指针自动指向文件最后一个字节的下一个位置，即这时的fp的指针值应该是14。再用fgetpos函数取得指针位置并存入pos，此时pos中的内容为14。然后在屏幕上显示出`The file pointer is at byte 14`。再用fseek函数重设文件指针的位置，让fp的值为3，即指向文件中第4个字节。再次取得指针位置并存入pos，然后在屏幕上显示出`The file pointer is at byte 3`。

### fsetpos函数

&emsp;&emsp;fsetpos函数将文件指针定位在pos指定的位置上，该函数的功能与fgetpos相反，是将文件指针fp按照pos指定的位置在文件中定位。函数原型如下所示：

``` c
#include <stdio.h>
int fsetpos ( FILE* fp, const fpos_t* pos );
```

函数执行成功则返回0，否则返回非0值。例程如下所示：

``` c
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

&emsp;&emsp;首先，程序以只读方式打开名为`test.txt`的文件。在这里，`test.txt`文件中已存入字符串`This is a test for testing the function of fsetpos.`。将pos设置为10，使用fsetpos函数将文件指针fp按照pos指定的位置在文件中定位。这样文件指针fp指向字符串中test的字母t。再从新定位的文件指针开始读取16个字符到buffer缓冲区，也就是说读取字符串`test for testing`到缓冲区buffer。最后显示结果`16 bytes at byte 10: test for testing`。