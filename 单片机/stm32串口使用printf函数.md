---
title: stm32串口使用printf函数
date: 2018-12-30 07:06:17
tags:
---
&emsp;&emsp;标准库函数的默认输出设备是显示器，要实现在串口或`LCD`输出，必须重定义标准库函数里与输出设备相关的函数。例如`printf`输出到串口，需要将`fputc`里面的输出指向串口(重定向)。只要添加一个`int fputc(int ch, FILE *f)`函数，能够输出字符就可以了：

``` c
#ifdef __GNUC__
    /* With GCC/RAISONANCE, small printf (option LD Linker -> Libraries
       -> Small printf set to 'Yes') calls __io_putchar() */
    #define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
    #define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif /* __GNUC__ */
​
PUTCHAR_PROTOTYPE {
    /* Place your implementation of fputc here */
    /* e.g. write a character to the USART */
    USART_SendData ( USART1, ( uint8_t ) ch );
    /* Loop until the end of transmission */
    while ( USART_GetFlagStatus ( USART1, USART_FLAG_TC ) == RESET );
​
    return ch;
}
```

`printf`之类的函数使用了半主机模式，使用标准库会导致程序无法运行，以下是解决方法：

- 使用微库。因为使用微库的话，就不会使用半主机模式。如果使用的是`MDK`，请在工程属性的`Target`->`Code Generation`中勾选`Use MicroLIB`，这样就可以使用`printf`、`sprintf`等函数了。
- 仍然使用标准库，在主程序添加如下代码：

``` c
/* 因为不使用半主机，标准C库stdio.h中有些使用半主机模式的函数要重新写，必须为这些函数提供自己的实现 */
#pragma import(__use_no_semihosting) /* 确保没有从C库链接使用半主机的函数 */
_sys_exit ( int x ) { /* 定义“_sys_exit”，以避免使用半主机模式 */
    x = x;
}
​
struct __FILE { /* 标准库需要的支持函数 */
    int handle;
};
​
FILE __stdout;
```

&emsp;&emsp;为确保没有从`C`库链接使用半主机模式的函数，必须导入符号`__use_no_semihosting`。可在工程的任何`C`或汇编语言源文件中执行此操作，如下所示：
&emsp;&emsp;1. 在`C`语言模块中，使用`#pragma`指令：

``` c
#pragma import(__use_no_semihosting)
```

&emsp;&emsp;2. 在汇编语言模块中，使用`IMPORT`指令：

``` c
IMPORT __use_no_semihosting
```

如果仍然链接了使用半主机模式的函数，链接器则会报告错误。