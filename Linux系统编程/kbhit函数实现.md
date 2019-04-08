---
title: kbhit函数实现
date: 2018-12-29 16:41:19
tags:
---
&emsp;&emsp;编写过`MS-DOS`程序的人通常都会查找`Linux`下等同于`kbhit`的函数，这个函数会检测一个按键是否被按下而并不实际的读取。不幸的是他们并没有找到这样的函数，因为并没有直接等同的函数。`Unix`程序员并不会注意到这个遗漏，因为`Unix`的编程方式通常为程序应准备好等待事件的发生。当我们要移植`MS-DOS`程序时，通常需要模拟`kbhit`，此时我们可以用非正规输入模式来实现。

``` c
#include <stdio.h>
#include <termios.h>
#include <term.h>
#include <curses.h>
#include <unistd.h>
#include <stdlib.h>
​
static struct termios initial_settings, new_settings;
static int peek_character = -1;
​
void init_keyboard();
void close_keyboard();
int kbhit();
int readch();
​
int main() {
    int ch = 0;
    init_keyboard();
​
    while ( ch != 'q' ) {
        printf ( "looping\n" );
        sleep ( 1 );
​
        if ( kbhit() ) {
            ch = readch();
            printf ( "you hit %c\n", ch );
        }
    }
​
    close_keyboard();
    exit ( 0 );
}
​
void init_keyboard() {
    tcgetattr ( 0, &initial_settings );
    new_settings = initial_settings;
    new_settings.c_lflag &= ~ICANON;
    new_settings.c_lflag &= ~ECHO;
    new_settings.c_lflag &= ~ISIG;
    new_settings.c_cc[VMIN] = 1;
    new_settings.c_cc[VTIME] = 0;
    tcsetattr ( 0, TCSANOW, &new_settings );
}
​
void close_keyboard() {
    tcsetattr ( 0, TCSANOW, &initial_settings );
}
​
int readch() {
    char ch;
​
    if ( peek_character != -1 ) {
        ch = peek_character;
        peek_character = -1;
        return ch;
    }
​
    read ( 0, &ch, 1 );
    return ch;
}
​
int kbhit() {
    char ch;
    int nread;
​
    if ( peek_character != -1 ) {
        return 1;
    }
​
    new_settings.c_cc[VMIN] = 0;
    tcsetattr ( 0, TCSANOW, &new_settings );
    nread = read ( 0, &ch, 1 );
    new_settings.c_cc[VMIN] = 1;
    tcsetattr ( 0, TCSANOW, &new_settings );
​
    if ( nread == 1 ) {
        peek_character = ch;
        return 1;
    }
​
    return 0;
}
```

在`init_keyboard`中，配置终端在返回之前(`MIN = 1`，`TIME = 0`)读取一个字符，`kbhit`将其改变为检测输入并且立即返回(`MIN = 0`，`TIME = 0`)，然后在程序退出前恢复原始设置。