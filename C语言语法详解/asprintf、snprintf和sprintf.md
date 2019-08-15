---
title: asprintf、snprintf和sprintf
date: 2018-12-06 17:49:34
categories: C语言语法详解
---
### 用“snprintf/asprintf”取代不安全的“sprintf”

&emsp;&emsp;snprintf(或者_snprintf)可以用来只计算长度，例如`int c = snprintf(NULL, 0, ...);`。
&emsp;&emsp;asprintf第一个参数是指向指针的指针，它会在heap中自动申请空间(最后要手动free)。使用asprintf，要加上`#define _GNU_SOURCE`。
&emsp;&emsp;在C语言里，要建立一个字符数组的字符串，常常会使用sprintf这个函数来做格式化的处理。但是实际上，这个函数却不是那么安全。sprintf函数原型如下所示：

``` c
int sprintf ( char* str, const char* format, ... );
```

函数作用为最多从源串中拷贝`n - 1`个字符到目标串中，然后再在后面加一个`0`。所以如果目标串的大小为n的话，将不会溢出。若成功，则返回欲写入的字符串长度；若出错，则返回负值。
&emsp;&emsp;也就是在使用前，必须要先建立好一个字符数组的空间，再用这个函数把内容填入，下面就是简单的例子：

``` c
int tmp = 10;
char cstr[20];
sprintf ( cstr, "%d * %d = %d", tmp, tmp, tmp* tmp );
```

在这个例子里，cstr最后的值会是`10 * 10 = 100`。如果把tmp的值改成10000的话，cstr则要变成`10000 * 10000 = 100000000`，由于这时候的字符串所需长度为26，而要写入的cstr的长度只有20，所以就会造成`buffer overflow`的问题。
&emsp;&emsp;怎么避免sprintf的`buffer overflow`的问题呢？C99中多了一个`snprinf`，它是用来取代现有的sprintf函数：

``` c
int snprintf ( char* str, size_t size, const char* restrict format, ... );
```

snprinf比sprintf多了一个参数size，这个参数的用处就是用来限制最大的写入数据量，可以用来避免`buffer overflow`。如下所示：

``` c
int tmp = 10000;
char cstr[20];
snprintf ( cstr, sizeof ( cstr ), "%d * %d = %d", tmp, tmp, tmp * tmp );
```

这样一来，snprinf在把数据写到cstr时，最多就只会写入20个字符(cstr的长度)，而不会有`buffer overflow`的问题了。

``` c
int asprintf ( char** strp, const char* fmt, ... );
int vasprintf ( char** strp, const char* fmt, va_list ap );
```

&emsp;&emsp;函数asprintf和vasprintf是sprintf和vsprintf的对等物。它们分配一个包括结尾空字符在内的足够大的输出内存，并且通过其第一个参数返回。这个指针应该传给free，以便不再使用时释放它。当成功时，如同sprintf一样，这些函数返回打印的字节个数。如果内存分配失败，或者其它错误发生了，这些函数返回`-1`，并且strp的内容是未定义的。以下是`man page`的描述：

``` bash
The functions asprintf() and vasprintf() are analogues of sprintf() and vsprintf(), except that
they allocate a string large enough to hold the output including the terminating null byte,
and return a pointer to it via the first parameter. This pointer should be passed to
free(3) to release the allocated storage when it is no longer needed.
```

&emsp;&emsp;asprintf可以说是一个增强版的sprintf，在不确定字符串的长度时，非常灵活方便，能够根据格式化的字符串长度，申请足够的内存空间。此外，使用完后必须通过free释放空间。不过，这是GNU扩展的C函数库，不是标准C函数库或者POSIX。

### 要避免C语言的sprintf目标缓冲区溢出

&emsp;&emsp;当用于sprintf的格式串已知且相对简单时，你有时可以预测出缓冲区的大小。对于整形`%d`输出的字符数不会超过`(sizeof(int) * CHAR_BIT + 2) / 3 + 1 /* “+1” for “-” */`。但是这个计算可能有些过于保守了。它计算的是数字以八进制存储需要的字节数；十进制的存储可以保证使用同样或更少的字节数。
&emsp;&emsp;当格式串更复杂或者在运行前未知的时候，预测缓冲区大小会变得跟重新实现sprintf一样困难，而且会很容易出错。如果不能确保缓冲区足够大，你就不能调用sprintf，以防缓冲区溢出后改写其它的内存区。要避免溢出问题，你可以使用限制长度的sprintf版本，即snprintf。这样使用：

``` c
snprintf ( buf, bufsize, "You typed \"%s\"", answer );
```

C99的snprintf提供了预测任意sprintf调用所需的缓冲区大小的方法。snprintf返回它可能放到缓冲区的字符数，而它又可以用“0”作为缓冲区大小进行调用。因此，

``` c
nch = snprintf ( NULL, 0, fmtstring, /* 其它参数 */ );
```

这样的调用就可以预测出格式串扩展后所需要的字符数。另一个(非标准的)选择是asprintf函数，在bsd和GNU的C库中都有提供，它调用malloc为格式串分配空间，并返回分配内存区的指针。这样使用：

``` c
char* buf;
asprintf ( &buf, "%d = %s", 42, "forty-two" ); /* 现在的buf指向含有格式串的malloc的内存 */
```

&emsp;&emsp;**补充说明**：在`Linux`系统下，`sprintf_s`可以用`snprintf`替代。