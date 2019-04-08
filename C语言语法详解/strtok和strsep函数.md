---
title: strtok和strsep函数
date: 2018-12-14 19:55:28
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[strtok](https://baike.baidu.com/item/strtok)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;函数原型如下所示：

``` c
char* strtok ( char* s, const char* delim );
char* strsep ( char** s, const char* delim );
```

strtok和strsep两个函数的功能都是用来分解字符串为一组字符串，s为要分解的字符串，delim为分隔符字符串。函数返回从s开头开始的一个个子串，当没有分割的子串时返回NULL。两者都会改变源字符串，想要避免，可以使用strdupa或strdup。
&emsp;&emsp;strtok函数第一次调用时会把s字符串中所有在delim中出现的字符替换为NULL，然后通过依次调用`strtok(NULL, delim)`得到各部分子串。测试代码如下所示：

``` c
#include <stdio.h>
#include <string.h>

int main ( void ) {
    char s[] = "hello, world! welcome to china!";
    char delim[] = " ,!";
    char* token;

    for ( token = strtok ( s, delim ); token != NULL; token = strtok ( NULL, delim ) ) {
        printf ( "%s", token );
        printf ( "+" );
    }

    printf ( "\n" );
    return 0;
}
```

执行结果：

``` bash
hello+world+welcome+china+
```

&emsp;&emsp;对于strsep有如下例子：

``` c
#include <stdio.h>
#include <string.h>

int main ( void ) {
    char source[] = "hello, world! welcome to china!";
    char delim[] = " ,!";
    char* s = strdup ( source );
    char* token;

    for ( token = strsep ( &s, delim ); token != NULL; token = strsep ( &s, delim ) ) {
        printf ( "%s", token );
        printf ( "+" );
    }

    printf ( "\n" );
    return 0;
}
```

执行结果：

``` bash
hello++world++welcome+to+china++
```

&emsp;&emsp;为什么用strtok时子串中间只有一个`+`，而strsep却有多个`+`呢？文档中有如下的解释：

``` c
One difference between strsep and strtok_r is that if the input string contains
more than one character from delimiter in a row strsep returns an empty string
for each pair of characters from delimiter. This means that a program normally
should test for strsep returning an empty string before processing it.
```

&emsp;&emsp;如果输入的串的有连续的多个字符属于delim(此例source中的逗号加上空格，感叹号加上空格等就是这种情况)，strtok会返回NULL，而strsep会返回空串。因而如果想用strsep函数分割字符串，必须进行返回值是否是空串的判断。这也就解释了strsep的例子中有多个`+`的原因。我们在自己的程序中最好尽量避免使用strtok，转而使用strsep。

---

### strsep函数用法

&emsp;&emsp;strsep作为strtok的升级版，是一个很有用的字符串处理函数，但是也有很多不方便的地方，使用时需要特别小心。好在注意的事项都在`man strsep`里，如下所示：

``` c
#include <string.h>
char* strsep ( char** stringp, const char* delim );
​
Be cautious when using this function. If you do use it, note that:
* This function modifies its first argument.
* This function cannot be used on constant strings.
* The identity of the delimiting character is lost.
```

实例如下所示：

``` c
#include <string.h>
#include <stdio.h>

int main ( int arg, const char* argv[] ) {
    /* 字符串不能为常量，所以用strdup */
    char* string = strdup ( "/home/yinlijun/project:/home/yinlijun:/home/someone" );
    char* p;

    /* 第一个参数设为二级指针，字符串中所有的第二个参数(子串)最后会被替代成“\0” */
    while ( ( p = strsep ( &string, ":" ) ) != NULL ) {
        printf ( "%s\n", p );
    }

    return 0;
}
```

执行结果：

``` bash
/home/yinlijun/project
/home/yinlijun
/home/someone
```