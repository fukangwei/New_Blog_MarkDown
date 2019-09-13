---
title: curses库使用
date: 2019-02-25 14:02:21
categories: Linux应用笔记
---
### curses术语和概念

&emsp;&emsp;`stdscr`是指`curses`窗口，它与显示器的物理屏幕的尺寸完全一样。`curses`函数库用两个数据结构来映射终端屏幕，即`stdscr`和`curscr`。

- `stdscr`：对应的是`标准屏幕`，它的工作方式与`stdout`相似，是`curses`程序中的默认输出窗口。
- `curscr`：对应的是`当前屏幕`，在程序调用`refresh`函数之前，输出到`stdscr`上的内容不会显示在屏幕上。`curses`函数库会在`refresh`函数被调用时比较`stdscr`与第二个数据结构`curscr`之间的不同之处，然后用这两个数据结构之间的差异来刷新屏幕。

&emsp;&emsp;逻辑屏幕的布局通过一个字符数组来实现，它以屏幕的左上角即坐标`(0, 0)`为起点，通过行号和列号来组织。所有`cueses`函数使用的坐标都是`y`值(行号)在前，`x`值(列号)在后。每个位置不仅包含该屏幕处的字符，还包含它的属性(粗体、下划线、反白显示、色彩)。

``` cpp
#include <unistd.h>
#include <stdlib.h>
#include <ncurses.h>
​
int main() {
    initscr();
    move ( 5, 15 );
    printw ( "%s", "Hello World" );
    refresh();
    sleep ( 2 );
    endwin();
    exit ( EXIT_SUCCESS );
}
```

由于`curses`函数库在使用时需要创建和删除一些临时的数据结构，所以所有的`curses`程序必须在开始使用`curses`函数库之前对其进行初始化，并在结束使用后允许`curses`恢复原先设置。这两项工作由`initscr`和`endwin`函数分别完成。

### 屏幕

&emsp;&emsp;`initscr`函数与`endwin`函数：所有的`curses`程序必须以`initscr`函数开始，以`endwin`函数结束。`initscr`函数原型如下：

``` cpp
#include <curses.h>
WINDOW *initscr ( void );
```

如果成功，它返回一个指向`stdscr`结构的指针；如果失败，输出一条诊断错误信息并使程序退出。`initscr`函数在一个程序中只能调用一次。
&emsp;&emsp;`endwin`函数原型如下：

``` cpp
int endwin ( void );
```

调用成功时返回`OK`，失败时返回`ERR`。
&emsp;&emsp;输出到屏幕：`curses`函数库提供了一些用于刷新屏幕的基本函数。`chtype`等同于`unsigned long`，由`typedef`定义。

``` cpp
#include <curses.h>

int addch ( const chtype char_to_add ); /* 在光标的当前位置添加指定的字符 */
int addchstr ( chtype *const string_to_add ); /* 在光标的当前位置添加指定的字符串 */
int printw ( char *format, ... ); /* 对字符串进行格式化，并将其添加到光标的当前位置 */
int refresh ( void ); /* 刷新物理屏幕，成功时返回OK，错误时返回ERR */
int box ( WINDOW *win_ptr, chtype vertical_char, chtype horizontal_char ); /* 用来围绕一个窗口绘制方框 */
int insch ( chtype char_to_insert ); /* 插入一个字符，将已有的字符向右移 */
int insertIn ( void ); /* 插入一个空白行，将现有行依次向下移一行 */
int delch ( void ); /* 删除一个字符，将已有的字符向左移 */
int deleteIn ( void ); /* 删除一行，将现有行向上移一行 */
int beep ( void ); /* 在终端上发出声音 */
int flash ( void ); /* 使屏幕闪烁 */
```

&emsp;&emsp;清除屏幕：清除屏幕上的某个区域主要有`4`种方法：

``` cpp
#include <curses.h>
int erase ( void ); /* 在每个屏幕位置写上空白字符 */
int clear ( void ); /* 用于清屏，但在下次调用refresh函数时重现屏幕原文 */
int clrtobot ( void ); /* 清除当前光标位置直到屏幕结尾的所有内容 */
int clrtoeol ( void ); /* 清除当前光标位置直到光标所处行行尾的所有内容 */
```

&emsp;&emsp;`move`函数：用来将逻辑光标的位置移到指定地点，屏幕坐标以左上角`(0, 0)`为起点。并且有两个包含物理屏幕尺寸大小的外部整数`LINES`和`COLUMNS`，它们可用于决定参数`new_y`和`new_x`的最大值。

``` cpp
#include <curses.h>
int move ( int new_y, int new_x );
```

