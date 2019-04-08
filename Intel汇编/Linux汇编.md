---
title: Linux汇编
date: 2018-12-16 18:05:16
tags:
---

### 显示字符串(AT&T汇编)

&emsp;&emsp;代码如下所示：

``` c
SECTION .data ; 包含已初始化的数据的段

EatMsg: db "Eat at Joe's!", 10
EatLen: equ $-EatMsg

SECTION .bss  ; 包含未始化的数据的段
SECTION .text ; 包含代码的段

global _start ; 连接器需要据此找到入口点
_start:
    nop ; 这个无操作指令让gdb非常高兴
    mov eax, 4 ; 指定sys_write系统调用
    mov ebx, 1 ; 指定文件描述符，1是标准输出
    mov ecx, EatMsg ; 传递显示信息的偏移地址
    mov edx, EatLen ; 传递显示信息的长度
    int 80h ; 进行系统调用来输出文本到标准输出
    mov eax, 1 ; 指定Exit系统调用
    mov ebx, 0 ; 返回一个零代码
    int 80h ; 进行系统调用来终止程序
```

### 字符串大写字母转换(AT&T汇编)

&emsp;&emsp;代码如下所示：

``` c
.section .data
string1:
    .asciz "This is a TEST, of the conversion program!\n"
length:
    .int 43

.section .text
.globl main
main:
    nop
    leal string1, %esi
    movl %esi, %edi
    movl length, %ecx
    cld

loop1:
    lodsb
    cmpb $'a', %al
    jl skip
    cmpb $'z', %al
    jg skip
    subb $0x20, %al

skip:
    stosb
    loop loop1

end:
    pushl $string1
    call printf
    addl $4, %esp
    pushl $0
    call exit
```

### 显示数组(AT&T汇编)

&emsp;&emsp;代码如下所示：

``` c
.section .data
output:
    .asciz "The value is %d\n"
values:
    .int 10, 15,  20, 25, 30, 35, 40, 45, 50, 55, 60

.section .text
.globl main

main:
    nop # 加上“nop”是为了方便调试
    movl $0, %edi

loop:
    movl values(, %edi, 4), %eax
    pushl %eax
    pushl $output
    call printf

    addl $8, %esp
    inc %edi
    cmpl $11, %edi
    jne loop
    movl $0, %ebx
    movl $1, %eax
    int $0x80
```

### 在数组中找出最大值(AT&T汇编)

&emsp;&emsp;代码如下所示：

``` c
.section .data
output:
    .asciz "The largest value is %d\n"
values:
    .int 105, 235, 61, 315, 134, 221, 53, 145, 117, 5

.section .text
.globl main

main:
    nop # 加上“nop”是为了方便调试
    movl values, %ebx
    movl $1, %edi

loop:
    movl values(, %edi, 4), %eax
    cmp %ebx, %eax
    cmova %eax, %ebx
    inc %edi
    cmp $10, %edi
    jne loop

    pushl %ebx
    pushl $output
    call printf
    addl $8, %esp
    pushl $0
    call exit
```

### 使用系统调用(AT&T汇编)

&emsp;&emsp;代码如下所示：

``` c
.section .data
output:
    .ascii "This is a test message.\n"
output_end:
    .equ len, output_end - output

.section .text
.globl main
main:
    movl $4, %eax
    movl $1, %ebx
    movl $output, %ecx
    movl $len, %edx
    int $0x80

    movl $1, %eax
    movl $0, %ebx
    int $0x80
```

### 反向遍历数据数组(AT&T汇编)

&emsp;&emsp;代码如下所示：

``` c
.section .data
value:
    .int 21, 15, 34, 11, 6, 50, 32, 80, 10, 2
output:
    .asciz "The value is %d\n"

.section .text
.globl main

main:
    movl $9, %edi
loop:
    pushl value(, %edi, 4)
    pushl $output
    call printf
    addl $8, %esp
    dec %edi
    jns loop

    movl $1, %eax
    movl $0, %ebx
    int $0x80
```

### 使用call命令调用函数(AT&T汇编)

&emsp;&emsp;代码如下所示：

``` c
.section .data
output:
    .asciz "This is section %d\n"

.section .text
.globl main

main:
    pushl $1
    pushl $output
    call printf
    add $8, %esp # should clean up stack
    call overhere
    pushl $3
    pushl $output
    call printf
    add $8, %esp # should clean up stack
    pushl $0
    call exit

overhere:
    pushl %ebp
    movl %esp, %ebp

    pushl $2
    pushl $output
    call printf
    add $8, %esp # should clean up stack

    movl %ebp, %esp
    popl %ebp
    ret
```

### 两个64位数值的相减(AT&T汇编)

&emsp;&emsp;代码如下所示：

``` c
.section .data
data1:
    .quad 7252051615
data2:
    .quad 5732348928
output:
    .asciz "The result is %qd\n"

.section .text
.globl main

main:
    nop
    movl data1, %ebx
    movl data1+4, %eax
    movl data2, %edx
    movl data2+4, %ecx
    subl %ebx, %edx
    sbbl %eax, %ecx

    pushl %ecx
    pushl %edx
    push $output
    call printf
    addl $12, %esp
    pushl $0
    call exit
```