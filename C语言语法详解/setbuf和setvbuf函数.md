---
title: setbuf和setvbuf函数
categories: C语言语法详解
date: 2018-12-14 17:27:26
---
&emsp;&emsp;函数`setbuf`用于将指定缓冲区与特定的文件流相关联，实现操作缓冲区时直接操作文件流的功能。<!--more-->

``` cpp
#include <stdio.h>
void setbuf ( FILE* stream, char* buf );
```

参数`stream`为文件流指针，`buf`为缓冲区的起始地址。如果参数`buf`为`NULL`，则为无缓冲，`setbuf`相当于调用`setvbuf(stream, buf, _IONBF, BUFSIZE)`。
&emsp;&emsp;`setbuf`和`setvbuf`函数的实际意义在于：用户打开一个文件后，可以建立自己的文件缓冲区，而不必使用`fopen`函数打开文件时设定的默认缓冲区。这样就可以让用户自己来控制缓冲区，包括改变缓冲区大小、定时刷新缓冲区、改变缓冲区类型、删除流中默认的缓冲区、为不带缓冲区的流开辟缓冲区等。
&emsp;&emsp;在打开文件流后，读取内容之前，可以调用`setbuf`来设置文件流的缓冲区(而且必须是这样)。

``` cpp
#include <stdio.h>

char outbuf[BUFSIZ];

int main ( void ) {
    setbuf ( stdout, outbuf ); /* 把缓冲区与流相连 */
    puts ( "This is a test of buffered output.\n" );
    puts ( outbuf );
    fflush ( stdout ); /* 刷新 */
    puts ( outbuf ); /* 输出 */
    return 0;
}
```

执行结果：

``` bash
This is a test of buffered output..
This is a test of buffered output..
This is a test of buffered output..
This is a test of buffered output..
```

程序先把`outbuf`与输出流相连，然后输出一个字符串，这时因为缓冲区已经与流相连，所以`outbuf`中也保存着这个字符串，紧接着`puts`函数又输出一遍，所以现在`outbuf`中保存着两个一样的字符串。刷新输出流之后，再次`puts`，则又输出两个字符串。

---

### setbuf函数详解

&emsp;&emsp;程序输出有两种方式：一种是即时处理方式，另一种是先暂存起来，然后再大块写入的方式，前者往往造成较高的系统负担。因此，`C`语言通常都允许程序员在进行实际的写操作之前控制产生的输出数据量。这种控制能力一般是通过库函数`setbuf`实现的。如果`buf`是一个大小适当的字符数组，那么`setbuf(stdout, buf);`语句将通知输入/输出库，所有写入到`stdout`的输出都应该使用`buf`作为输出缓冲区，直到`buf`缓冲区被填满或者程序员直接调用`fflush`(译注：对于由写操作打开的文件，调用`fflush`将导致输出缓冲区的内容被实际地写入该文件)，`buf`缓冲区中的内容才实际写入到`stdout`中。缓冲区的大小由系统头文件`stdio.h`中的`BUFSIZ`定义。
&emsp;&emsp;下面的程序的作用是把标准输入的内容复制到标准输出中，演示了`setbuf`库函数最显而易见的用法：

``` cpp
#include <stdio.h>

int main() {
    int c;
    char buf[BUFSIZ];
    setbuf ( stdout, buf );

    while ( ( c = getchar() ) != EOF ) {
        putchar ( c );
    }
}
```

&emsp;&emsp;遗憾的是，这个程序是错误的，仅仅是因为一个细微的原因。程序中对库函数`setbuf`的调用，通知了输入/输出库所有字符的标准输出应该首先缓存在`buf`中。要找到问题出自何处，我们不妨思考一下`buf`缓冲区最后一次被清空是在什么时候？答案是在`main`函数结束之后，作为程序交回控制权给操作系统之前`C`运行库所必须进行的清理工作的一部分。但是，在此之前`buf`字符数组已经被释放！
&emsp;&emsp;要避免这种类型的错误有两种办法。第一种办法是让缓冲数组成为静态数组，既可以直接显式声明`buf`为静态：

``` cpp
static char buf[BUFSIZ];
```

也可以把`buf`声明完全移到`main`函数之外。
&emsp;&emsp;第二种办法是动态分配缓冲区，在程序中并不主动释放分配的缓冲区(译注：由于缓冲区是动态分配的，所以`main`函数结束时并不会释放该缓冲区，这样`C`运行库进行清理工作时就不会发生缓冲区已释放的情况)：

``` cpp
char* malloc();
setbuf ( stdout, malloc ( BUFSIZ ) );
```

如果读者关心一些编程小技巧，也许会注意到这里其实并不需要检查`malloc`函数调用是否成功。如果`malloc`函数调用失败，将返回一个`null`。`setbuf`函数的第二个参数取值可以为`null`，此时标准输出不需要进行缓冲。这种情况下，程序仍然能够工作，只不过速度较慢而已。

---

### setvbuf详解

&emsp;&emsp;函数原型如下：

``` cpp
int setvbuf ( FILE* stream, char* buf, int type, unsigned size );
```

- `type`：期望缓冲区的类型：

1. `_IOFBF`(满缓冲)：当缓冲区为空时，从流读入数据。或者当缓冲区满时，向流写入数据。
2. `_IOLBF`(行缓冲)：每次从流中读入一行数据或向流中写入一行数据。
3. `_IONBF`(无缓冲)：直接从流中读入数据或直接向流中写入数据，而没有缓冲区。

- `size`：缓冲区内字节的数量。

&emsp;&emsp;如果`buf`的值不为`NULL`，则`setvbuf`函数将`buf`指向的区域作为流的缓冲区，否则分配一个缓冲区。如果`setvbuf`函数出错，则返回一个`非0值`。注意如下内容：

``` bash
This function should be called once the file associated with the stream has
already been opened but before any input or output operation has taken place.
```

意思是这个函数应该在打开流后，在任何对该流做输入输出前，立即调用。

``` cpp
#include <stdio.h>

int main() {
    FILE* input, *output;
    char bufr[512];
    input = fopen ( "file.in", "r+b" );
    output = fopen ( "file.out", "w" );

    /* set up input stream for minimal disk access,
       using our own character buffer */
    if ( setvbuf ( input, bufr, _IOFBF, 512 ) != 0 ) {
        printf ( "failed to set up buffer for input file\n" );
    } else {
        printf ( "buffer set up for input file\n" );
    }

    /* set up output stream for line buffering using space that
       will be obtained through an indirect call to malloc */
    if ( setvbuf ( output, NULL, _IOLBF, 132 ) != 0 ) {
        printf ( "failed to set up buffer for output file\n" );
    } else {
        printf ( "buffer set up for output file\n" );
    }

    /* perform file I/O here */
    /* close files */
    fclose ( input );
    fclose ( output );
    return 0;
}
```