调用`move`函数本身并不会使物理光标移动，它仅改变逻辑屏幕上的光标位置，下次的输出内容就将出现在该位置上。如果希望物理屏幕上的光标位置在调用`move`函数之后立刻有变化，则需在它之后立刻调用`refresh`函数
&emsp;&emsp;`leaveok`函数：设置一个标志，该标志用于控制在屏幕刷新后`curses`将物理光标放置的位置：

``` cpp
#include <curses.h>
int leaveok ( WINDOW *window_ptr, bool leave_flag );
```

默认情况下，该标志为`false`，意为屏幕刷新后，硬件光标将停留在屏幕上逻辑光标所处的位置；如果该标志设置为`true`，则硬件光标会被随机地放置在屏幕上的任意位置。一般来说，默认选项更符合用户的需求，这能确保光标停留在一个有意义的位置。
&emsp;&emsp;字符属性：每个`curses`字符都可以有一些属性用于控制该字符在屏幕上的显示方式，前提是用于显示的硬件设备能够支持要求的属性。预定义的属性有`A_BLINK`、`A_BOLD`、`A_DIM`、`A_REVERSE`、`A_STANDOUT`和`A_UNDERLINE`。

属性           | 说明
---------------|------
`A_NORMAL`     | Normal display (no highlight)
`A_STANDOUT`   | Best highlighting mode of the terminal
`A_UNDERLINE`  | Underlining
`A_REVERSE`    | Reverse video
`A_BLINK`      | Blinking
`A_DIM`        | Half bright
`A_BOLD`       | Extra bright or bold
`A_PROTECT`    | Protected mode
`A_INVIS`      | Invisible or blank mode
`A_ALTCHARSET` | Alternate character set

函数如下：

``` cpp
#include <curses.h>

int attron ( chtype attribute ); /* 启用指定的属性 */
int attroff ( chtype attribute ); /* 关闭指定的属性 */
int attrset ( chtype attribute ); /* 设置curses属性 */
int standout ( void ); /* 提供了一种强调或“突出”模式，在大多数终端上，通常被映射为反白显示 */
```

示例代码如下：

``` cpp
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <curses.h>
​
int main() {
    const char witch_one[] = " First Witch  ";
    const char witch_two[] = " Second Witch  ";
    const char *scan_ptr;
    initscr();
    move ( 5, 15 );
    attron ( A_BOLD );
    printw ( "%s", "Macbeth" );
    attroff ( A_BOLD );
    refresh();
    sleep ( 1 );
    move ( 8, 15 );
    attron ( A_STANDOUT );
    printw ( "%s", "Thunder and Lightning" );
    attroff ( A_STANDOUT );
    refresh();
    sleep ( 1 );
    move ( 10, 10 );
    printw ( "%s", "When shall we three meet again" );
    move ( 11, 23 );
    printw ( "%s", "In thunder, lightning, or in rain?" );
    move ( 13, 10 );
    printw ( "%s", "When the hurlyburly's done," );
    move ( 14, 23 );
    printw ( "%s", "When the battle's lost and won." );
    refresh();
    sleep ( 1 );
    attron ( A_DIM );
    scan_ptr = witch_one + strlen ( witch_one ) - 1;
​
    while ( scan_ptr != witch_one ) {
        move ( 10, 10 );
        insch ( *scan_ptr-- );
    }
​
    scan_ptr = witch_two + strlen ( witch_two ) - 1;
​
    while ( scan_ptr != witch_two ) {
        move ( 13, 10 );
        insch ( *scan_ptr-- );
    }
​
    attroff ( A_DIM );
    refresh();
    sleep ( 1 );
    move ( LINES - 1, COLS - 1 );
    refresh();
    sleep ( 1 );
    endwin();
    exit ( EXIT_SUCCESS );
}
```

输出效果：先在第`5`行第`15`列的位置输出`Macbeth`加粗，再在第`8`行第`15`列的位置输出`Thunder and Lightning`加亮。之后很短的间隔，在第`10`行第`10`列的位置输出`When shall we three meet again`，在第`11`行第`23`列的位置同时输出`In thunder, lightning, or in rain?`，在第`13`行第10列的位置同时输出`When the hurlyburly's done,`，在第`14`行第`23`列的位置同时输出`When the battle's lost and won.`。再经过很短的间隔，在第一句和第三句前分别加上两个数组的内容。在第`10`行第`10`列的位置插入`(space)First Witch(space)(space)`，在第`13`行第`10`列的位置同时插入`(space)Second Witch(space)(space)`。

### 键盘

&emsp;&emsp;输入字符的回显：键盘读取例程由键盘模式控制。用于设置键盘模式的函数有：

``` cpp
#include <curses.h>
int echo ( void );
int noecho ( void );
```

用于开启或关闭输入字符的回显功能。键盘模式如下：

