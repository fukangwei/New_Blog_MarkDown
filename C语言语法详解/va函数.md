---
title: va函数
date: 2018-12-10 09:08:20
categories: C语言语法详解
---
&emsp;&emsp;`va_list`、`va_start`、`va_arg`和`va_end`这几个宏都是用于函数的可变参数的：

``` cpp
/* stdarg.h */
#define va_start _crt_va_start
#define va_arg _crt_va_arg
#define va_end _crt_va_end

/* vadefs.h */
#define _ADDRESSOF(v)        ( &reinterpret_cast<const char &>(v) )
typedef char* va_list;
#define _INTSIZEOF(n)        ( (sizeof(n) + sizeof(int) - 1) & ~(sizeof(int) - 1) )
#define _crt_va_start(ap, v) ( ap = (va_list)_ADDRESSOF(v) + _INTSIZEOF(v) )
#define _crt_va_arg(ap, t)   ( *(t *)((ap += _INTSIZEOF(t)) - _INTSIZEOF(t)) )
#define _crt_va_end(ap)      ( ap = (va_list)0 )
```

各个宏的功能如下：

- `va_list`用于声明一个变量，我们知道函数的可变参数列表其实就是一个字符串，所以`va_list`才被声明为字符型指针，这个类型用于声明一个指向参数列表的字符型指针变量，例如`va_list ap; /* ap: arguement pointer */`。
- `va_start(ap, v)`的第一个参数是指向可变参数字符串的变量，第二个参数是可变参数函数的第一个参数，通常用于指定可变参数列表中参数的个数。
- `va_arg(ap, t)`的第一个参数指向可变参数字符串的变量，第二个参数是可变参数的类型。
- `va_end(ap)`用于将存放可变参数字符串的变量清空(赋值为`NULL`)。

&emsp;&emsp;我们看一段用可变参数列表的函数来求数组和的代码：

``` cpp
/*
* 功能：宏va_arg用于给函数传递可变长度的参数列表。
* 首先，必须调用va_start传递有效的参数列表va_list和函数强制的第一个参数。第一个参数代表将要传递的参数的个数。
* 其次，调用va_arg传递参数列表va_list和将被返回的参数的类型。va_arg的返回值是当前的参数。
* 再次，对所有的参数重复调用va_arg。
* 最后，调用va_end传递va_list对完成后的清除是必须的。
*/
#include <stdio.h>
#include <stdarg.h>
#include <stdlib.h>
​
int sum ( int number, ... ) { /* 第一个参数指定了参数的个数 */
    va_list vaptr;
    int i;
    int sum = 0;
    va_start ( vaptr, number );

    for ( i = 0; i < number; i++ ) {
        sum += va_arg ( vaptr, int );
    }

    va_end ( vaptr );
    return sum;
}

int main ( void ) {
    printf ( "%d\n", sum ( 4, 4, 3, 2, 1 ) );
    system ( "pause" );
    return 0;
}
```

&emsp;&emsp;`va_start`的功能是要把`ap`指针指向可变参数的第一个参数位置处：

``` cpp
#define _crt_va_start(ap, v) ( ap = (va_list)_ADDRESSOF(v) + _INTSIZEOF(v) )
```

先取第一个参数的地址，在`sum`函数中就是取`number`的地址并且将其转化为`char *`的(因为`char *`的指针进行加减运算后，偏移的字节数才与加的数字相同。如果为`int *p`，那么`p + 1`实际上将`p`移动了`4`个字节)，然后加上`4`(计算过程是`__INITSIZEOF(number) = (4 + 3) & ~3`)，这样就将`ap`指向了可变参数字符串的第一个参数。

``` cpp
#define _INTSIZEOF(n) ( (sizeof(n) + sizeof(int) - 1) & ~(sizeof(int) - 1) )
```

以`int`所占的字节为标准进行对其操作。如果`int`占四字节，则以`4`字节对齐为标准读取数据。
&emsp;&emsp;`va_arg`是要从`ap`中取下一个参数：

``` c
#define _crt_va_arg(ap, t) ( *(t *)((ap += _INTSIZEOF(t)) - _INTSIZEOF(t)) )
```

首先是`ap = ap + __INTSIZEOF(t)`，此时`ap`已经被改变了，它已经指向了下一个参数。我们令`x = ap + __INTSIZEOF(t);`，那么括号内就变成了`(x - __INTSIZEOF(t))`，但是这里没有赋值运算符，所以`ap`的值没有发生变化，此时`ap`仍然指向的是当前参数的下一个参数的位置，也就是说`ap`指向的位置比当前正在处理的位置超前了一个位置。其实写成下面的形式就简单明了了：

``` cpp
#define va_arg(ap, t) ( *(t *)((ap += _INTSIZEOF(t)), ap - _INTSIZEOF(t)) )
```

&emsp;&emsp;为什么要将`ap`指向当前处理参数的下一个参数了？经过上面的分析，我们知道`va_start(ap, v)`已经将`ap`指向了可变参数列表的第一个参数了，以后我们每一步操作都需要将`ap`移动到下一个参数的位置，由于每次使用可变参数的顺序是`va_start(ap, v) -> va_arg(ap, t)`，这样我们在第一次去参数的时候，其实`ap`已经指向了第二个参数开始的位置，所以我们用表达式的方式获得一个指向第一个参数的临时指针，就可以采用这种一致的方式来处理可变参数列表。
&emsp;&emsp;`va_end(ap)`将声明的`ap`指针置为空，因为指针使用后最后设置为空。