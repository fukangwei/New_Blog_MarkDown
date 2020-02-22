---
title: 随机函数rand实现
categories: C语言应用代码
date: 2018-12-26 21:23:16
---
&emsp;&emsp;`rand`函数通过一个全局静态变量种子来按一定规则产生一个随机数，并且更新这个全局变量作为下一次的种子变量。但是如果种子变量的初始值固定不变，那么`rand`生成的随机变量每次都是固定的序列。但是时间是一直在变的，所以可以用时间变量来改变种子变量的值。<!--more-->

``` cpp
#include "stdio.h"
#include "time.h"

static unsigned long int next = 1;

void imsrand ( unsigned int );
int imrand ( void );

int main ( void ) {
    int it = 0;
    int array[10] = {0};
    imsrand ( ( unsigned int ) time ( 0 ) );

    while ( 1 ) {
        for ( it = 0; it < 10; ++it ) {
            array[it] = imrand() % 100;
        }

        printf ( "随机整型数组为:" );

        for ( it = 0; it < 10; ++it ) {
            printf ( "%3d", array[it] );
        }

        printf ( "\n" );
        getchar();
    }

    return 0;
}

int imrand ( void ) { /* 返回取值在0至32767之间的伪随机数 */
    next = next * 1103515245 + 12345;
    return ( unsigned int ) ( next / 65536 ) % 32768;
}

void imsrand ( unsigned int seed ) { /* 为rand函数设置种子数 */
    next = seed;
}
```