- `cooked`：所有处理是基于行的。
- `cbreak`：字符一经键入，就立刻被传送给程序。

以下函数用于控制在终端上输入的字符传送给`curses`程序的方式：

``` cpp
int cbreak ( void );
int nocbreak ( void );
```

当`curses`程序通过调用`initscr`函数开始运行时，输入模式被设置为预处理模式(或称`cooked`模式)。这意味着所有处理都是基于行的，也就是说，只有在用户按下回车键后，输入的数据才会被传送给程序。在该模式下，键盘特殊字符被启用，所以按下合适的组合键即可在程序中产生一个信号。程序可通过调用`cbreak`函数将输入模式设置为`cbreak`模式，在这种模式下，字符一经键入就立刻被传递给程序，在此模式下，键盘特殊字符也被启用。但一些简单的字符，如`Backspace`会直接传递给程序处理，所以如果想让退格键保底原来的功能，就必须自己在程序中实现它。
&emsp;&emsp;关闭特殊字符的处理：

``` cpp
int raw ( void );
int noraw ( void );
```

`raw`函数调用的作用是关闭特殊字符的处理，所以执行该函数调用后，再想通过输入特殊字符序列来产生信号或进行流控就不可能了。`noraw`函数调用同时恢复`cooked`模式和特殊字符处理模式。
&emsp;&emsp;键盘输入：

``` cpp
#include <curses.h>
int getch ( void );
int getstr ( char *string );
int getnstr ( char *string, int number_of_characters );
int scanw ( char *format, ... );
```

类似于`getchar`、`gets`和`scanf`，但要注意的是，`getstr`函数对其返回的字符串的长度没有限制，要尽可能使用`getnstr`来代替`getstr`。

``` cpp
#include <unistd.h>
#include <stdlib.h>
#include <curses.h>
#include <string.h>
​
#define PW_LEN 256
#define NAME_LEN 256
​
int main() {
    char name[NAME_LEN];
    char password[PW_LEN];
    const char *real_password = "xyzzy";
    int i = 0;

    initscr();
    move ( 5, 10 );
    printw ( "%s", "Please login:" );
    move ( 7, 10 );
    printw ( "%s", "User name: " );
    getstr ( name );
    move ( 8, 10 );
    printw ( "%s", "Password: " );
    refresh();
    cbreak();
    noecho();
    memset ( password, '\0', sizeof ( password ) );
​
    while ( i < PW_LEN ) {
        password[i] = getch();
​
        if ( password[i] == '\n' ) {
            break;
        }
​
        move ( 8, 20 + i );
        addch ( '*' );
        refresh();
        i++;
    }
​
    echo();
    nocbreak();
    move ( 11, 10 );
​
    if ( strncmp ( real_password, password, strlen ( real_password ) ) == 0 ) {
        printw ( "%s", "Correct" );
    } else {
        printw ( "%s", "Wrong" );
    }
​
    printw ( "%s", " password" );
    refresh();
    sleep ( 2 );
    endwin();
    exit ( EXIT_SUCCESS );
}
```

在第`5`行第`10`列的位置输出`Please login:`，在第`7`行第`10`列的位置输出`User name:(space)`。输入用户名(随意命名)，然后回车。在第`8`行第`10`列的位置输出`Password:(space)`，然后输入密码；若密码不为`xyzzy`，在第`11`行第`10`列的位置输出`Wrong password`，有背景填充；若相符，则在第`11`行第`10`列的位置输出`Correct password`，无背景。

### 窗口

&emsp;&emsp;以上程序一直将终端作为一个全屏幕的输出介质，但curses函数库在物理屏幕上可同时显示多个不同尺寸的窗口。
&emsp;&emsp;`WINDOW`结构：标准屏幕`stdscr`只是`WINDOW`结构的一个特例，就像标准输出`stdout`是文件流的一个特例一样。可以用函数调用`newwin`和`delwin`来创建和销毁窗口。`newwin`函数原型如下：

``` cpp
#include <curses.h>
WINDOW *newwin ( int num_of_lines, int num_of_cols, int start_y, intstart_x );
```

该窗口从屏幕位置`(start_y, start_x)`开始，行数和列数分别由参数`num_of_lines`和`num_of_cols`指定。如果想让新窗口的右下角正好落在屏幕的右下角上，可以将函数的行、列参数设为`0`。该函数返回一个指向新窗口的指针，如果新窗口创建失败则返回`null`。
&emsp;&emsp;注意，所有的窗口范围都必须在当前屏幕范围之内，如果新窗口的任何部分落在当前屏幕范围之外，则`newwin`函数调用失败；通过`newwin`函数创建的新窗口完全独立于所有已存在的窗口。默认情况下，它被放置在任何已有窗口之上，覆盖(但不是改变)它们的内容。
&emsp;&emsp;`delwin`函数原型如下：

