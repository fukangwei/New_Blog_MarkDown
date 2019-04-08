---
title: vsnprintf和vprintf函数
date: 2018-12-14 16:35:26
categories: C语言语法详解
---

### vsnprintf函数

&emsp;&emsp;该博客主要来源于[_vsnprintf](https://baike.baidu.com/item/_vsnprintf)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;`_vsnprintf`是C语言库函数之一，属于可变参数函数，用于向字符串中打印数据，数据格式由用户自定义。

``` c
#include <stdarg.h>
int _vsnprintf ( char* str, size_t size, const char* format, va_list ap );
```

- `char* str`：把生成的格式化的字符串存放在这里。
- `size_t size`：str可接受的最大字符数(非字节数，UNICODE一个字符是两个字节)，防止产生数组越界。
- `const char* format`：指定输出格式的字符串，它决定了你需要提供的可变参数的类型、个数和顺序。
- `va_list ap`：`va_list`变量(va即`variable argument`，意思是可变参数)。

函数功能是将可变参数格式化输出到一个字符数组。用法类似于vsprintf，不过加了size的限制，防止了内存溢出(size为str所指的存储空间的大小)。函数执行成功，则返回写入到字符数组str中的字符个数(不包含终止符)，最大不超过size；执行失败，则返回负值，并置errno。
&emsp;&emsp;注意，Linux环境下是`vsnprintf`，VC6环境下是`_vsnprintf`。

``` c
#include <stdio.h>
#include <stdarg.h>
​
int mon_log ( char* format, ... ) {
    char str_tmp[50];
    int i = 0;
    va_list vArgList; /* 定义一个va_list型的变量，这个变量是指向参数的指针 */
    /* 用va_start宏初始化变量，这个宏的第二个参数是第一个可变参数的前一个参数，是一个固定的参数 */
    va_start ( vArgList, format );
    i = vsnprintf ( str_tmp, 50, format, vArgList );
    printf ( "str_tmp is %s\n", str_tmp );
    va_end ( vArgList ); /* 用va_end宏结束可变参数的获取 */
    return i; /* 返回参数的字符个数中间有逗号间隔 */
}

int main() {
    int i = mon_log ( "%s,%d,%d,%d", "asd", 2, 3, 4 );
    printf ( "%d\n", i );
    return 0;
}
```

执行结果：

``` bash
str_tmp is asd,2,3,4
9
```

### vprintf函数

&emsp;&emsp;函数原型如下所示：

``` c
#include <stdio.h>
#include <stdarg.h>
int vprintf ( const char* format, va_list ap );
```

vprintf的作用和printf相同，参数format格式也相同，`va_list`为不定个数的参数列。对于返回值，成功则返回实际输出的字符数，失败则返回`-1`，错误原因存于errno中。

``` c
#include <stdio.h>
#include <stdarg.h>

int vpf ( char* fmt, ... ) {
    va_list argptr;
    int cnt;
    va_start ( argptr, fmt );
    cnt = vprintf ( fmt, argptr );
    va_end ( argptr );
    return ( cnt );
}

int main ( void ) {
    int inumber = 30;
    float fnumber = 90.0;
    char* string = "abc";
    vpf ( "%d %f %s\n", inumber, fnumber, string );
    return 0;
}
```