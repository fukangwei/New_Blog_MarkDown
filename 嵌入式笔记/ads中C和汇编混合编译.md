---
title: ads中C和汇编混合编译
categories: 嵌入式笔记
date: 2019-01-18 15:49:35
---
&emsp;&emsp;在稍大规模的嵌入式程序设计中，大部分的代码都是用`C`来编写的，主要是因为`C`语言具有较强的结构性，便于人的理解，并且具有大量的库支持。但对于一些硬件上的操作，很多地方还是要用到汇编语言，例如硬件系统的初始化中的`CPU`状态的设定、中断的使能、主频的设定、`RAM`控制参数等。另外在一些对性能非常敏感的代码块，基于汇编与机器码一一对应的关系，这时不能依靠`C`编译器的生成代码，而要手工编写汇编，从而达到优化的目的。汇编语言是和`CPU`的指令集紧密相连的，作为涉及底层的嵌入式系统开发，熟练对应汇编语言的使用也是必须的。单纯的`C`或者汇编编程请参考相关的书籍或者手册，这里主要讨论`C`和汇编的混合编程，包括相互之间的函数调用。<!--more-->

### 在C语言中内嵌汇编

&emsp;&emsp;在`C`语言中内嵌的汇编指令包含大部分的`ARM`和`Thumb`指令，不过使用与单纯的汇编程序使用的指令略有不同，存在一些限制，主要有下面几个方面：

- 不能直接向`PC`寄存器赋值，程序跳转要使用`B`或者`BL`指令。
- 在使用物理寄存器时，不要使用过于复杂的`C`表达式，避免物理寄存器冲突。
- `R12`和`R13`可能被编译器用来存放中间编译结果，计算表达式值时可能把`R0`至`R3`、`R12`及`R14`用于子程序调用，因此避免直接使用这些物理寄存器。
- 一般不要直接指定物理寄存器。
- 让编译器进行分配内嵌汇编使用的标记是`__asm`或`asm`关键字，用法为`__asm{instruction [; instruction]}`或`asm("instruction [; instruction]")`。

``` cpp
#include <stdio.h>

void my_strcpy ( const char *src, char *dest ) {
    char ch;
    __asm{
        loop:
            ldrb ch, [src], #1
            strb ch, [dest], #1
            cmp ch, #0
            bne loop
    }
}

int main() {
    char *a = "forget it and move on!";
    char b[64];
    my_strcpy ( a, b );
    printf ( "original: %s", a );
    printf ( "copyed: %s", b );
    return 0;
}
```

在此例中，`C`语言和汇编之间的值传递是用`C`语言的指针来实现的，因为指针对应的是地址，所以汇编中也可以访问。

### 在汇编中使用C定义的全局变量

&emsp;&emsp;内嵌汇编不用单独编辑汇编语言文件，比较简洁，但是有很多的限制。当汇编的代码较多时，一般放在单独的汇编文件中，这时就需要在汇编文件和`C`文件之间进行一些数据的传递，最简便的办法就是使用全局变量。
&emsp;&emsp;`C`语言文件如下：

``` cpp
#include <stdio.h>

int gVar = 12;
extern asmDouble ( void );

int main() {
    printf ( "original value of gVar is: %d", gVar_1 );
    asmDouble();
    printf ( " modified value of gVar is: %d", gVar_1 );
    return 0;
}
```

&emsp;&emsp;汇编语言文件如下：

``` cpp
    AREA asmfile, CODE, READONLY

    EXPORT asmDouble
    IMPORT gVar
asmDouble
        ldr r0, =gVar
        ldr r1, [r0]
        mov r2, #2
        mul r3, r1, r2
        str r3, [r0]
        mov pc, lr
        END
```

在此例中，汇编文件与`C`文件之间相互传递了全局变量`gVar`和函数`asmDouble`，留意声明的关键字`extern`和`IMPORT`。

### 在C中调用汇编的函数

&emsp;&emsp;有一些对机器要求高的敏感函数，通过`C`语言编写再通过`C`编译器翻译有时会出现误差，因此这样的函数一般采用汇编语言来编写，然后供`C`语言调用。在`C`文件中调用汇编文件中的函数，要注意的有两点，一是要在`C`文件中声明所调用的汇编函数原型，并加入`extern`关键字作为引入函数的声明；二是在汇编文件中对对应的汇编代码段标识用`EXPORT`关键字作为导出函数的声明，函数通过`mov pc, lr`指令返回，这样就可以在`C`文件中使用该函数了。从`C`语言的角度的角度，并不知道调用的函数的实现是用`C`语言还是汇编语言，原因`C`语言的函数名起到了表明函数代码起始地址的作用，而这个作用和汇编语言的代码段标识符是一致的。
&emsp;&emsp;`C`语言文件如下：

``` cpp
#include <stdio.h>

extern void asm_strcpy ( const char *src, char *dest );

int main() {
    const char *s = "seasons in the sun";
    char d[32];
    asm_strcpy ( s, d );
    printf ( "source: %s", s );
    printf ( " destination: %s", d );
    return 0;
}
```

&emsp;&emsp;汇编语言文件如下：

``` cpp
    AREA asmfile, CODE, READONLY
    EXPORT asm_strcpy
asm_strcpy
    loop:
        ldrb r4, [r0], #1
        cmp r4, #0
        beq over
        strb r4, [r1], #1
        b loop
    over:
        mov pc, lr
        END
```

在此例中，`C`语言和汇编语言之间的参数传递是通过对应的用`R0`至`R3`来进行传递，即`R0`传递第一个参数，`R1`传递第二个参数，多于`4`个时借助栈完成，函数的返回值通过`R0`来传递。这个规定叫作`ATPCS(ARM Thumb Procedure Call Standard)`，具体见`ATPCS`规范。

### 在汇编中调用C的函数

&emsp;&emsp;在汇编语言中调用`C`语言的函数，需要在汇编中`IMPORT`对应的`C`函数名，然后将`C`的代码放在一个独立的`C`文件中进行编译，剩下的工作由连接器来处理。下面是一个汇编语言调用`C`语言函数例子：
&emsp;&emsp;`C`语言文件如下：

``` cpp
int cFun ( int a, int b, int c ) {
    return a + b + c;
}
```

&emsp;&emsp;汇编语言文件如下：

``` cpp
    AREA asmfile, CODE, READONLY
    IMPORT cFun
start
    mov r0, #0x1
    mov r1, #0x2
    mov r2, #0x3
    bl cFun ; 如果希望返回，则用BL；否则用B
    nop
    nop
    b start
    END
```

在汇编语言中调用`C`语言的函数，参数的传递也是按照`ATPCS`规范来实现的。在这里简单介绍一下部分`ATPCS`规范：

- 子程序间通过寄存器`R0`至`R3`来传递参数。参数不超过`4`个时，可以使用寄存器`R0`至`R3`来传递参数；当参数超过`4`个时，还可以使用数据栈来传递参数。
- 在子程序中，使用寄存器`R4`至`R11`来保存局部变量。
- 寄存器`R12`用于子程序间`scratch`寄存器(用于保存`SP`，在函数返回时使用该寄存器出栈)，记作`IP`。
- 寄存器`R13`用于数据栈指针，记作`SP`，在进入子程序时的值和退出子程序时的值必须相等。
- 寄存器`R14`称为链接寄存器，记作`LR`，用于保存子程序的返回地址。
- 寄存器`R15`是程序计数器，记作`PC`。
- 结果为一个`32`位整数时，可以通过寄存器`R0`返回。
- 结果为一个`64`位整数时，可以通过寄存器`R0`和`R1`返回，依次类推。