``` cpp
int delwin ( WINDOW *window_to_delete );
```

因为调用`newwin`函数可能会给新窗口分配内存，所以当不再需要这些窗口时，不要忘记通过`delwin`函数将其删除。注意，不要删除`curses`自己的窗口`stdscr`和`curscr`。

### 通用函数

&emsp;&emsp;在本章中以前学过的函数都可以加上一些前缀变为通用函数。前缀`w`用于窗口，但必须在该函数的参数表的最前面增加一个`WINDOW`指针参数；前缀`mv`用于光标移动，但需要在函数的参数表的最前面增加两个参数，分别是纵坐标`y`和横坐标`x`，这两个坐标值指定了执行操作的位置。坐标值`y`和`x`是相对于窗口而不是相对于屏幕的，坐标`(0, 0)`代表窗口的左上角；前缀`mvw`用于在窗口中移动光标，但需要传递`3`个参数，即一个`WINDOW`指针、`y`和`x`坐标值，并且`WINDOW`指针参数总是出现在屏幕坐标值之前。

``` cpp
#include <curses.h>
int addch ( const chtype char );
int waddch ( WINDOW *window_pointer, const chtype char );
int mvaddch ( int y, int x, const chtype char );
int mvwaddch ( WINDOW *window_pointer, int y, int x, const chtype char );
int printw ( char *format, ... );
int wprintw ( WINDOW *window_pointer, char *format, ... );
int mvprintw ( int, intx, char *format, ... );
int mvwprintw ( WINDOW *window_pointer, int y, int x, char *format, ... );
```

### 移动和更新窗口

&emsp;&emsp;通过以下函数，可以移动和重新绘制窗口：

``` cpp
#include <curses.h>
int mvwin ( WINDOW *window_to_move, int new_y, int new_x );
```

因为不允许窗口的任何部分超出屏幕范围，所以如果在调用`mvwin`函数时，将窗口的某个部分移动到屏幕区域之外，`mvwin`函数调用将会失败。

``` cpp
int wrefresh ( WINDOW *window_ptr );
int wclear ( WINDOW *window_ptr );
int werase ( WINDOW *window_ptr );
int touchwin ( WINDOW *window_ptr;
```

通过`curses`函数库，其指针参数指向的窗口内容已发生改变。这就意味着在下次调用`wrefresh`函数时，`curses`必须重新绘制该窗口，即使用户实际上并未修改该窗口中的内容。当屏幕上重叠着多个窗口时，可以通过该函数来安排要显示的窗口。

``` cpp
int scrollok ( WINDOW *window_ptr, bool scroll_flag );
```

该函数控制着窗口的卷屏。如果传递给`scrollok`函数的是布尔值`true`(通常是非零值)，则允许窗口卷屏。在默认情况下，窗口是不能卷屏的。

``` cpp
int scroll ( WINDOW *window_ptr );
```

该函数是把窗口内容上卷一行。

``` cpp
#include <unistd.h>
#include <stdlib.h>
#include <curses.h>
​
int main() {
    WINDOW *new_window_ptr;
    WINDOW *popup_window_ptr;
    int x_loop;
    int y_loop;
    char a_letter = 'a';

    initscr();
    move ( 5, 5 );
    printw ( "%s", "Testing multiple windows" );
    refresh();
    sleep ( 2 );
​
    for ( x_loop = 0; x_loop < COLS - 1; x_loop++ ) { /* COLS指现在屏幕列数 */
        for ( y_loop = 0; y_loop < LINES - 1; y_loop++ ) { /* LINES指现在屏幕行数 */
            mvwaddch ( stdscr, y_loop, x_loop, a_letter );
            a_letter++;
​
            if ( a_letter > 'z' ) {
                a_letter = 'a';
            }
        }
    }
​
    refresh();
    sleep ( 5 );
    new_window_ptr = newwin ( 10, 20, 5, 5 );
    mvwprintw ( new_window_ptr, 2, 2, "%s", "Hello World" );
    mvwprintw ( new_window_ptr, 5, 2, "%s", "Notice howvery long lines wrap inside the window" );
    wrefresh ( new_window_ptr );
    sleep ( 2 );
    a_letter = '0';
​
    for ( x_loop = 0; x_loop < COLS - 1; x_loop++ ) {
        for ( y_loop = 0; y_loop < LINES - 1; y_loop++ ) {
            mvwaddch ( stdscr, y_loop, x_loop, a_letter );
            a_letter++;
​
            if ( a_letter > '9' ) {
                a_letter = '0';
            }
        }
    }
​
    refresh();
    sleep ( 2 );
    wrefresh ( new_window_ptr );
    sleep ( 2 );
    touchwin ( new_window_ptr );
    wrefresh ( new_window_ptr );
    sleep ( 2 );
    popup_window_ptr = newwin ( 10, 20, 8, 8 );
    box ( popup_window_ptr, '|', '-' );
    mvwprintw ( popup_window_ptr, 5, 2, "%s", "Pop UpWindow!" );
    wrefresh ( popup_window_ptr );
    sleep ( 2 );
    touchwin ( new_window_ptr );
    wrefresh ( new_window_ptr );
    sleep ( 2 );
    wclear ( new_window_ptr );
    wrefresh ( new_window_ptr );
    sleep ( 2 );
    delwin ( new_window_ptr );
    touchwin ( popup_window_ptr );
    wrefresh ( popup_window_ptr );
    sleep ( 2 );
    delwin ( popup_window_ptr );
    touchwin ( stdscr );
    refresh();
    sleep ( 2 );
    endwin();
    exit ( EXIT_SUCCESS );
}
```

