---
title: 解析C语言复杂声明
categories: C语言应用代码
date: 2019-03-12 12:28:47
---
&emsp;&emsp;代码如下(`unix`的`dcl`程序)：<!--more-->

``` cpp
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define MAXTOKEN 100

enum { NAME, PARENS, BRACKETS };

void dcl ( void );
void dirdcl ( void );
int gettoken ( void );
int tokentype; /* 最后一个记号的类型 */
char token[MAXTOKEN]; /* 最后一个记号字符串 */
char name[MAXTOKEN]; /* 标识符名 */
char datatype[MAXTOKEN]; /* 数据类型为char、int等 */
char out[1000]; /* 输出串 */

int main ( void ) { /* 将声明转换为文字描述 */
    while ( gettoken() != EOF ) {
        /* 该行的第一个记号是数据类型 */
        strcpy ( datatype, token ); /* is the datatype */
        out[0] = '\0';
        dcl(); /* 分析该行的其余部分 */

        if ( tokentype != '\n' ) {
            printf ( "syntax error\n" );
        }

        printf ( "%s: %s %s\n", name, out, datatype );
    }

    return 0;
}

/* gettoken用来跳过空格和制表符，以查找输入中的下一个记号。记号(token)可以是一个名字，
   一对圆括号，可能包含一个数字的一对括号，也可以是其他任何单个字符 */
int gettoken ( void ) { /* 返回下一个标记 */
    int c, getch ( void );
    void ungetch ( int );
    char *p = token;

    while ( ( c = getch() ) == ' ' || c == '\t' )
        ;

    if ( c == '(' ) {
        if ( ( c = getch() ) == ')' ) {
            strcpy ( token, "()" );
            return tokentype = PARENS;
        } else {
            ungetch ( c );
            return tokentype = '(';
        }
    } else if ( c == '[' ) {
        for ( *p++ = c; ( *p++ = getch() ) != ']'; )
            ;

        *p = '\0';
        return tokentype = BRACKETS;
    } else if ( isalpha ( c ) ) {
        for ( *p++ = c; isalnum ( c = getch() ); ) {
            *p++ = c;
        }

        *p = '\0';
        ungetch ( c );
        return tokentype = NAME;
    } else {
        return tokentype = c;
    }
}

void dcl ( void ) { /* 对一个声明符进行语法分析 */
    int ns;

    for ( ns = 0; gettoken() == '*'; ) { /* 统计字符“*”的个数 */
        ns++;
    }

    dirdcl();

    while ( ns-- > 0 ) {
        strcat ( out, " pointer to" );
    }
}

void dirdcl ( void ) { /* 分析一个直接声明 */
    int type;

    if ( tokentype == '(' ) { /* 形式为(dcl) */
        dcl();

        if ( tokentype != ')' ) {
            printf ( "error: missing )\n" );
        }
    } else if ( tokentype == NAME ) { /* 变量名 */
        strcpy ( name, token );
    } else {
        printf ( "error: expected name or (dcl)\n" );
    }

    while ( ( type = gettoken() ) == PARENS || type == BRACKETS )
        if ( type == PARENS ) {
            strcat ( out, " function returning" );
        } else {
            strcat ( out, " array" );
            strcat ( out, token );
            strcat ( out, " of" );
        }
}

#define BUFSIZE 100

char buf[BUFSIZE]; /* buffer for ungetch */

int bufp = 0; /* next free position in buf */

int getch ( void ) { /* 取出一个字符(可能是压回的字符) */
    return ( bufp > 0 ) ? buf[--bufp] : getchar();
}

void ungetch ( int c ) { /* 把字符压回到输入中 */
    if ( bufp >= BUFSIZE ) {
        printf ( "ungetch: too many characters\n" );
    } else {
        buf[bufp++] = c;
    }
}
```

&emsp;&emsp;程序`dcl`是基于声明符的语法编写的，下面是其简化的语法形式：

``` cpp
dcl:        前面带有可选的“*”的direct-dcl
direct-dcl: name
            (dcl)
            direct-dcl()
            direct-dcl[可选的长度]
```

简而言之，声明符`dcl`就是前面可能带有多个`*`的`direct-dcl`。`direct-dcl`可以是`name`、由一对圆括号括起来的`dcl`、后面跟有一对圆括号的`direct-dcl`、后面跟有用方括号括起来的表示可选长度的`direct-dcl`。
&emsp;&emsp;例如分析`(* pfa[])()`，按该语法分析，`pfa`将被识别为一个`name`，从而被认为是一个`direct-dcl`。于是`pfa[]`也是一个`direct-dcl`。接着，`* pfa[]`被识别为一个`dcl`，因此判定`(* pfa[])`是一个`direct-dcl`。再接着，`(* pfa[])()`被识别为一个`direct-dcl`，因此也是一个`dcl`。该过程如下：

<div align="center">

``` mermaid
graph TD
    A1["("]
    A2["*"]
    A3[pfa]
    A4["[]"]
    A5[")"]
    A6["()"]

    B1["name"]
    C1["dir-dcl"]
    D1["dir-dcl"]
    E1["dcl"]
    F1["dir-dcl"]
    G1["dir-dcl"]
    H1["dcl"]

    A1------>F1
    A2----->E1
    A3-->B1-->C1-->D1-->E1-->F1-->G1-->H1
    A4---->D1
    A5------>F1
    A6------->G1
```

</div>