---
title: setjmp和longjmp函数
date: 2018-12-13 08:58:46
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[setjmp和longjmp函数使用详解](https://blog.csdn.net/chenyiming_1990/article/details/8683413)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;非局部跳转语句是setjmp和longjmp函数。非局部指的是：不是由C语言goto语句在一个函数内实施的跳转，而是在栈上跳过若干调用帧，返回到当前函数调用路径上的某一个函数中。

``` c
#include <setjmp.h>
int setjmp ( jmp_buf env );
```

返回值为：若直接调用则返回0；若从longjmp调用返回，则返回非0值的longjmp中的val值。longjmp函数原型如下所示：

``` c
void longjmp ( jmp_buf env, int val );
```

调用此函数则返回到语句setjmp所在的地方，其中env就是setjmp中的env，而val则是使setjmp的返回值变为val。
&emsp;&emsp;首先声明一个`jmp_buf`变量，并调用setjmp进行初始化，setjmp的返回值为0。setjmp把程序的状态信息(例如堆栈指针的当前位置和程序的计数器)保存到跳转缓冲区(即程序当前正在执行的指令的地址)，你调用setjmp时所处的函数便是你的`顶层`函数。以后，在顶层函数或其他任何它所调用的函数(不论是直接调用或是间接调用)内的任何地方调用longjmp函数，将导致这个被保存的状态重新恢复。longjmp的效果就是使执行流通过再次从setjmp返回，从而立即跳回到顶层函数中。使用模板如下所示：

``` c
if ( setjmp ( env ) == 0 ) {
    /* 直接调用setjmp时，转移到这里 */
} else {
    /* 调用longjmp时，转移到这里 */
}
```

&emsp;&emsp;当检查到一个错误时，则以两个参数调用longjmp函数，第一个就是在调用setjmp时所用的env，第二个参数是具有非0值的val，它将成为从setjmp处返回的值。使用第二个参数的原因是对于一个setjmp可以有多个longjmp。

``` c
#include <stdio.h>
#include <setjmp.h>
​
static jmp_buf buf;
​
void second ( void ) {
    printf ( "second\n" );
    longjmp ( buf, 1 ); /* 跳回setjmp的调用处，使得setjmp返回值为1 */
}
​
void first ( void ) {
    second();
    printf ( "first\n" ); /* 不可能执行到此行 */
}
​
int main() {
    if ( ! setjmp ( buf ) ) {
        first(); /* 进入此行前，setjmp返回0 */
    } else { /* 当longjmp跳转回，setjmp返回1，因此进入此行 */
        printf ( "main\n" );
    }

    return 0;
}
```

执行结果：

``` bash
second
main
```

注意到虽然first子程序被调用，但`first`不可能被打印。`main`被打印，因为条件语句`if (!setjmp(buf))`被执行第二次。
&emsp;&emsp;使用setjmp和longjmp要注意以下几点：
&emsp;&emsp;1. setjmp与longjmp结合使用时，它们必须有严格的先后执行顺序，也即先调用setjmp函数，之后再调用longjmp函数，以恢复到先前被保存的`程序执行点`。否则，如果在setjmp调用之前执行longjmp函数，将导致程序的执行流变的不可预测，很容易导致程序崩溃而退出。
&emsp;&emsp;2. longjmp必须在setjmp调用之后，而且longjmp必须在setjmp的作用域之内。具体来说，在一个函数中使用setjmp来初始化一个全局标号，然后只要该函数未曾返回，那么在其它任何地方都可以通过longjmp调用来跳转到setjmp的下一条语句执行。实际上setjmp函数将发生调用处的局部环境保存在了一个`jmp_buf`的结构当中，只要主调函数中对应的内存未曾释放(函数返回时局部内存就失效了)，那么在调用longjmp的时候就可以根据已保存的jmp_buf参数恢复到setjmp的地方执行。
&emsp;&emsp;3. 当顶层函数(调用setjmp的那个)返回时，保存在跳转缓冲区中的状态信息便不再有效。在此之后调用longjmp很可能失效，而它的症状很难调试。这就是为什么longjmp只能在顶层函数或者在顶层函数所调用的函数中进行调用的原因。

### 异常处理

&emsp;&emsp;在下例中，setjmp被用于包住一个例外处理，类似于try。longjmp调用类似于throw语句，允许一个异常返回给setjmp一个异常值。下属代码示例遵从`1999 ISO C standard`与`Single UNIX Specification`，即仅在特定范围内引用setjmp：

- if、switch或它们的嵌套使用的条件表达式。
- 上述情况下与“!”一起使用或者与整数常值比较。
- 作为单独的语句(不使用其返回值)。

&emsp;&emsp;遵从上述规则使得创建程序环境缓冲区更为容易。更一般的使用setjmp可能引起未定义行为，如破坏局部变量；编译器被要求保护或警告这些用法。但轻微的复杂用法如`switch ((exception_type = setjmp(env))) { }`在文献与实践中是常见的，并保持了相当的可移植性。

``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <setjmp.h>

void first ( void );
void second ( void );

/* This program's output is:
   calling first
   calling second
   entering second
   second failed with type 3 exception; remapping to type 1.
   first failed, exception type 1
*/

/* Use a file scoped static variable for the exception stack
   so we can access it anywhere within this translation unit. */
static jmp_buf exception_env;
static int exception_type;

int main() {
    void* volatile mem_buffer;
    mem_buffer = NULL;

    if ( setjmp ( exception_env ) ) {
        /* if we get here there was an exception */
        printf ( "first failed, exception type %d\n", exception_type );
    } else {
        /* Run code that may signal failure via longjmp. */
        printf ( "calling first\n" );
        first();
        mem_buffer = malloc ( 300 ); /* allocate a resource */
        printf ( strcpy ( ( char* ) mem_buffer, "first succeeded!" ) ); /* ... this will not happen */
    }

    if ( mem_buffer ) {
        free ( ( void* ) mem_buffer ); /* carefully deallocate resource */
    }

    return 0;
}

void first ( void ) {
    jmp_buf my_env;
    printf ( "calling second\n" );
    memcpy ( my_env, exception_env, sizeof ( jmp_buf ) );

    switch ( setjmp ( exception_env ) ) {
        case 3:
            /* if we get here there was an exception. */
            printf ( "second failed with type 3 exception; remapping to type 1.\n" );
            exception_type = 1;

        default: /* fall through */
            memcpy ( exception_env, my_env, sizeof ( jmp_buf ) ); /* restore exception stack */
            longjmp ( exception_env, exception_type ); /* continue handling the exception */

        case 0:
            /* normal, desired operation */
            second();
            printf ( "second succeeded\n" ); /* not reached */
    }

    memcpy ( exception_env, my_env, sizeof ( jmp_buf ) ); /* restore exception stack */
}

void second ( void ) {
    printf ( "entering second\n" ); /* reached */
    exception_type = 3;
    longjmp ( exception_env, exception_type ); /* declare that the program has failed */
    printf ( "leaving second\n" ); /* not reached */
}
```