`newwin(10, 20, 5, 5)`参数的含义如下：

- `10`：`num_of_lines`，窗口的行数为`10`行。
- `20`：`num_of_cols`，窗口的列数为`20`列。
- `5`：`start_y`，该窗口从屏幕位置第`5`行开始。
- `5`：`start_x`，该窗口从屏幕位置第`5`列开始。

&emsp;&emsp;为什么要使用`touchwin`？因为通过`curses`函数库其指针参数指向的窗口内容已发生改变。这就意味着在下次调用`wrefresh`函数时，`curses`必须重新绘制该窗口，即使用户实际上并未修改该窗口中的内容。所以当屏幕上重叠着多个窗口时，可以通过该函数来安排要显示的窗口。

### 优化屏幕刷新

&emsp;&emsp;为了尽量减少需要在屏幕上绘制的字符数目，`curses`函数库为此提供了一种特殊手段，这需要使到下面`2`个函数：

``` cpp
#include <curses.h>
int wnoutrefresh ( WINDOW *window_ptr );
int doupdate ( void );
```

`wnoutrefresh`函数用于决定把哪些字符发送到屏幕上，但它并不真正地发送这些字符，真正将更新发送到终端的工作由`doupdate`函数来完成。如果只是调用`wnoutrefresh`函数，然后立刻调用`doupdate`函数，则它的效果与直接调用`wrefresh`完全一样。
&emsp;&emsp;但如果想重新绘制多个窗口，可以为每个窗口分别调用`wnoutrefresh`函数，然后只需在调用最后一个`wnoutrefresh`之后调用一次`doupdate`函数即可。这允许`curses`依次为每个窗口执行屏幕更新计算工作，最后仅把最终的更新结果输出到屏幕上，这种做法可以最大限度地减少`curses`需要发送的字符数目。

### 子窗口

&emsp;&emsp;子窗口的创建和删除可以用以下几个函数来完成：

``` cpp
#include <curses.h>
WINDOW *subwin ( WINDOW *parent, intnum_of_lines, int num_of cols, int start_y, int start_x );
int delwin ( WINDOW *window_to_delete );
```

&emsp;&emsp;子窗口与新窗口(`newwin`)的重要区别是，子窗口没有自己独立的屏幕字符存储空间，它们与其父窗口(在创建子窗口时指定)共享同一字符存储空间。这意味着，对子窗口中内容的任何修改都会反映到其父窗口中，所以删除子窗口时，屏幕显示不会发生任何变化。
&emsp;&emsp;子窗口的用途是，提供了一种简洁的方式来卷动另一窗口里的部分内容。编写`curses`程序时，经常会需要卷动屏幕的某个小区域。通过将这个小区域定义为一个子窗口，然后对其卷动，就能达到这种效果。

``` cpp
#include <unistd.h>
#include <stdlib.h>
#include <curses.h>
​
int main() {
    WINDOW *sub_window_ptr;
    int x_loop;
    int y_loop;
    int counter;
    char a_letter = '1';

    initscr();
​
    for ( y_loop = 0; y_loop < LINES - 1; y_loop++ ) {
        for ( x_loop = 0; x_loop < COLS - 1; x_loop++ ) {
            mvwaddch ( stdscr, y_loop, x_loop, a_letter );
            a_letter++;
​
            if ( a_letter > '9' ) {
                a_letter = '1';
            }
        }
    }
​
    sub_window_ptr = subwin ( stdscr, 10, 20, 10, 10 );
    scrollok ( sub_window_ptr, 1 );
    touchwin ( stdscr );
    refresh();
    sleep ( 1 );
    werase ( sub_window_ptr );
    mvwprintw ( sub_window_ptr, 2, 0, "%s", "This window willnow scroll" );
    wrefresh ( sub_window_ptr );
    sleep ( 1 );
​
    for ( counter = 1; counter < 10; counter++ ) {
        wprintw ( sub_window_ptr, "%s", "This text is both wrappingand scrolling." );
        wrefresh ( sub_window_ptr );
        sleep ( 1 );
    }
​
    delwin ( sub_window_ptr );
    touchwin ( stdscr );
    refresh();
    sleep ( 1 );
    endwin();
    exit ( EXIT_SUCCESS );
}
```

&emsp;&emsp;简要描述子窗口的处理过程：

1. `subwin`创建子窗口并且指定与其父窗口共享同一字符存储空间，子窗口只占一个指定的小区域。提供了一种简洁的方式来卷动另一窗口里的部分内容。允许子窗口卷屏，卷动其所在屏幕的某个小区域。
2. 利用`touchwin`函数来安排要显示的窗口，使子窗口在指定位置输出指定字符，更新间隔很短。
3. `delwin`删除已显示过的子窗口，删除子窗口时，屏幕显示不会发生任何变化。

### keypad模式

&emsp;&emsp;键盘上的方向键、功能键及数字小键盘等按键会发送以`escape`字符开头的字符串序列。`curses`函数库提供了一个用于管理功能键的功能。头文件`curses.h`通过一组以`KEY_`为前缀的定义来管理逻辑键。`Keypad`模式是指按键转义序列，此时读键盘操作不仅能够返回用户按下的键，还将返回与逻辑按键对应的`KEY_`定义(在`curses.h`头文件中定义)。

``` cpp
#define KEY_CODE_YES    0400         /* A wchar_t contains a key code */
#define KEY_MIN         0401         /* Minimum curses key */
#define KEY_BREAK       0401         /* Break key (unreliable) */
#define KEY_SRESET      0530         /* Soft (partial) reset(unreliable) */
#define KEY_RESET       0531         /* Reset or hard reset(unreliable)*/
#define KEY_DOWN        0402         /* down-arrow key */
#define KEY_UP          0403         /* up-arrow key */
#define KEY_LEFT        0404         /* left-arrow key */
#define KEY_RIGHT       0405         /* right-arrow key */
#define KEY_HOME        0406         /* home key */
#define KEY_BACKSPACE   0407         /* backspace key */
#define KEY_F0          0410         /* Function keys.  Space for 64 */
#define KEY_F(n)        (KEY_F0+(n)) /* Value of function key n */
#define KEY_DL          0510         /* delete-line key */
#define KEY_IL          0511         /* insert-line key */
#define KEY_DC          0512         /* delete-character key */
#define KEY_IC          0513         /* insert-character key */
#define KEY_EIC         0514         /* sent by rmir or smir in insertmode */
#define KEY_CLEAR       0515         /* clear-screen or erase key */
#define KEY_EOS         0516         /* clear-to-end-of-screen key */
#define KEY_EOL         0517         /* clear-to-end-of-line key */
#define KEY_SF          0520         /* scroll-forward key */
#define KEY_SR          0521         /* scroll-backward key */
#define KEY_NPAGE       0522         /* next-page key */
#define KEY_PPAGE       0523         /* previous-page key */
#define KEY_STAB        0524         /* set-tab key */
#define KEY_CTAB        0525         /* clear-tab key */
#define KEY_CATAB       0526         /* clear-all-tabs key */
#define KEY_ENTER       0527         /* enter/send key */
#define KEY_PRINT       0532         /* print key */
#define KEY_LL          0533         /* lower-left key (home down) */
#define KEY_A1          0534         /* upper left of keypad */
#define KEY_A3          0535         /* upper right of keypad */
#define KEY_B2          0536         /* center of keypad */
#define KEY_C1          0537         /* lower left of keypad */
#define KEY_C3          0540         /* lower right of keypad */
#define KEY_BTAB        0541         /* back-tab key */
#define KEY_BEG         0542         /* begin key */
#define KEY_CANCEL      0543         /* cancel key */
#define KEY_CLOSE       0544         /* close key */
#define KEY_COMMAND     0545         /* command key */
#define KEY_COPY        0546         /* copy key */
#define KEY_CREATE      0547         /* create key */
#define KEY_END         0550         /* end key */
#define KEY_EXIT        0551         /* exit key */
#define KEY_FIND        0552         /* find key */
#define KEY_HELP        0553         /* help key */
#define KEY_MARK        0554         /* mark key */
#define KEY_MESSAGE     0555         /* message key */
#define KEY_MOVE        0556         /* move key */
#define KEY_NEXT        0557         /* next key */
#define KEY_OPEN        0560         /* open key */
#define KEY_OPTIONS     0561         /* options key */
#define KEY_PREVIOUS    0562         /* previous key */
#define KEY_REDO        0563         /* redo key */
#define KEY_REFERENCE   0564         /* reference key */
#define KEY_REFRESH     0565         /* refresh key */
#define KEY_REPLACE     0566         /* replace key */
#define KEY_RESTART     0567         /* restart key */
#define KEY_RESUME      0570         /* resume key */
#define KEY_SAVE        0571         /* save key */
#define KEY_SBEG        0572         /* shifted begin key */
#define KEY_SCANCEL     0573         /* shifted cancel key */
#define KEY_SCOMMAND    0574         /* shifted command key */
#define KEY_SCOPY       0575         /* shifted copy key */
#define KEY_SCREATE     0576         /* shifted create key */
#define KEY_SDC         0577         /* shifted delete-character key */
#define KEY_SDL         0600         /* shifted delete-line key */
#define KEY_SELECT      0601         /* select key */
#define KEY_SEND        0602         /* shifted end key */
#define KEY_SEOL        0603         /* shifted clear-to-end-of-linekey*/
#define KEY_SEXIT       0604         /* shifted exit key */
#define KEY_SFIND       0605         /* shifted find key */
#define KEY_SHELP       0606         /* shifted help key */
#define KEY_SHOME       0607         /* shifted home key */
#define KEY_SIC         0610         /* shifted insert-character key */
#define KEY_SLEFT       0611         /* shifted left-arrow key */
#define KEY_SMESSAGE    0612         /* shifted message key */
#define KEY_SMOVE       0613         /* shifted move key */
#define KEY_SNEXT       0614         /* shifted next key */
#define KEY_SOPTIONS    0615         /* shifted options key */
#define KEY_SPREVIOUS   0616         /* shifted previous key */
#define KEY_SPRINT      0617         /* shifted print key */
#define KEY_SREDO       0620         /* shifted redo key */
#define KEY_SREPLACE    0621         /* shifted replace key */
#define KEY_SRIGHT      0622         /* shifted right-arrow key */
#define KEY_SRSUME      0623         /* shifted resume key */
#define KEY_SSAVE       0624         /* shifted save key */
#define KEY_SSUSPEND    0625         /* shifted suspend key */
#define KEY_SUNDO       0626         /* shifted undo key */
#define KEY_SUSPEND     0627         /* suspend key */
#define KEY_UNDO        0630         /* undo key */
#define KEY_MOUSE       0631         /* Mouse event has occurred */
#define KEY_RESIZE      0632         /* Terminal resize event */
#define KEY_EVENT       0633         /* We were interrupted by an event*/
#define KEY_MAX         0777         /* Maximum key value is 0633 */
```

`curses`在启动时会关闭转义序列与逻辑键之间的转换功能，该功能需要通过`keypad`函数来启用：

``` cpp
#include <curses.h>
int keypad ( WINDOW *window_ptr, bool keypad_on );
```

将`keypad_on`参数设置为`true`，然后调用`keypad`函数来启用`keypad`模式。函数调用成功时，返回`OK`；调用失败时，返回`ERR`。
&emsp;&emsp;几种特殊字符(以`escape`开头的转义序列)如下：

- `END`：键盘`End`键。
- `BEGINNING`：`begin`键(没有)。
- `RIGHT`：向右的方向键。
- `LEFT`：向左的方向键。
- `UP`：向上的方向键。
- `DOWN`：向下的方向键。

### 彩色显示

&emsp;&emsp;`curses`只能以一种非常有限的方式来使用色彩，屏幕上的每个字符位置都有它的前景色或背景色。但`curses`对字符颜色的定义及其背景色的定义并不能完全独立。必须同时定义一个字符的前景色与背景色，将它们称之为颜色组合(`color pair`)。
&emsp;&emsp;`curses`颜色例程初始化：

``` cpp
#include <curses.h>
bool has_colors ( void ); /* 如果终端支持颜色，该函数将返回true */
int start_color ( void ); /* 若该函数成功初始化了颜色显示功能，它将返回OK */
```

一旦`start_color`函数被成功调用，变量`COLOR_PAIRS`将被设置为终端所能支持的颜色组合数目的最大值，一般为`64`(`8 * 8`)。变量`COLORS`定义可用颜色数目的最大值，一般只有`8`种。在内部实现中，每种可用的颜色以一个从`0`到`63`的数字作为其唯一的`ID`号。
&emsp;&emsp;对使用的颜色组合进行初始化：在把颜色作为属性使用之前，必须首先调用`init_pair`函数对准备使用的颜色组合进行初始化，对颜色属性的访问是通过`COLOR_PAIR`函数来完成的。

``` cpp
#include <curses.h>
int init_pair ( short pair_number, short foreground, short background ); /* 初始化颜色对 */
int COLOR_PAIR ( int pair_number );
int pair_content ( short pair_number, short *foreground, short *background ); /* 获取已定义颜色信息 */
```

彩色显示代码如下：

``` cpp
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <curses.h>
​
int main() {
    int i;
    initscr();
​
    if ( !has_colors() ) {
        endwin();
        fprintf ( stderr, "Error - no color support on this terminal\n" );
        exit ( 1 );
    }
​
    if ( start_color() != OK ) {
        endwin();
        fprintf ( stderr, "Error - could not initialize colors\n" );
        exit ( 2 );
    }
​
    clear();
    mvprintw ( 5, 5, "There are %d COLORS, and %d COLOR_PAIRS available", \
               COLORS, COLOR_PAIRS );
    refresh();
    init_pair ( 1, COLOR_RED, COLOR_BLACK );
    init_pair ( 2, COLOR_RED, COLOR_GREEN );
    init_pair ( 3, COLOR_GREEN, COLOR_RED );
    init_pair ( 4, COLOR_YELLOW, COLOR_BLUE );
    init_pair ( 5, COLOR_BLACK, COLOR_WHITE );
    init_pair ( 6, COLOR_MAGENTA, COLOR_BLUE );
    init_pair ( 7, COLOR_CYAN, COLOR_WHITE );
​
    for ( i = 1; i <= 7; i++ ) {
        attroff ( A_BOLD );
        attrset ( COLOR_PAIR ( i ) );
        mvprintw ( 5 + i, 5, "Color pair %d", i );
        attrset ( COLOR_PAIR ( i ) | A_BOLD );
        mvprintw ( 5 + i, 25, "Bold color pair %d", i );
        refresh();
        sleep ( 1 );
    }
​
    endwin();
    exit ( EXIT_SUCCESS );
}
```

### pad

&emsp;&emsp;`Pad`指一个尺寸大于物理屏幕的逻辑屏幕，可一次只显示该逻辑屏幕的某个部分。在一般情况下，逻辑屏幕不允许大于物理屏幕。`Pad`结构非常类似于`WINDOW`结构，所有执行写窗口操作的`curses`函数同样可用于`pad`。
&emsp;&emsp;创建`pad`：创建`pad`的方式与创建正常窗口的方式基本相同：

``` cpp
#include <curses.h>
WINDOW *newpad ( int number_of_lines, int number_of_colums );
```

`pad`用`delwin`函数来删除，这与正常的窗口的删除一样：

``` cpp
int delwin ( WINDOW  *window_to_delete );
```

&emsp;&emsp;`pad`刷新：`Pad`使用不同的函数执行刷新操作。因为一个`pad`并不局限于某个特定的屏幕位置，所以必须指定希望放到屏幕上的`pad`范围及其放置在屏幕上的位置。

``` cpp
#include <curses.h>
​
int prefresh (
    *WINDOW *pad_ptr,
    int pad_row,
    int pad_column,
    int screen_row_min,
    int screen_col_min,
    int screen_row_max,
    int screen_col_max
);
```

函数作用是将`pad`从坐标`(pad_row, pad_colum)`开始的区域写到屏幕上指定的显示区域，该显示区域的范围从坐标`(screen_row_min, screen_col_min)`到`(screen_row_max, screen_col_max)`。
&emsp;&emsp;`pad`的使用示例如下：

``` cpp
#include <unistd.h>
#include <stdlib.h>
#include <curses.h>
​
int main() {
    WINDOW *pad_ptr;
    int x, y;
    int pad_lines;
    int pad_cols;
    char disp_char;

    initscr();
    /* 定义长度和宽度，大于屏幕 */
    pad_lines = LINES + 50;
    pad_cols = COLS + 50;
    pad_ptr = newpad ( pad_lines, pad_cols );
    disp_char = 'a';
​
    for ( x = 0; x < pad_lines; x++ ) {
        for ( y = 0; y < pad_cols; y++ ) {
            mvwaddch ( pad_ptr, x, y, disp_char );
​
            if ( disp_char == 'z' ) {
                disp_char = 'a';
            } else {
                disp_char++;
            }
        }
    }
​
    /* 将pad的不同区域绘制到屏幕的不同位置上，结束程序 */
    prefresh ( pad_ptr, 5, 7, 2, 2, 9, 9 );
    sleep ( 1 );
    prefresh ( pad_ptr, LINES + 5, COLS + 7, 5, 5, 21, 19 );
    sleep ( 1 );
    delwin ( pad_ptr );
    endwin();
    exit ( EXIT_SUCCESS );
}
```