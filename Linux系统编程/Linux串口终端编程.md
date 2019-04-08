---
title: Linux串口终端编程
date: 2019-02-03 20:52:30
tags:
---
### 串口概述

&emsp;&emsp;用户常见的数据通信的基本方式有两种：并行通信和串行通信。串行通信是计算机常用的接口，如`RS-232`接口。该标准规定采用一个`DB25`芯引脚连接器或`DB9`芯引脚连接器。芯片内部常具有`UART`控制器，其可工作于`Interrupt`(中断模式)或`DMA`(直接内存访问)模式。
&emsp;&emsp;`UART`的操作主要包括以下几个部分：数据发送、数据接收、产生中断、产生波特率、`Loopback`模式、红外模式和自动流控模式。串口参数的配置主要包括：波特率、数据位、停止位、流控协议。
&emsp;&emsp;`linux`中的串口设备文件存放于`/dev`目录下，其中串口一、串口二对应设备名依次为`/dev/ttyS0`、`/dev/ttyS1`。在`Linux`下操作串口与操作文件相同。
&emsp;&emsp;在使用串口之前必须设置相关配置，包括波特率、数据位、校验位、停止位等。串口设置由下面结构体实现：

``` cpp
struct termios {
    unsigned short c_iflag; /* 输入模式标志 */
    unsigned short c_oflag; /* 输出模式标志 */
    unsigned short c_cflag; /* 控制模式标志 */
    unsigned short c_lflag; /* 本地模式标志 */
    unsigned char c_line; /* 线路规程 */
    unsigned char c_cc[NCC]; /* 控制特性 */
    speed_t c_ispeed; /* 输入速度 */
    speed_t c_ospeed; /* 输出速度 */
};
```

该结构中`c_cflag`最为重要，可设置波特率、数据位、校验位、停止位。在设置波特率时需在数字前加上`B`，如`B9600`、`B19200`。

### 串口控制函数

&emsp;&emsp;函数如下：

函数名        | 作用
--------------|------------
`tcgetattr`   | 取属性(`termios`结构)
`tcsetattr`   | 设置属性(`termios`结构)
`cfgetispeed` | 得到输入速度
`cfgetospeed` | 得到输出速度
`cfsetispeed` | 设置输入速度
`cfsetospeed` | 设置输出速度
`tcdrain`     | 等待所有输出都被传输
`tcflow`      | 挂起传输或接收
`tcflush`     | 刷清未决输入和/或输出
`tcsendbreak` | 送`BREAK`字符
`tcgetpgrp`   | 得到前台进程组`ID`
`tcsetpgrp`   | 设置前台进程组`ID`

### 串口配置流程

&emsp;&emsp;1. 保存原先串口配置使用`tcgetattr(fd, &oldtio)`函数：

``` cpp
struct termios newtio, oldtio;
tcgetattr ( fd, &oldtio );
```

&emsp;&emsp;2. 激活选项有`CLOCAL`和`CREAD`，用于本地连接和接收使能：

``` cpp
newtio.c_cflag |= CLOCAL | CREAD;
```

&emsp;&emsp;3. 设置波特率，使用函数`cfsetispeed`、`cfsetospeed`：

``` cpp
cfsetispeed ( &newtio, B115200 );
cfsetospeed ( &newtio, B115200 );
```

&emsp;&emsp;4. 设置数据位，需使用掩码设置：

``` cpp
newtio.c_cflag &= ~CSIZE;
newtio.c_cflag |= CS8;
```

&emsp;&emsp;5. 设置奇偶校验位，使用`c_cflag`和`c_iflag`。设置奇校验如下：

``` cpp
newtio.c_cflag |= PARENB;
newtio.c_cflag |= PARODD;
newtio.c_iflag |= ( INPCK | ISTRIP );
```

设置偶校验如下：

``` cpp
newtio.c_iflag |= ( INPCK | ISTRIP );
newtio.c_cflag |= PARENB;
newtio.c_cflag &= ~PARODD;
```

&emsp;&emsp;6. 设置停止位，通过激活`c_cflag`中的`CSTOPB`实现。若停止位为`1`，则清除`CSTOPB`；若停止位为`2`，则激活`CSTOPB`。

``` cpp
newtio.c_cflag &= ~CSTOPB;
```

&emsp;&emsp;7. 设置最少字符和等待时间，对于接收字符和等待时间没有特别要求时，可设为`0`。

``` cpp
newtio.c_cc[VTIME] = 0;
newtio.c_cc[VMIN] = 0;
```

&emsp;&emsp;8. 处理要写入的引用对象：`tcflush`函数刷清(抛弃)输入缓存(终端驱动程序已接收到，但用户程序尚未读)或输出缓存(用户程序已经写，但尚未发送)。

``` cpp
int tcflush ( int filedes, int queue );
```

`queue`应当是下列三个常数之一：

- `TCIFLUSH`：刷清输入队列。
- `TCOFLUSH`：刷清输出队列。
- `TCIOFLUSH`：刷清输入、输出队列。

&emsp;&emsp;9. 在完成配置后，需激活配置使其生效，需要使用`tcgetattr`和`tcsetattr`函数：

``` cpp
int tcgetattr ( int filedes, struct termios *termptr );
int tcsetattr ( int filedes, int opt, const struct termios *termptr );
```

`tcsetattr`的参数`opt`可以指定在什么时候新的终端属性才起作用。`opt`可以指定为下列常数中的一个：

- `TCSANOW`：更改立即发生。
- `TCSADRAIN`：发送了所有输出后更改才发生。若更改输出参数则应使用此选择项。
- `TCSAFLUSH`：发送了所有输出后更改才发生。更进一步，在更改发生时未读的所有输入数据都被删除(刷清)。

使用示例：

``` cpp
tcsetattr ( fd, TCSANOW, &newtio );
```

### 串口使用详解

&emsp;&emsp;在配置完串口的相关属性后，就可对串口进行打开，读写操作了。其使用方式与文件操作一样，区别在于串口是一个终端设备。
&emsp;&emsp;打开串口使用如下代码：

``` cpp
fd = open ( "/dev/ttyS0", O_RDWR | O_NOCTTY | O_NDELAY );
```

- O_NOCTTY：通知linux系统，这个程序不会成为这个端口的控制终端。
- O_NDELAY：通知linux系统不关心DCD信号线所处的状态(端口的另一端是否激活或者停止)。

然后恢复串口的状态为阻塞状态，用于等待串口数据的读入。需要使用`fcntl`函数：

``` cpp
fcntl ( fd, F_SETFL, 0 );
```

接着测试打开的文件描述符是否引用一个终端设备，以进一步确认串口是否正确打开：

``` cpp
isatty ( STDIN_FILENO );
```

串口的读写与普通文件一样，使用`read`、`write`函数：

``` cpp
read ( fd, buff, 8 );
write ( fd, buff, 8 );
```

使用示例如下：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <termios.h>
#include <errno.h>
​
#define FALSE -1
#define TRUE  0
​
int speed_arr[] = {
    B38400, B19200, B9600, B4800, B2400, B1200, B300,
    B38400, B19200, B9600, B4800, B2400, B1200, B300,
};
​
int name_arr[] = {
    38400, 19200, 9600, 4800, 2400, 1200, 300,
    38400, 19200, 9600, 4800, 2400, 1200, 300,
};
​
void set_speed ( int fd, int speed ) {
    int i;
    int status;
    struct termios Opt;
    tcgetattr ( fd, &Opt );
​
    for ( i = 0; i < sizeof ( speed_arr ) / sizeof ( int ); i++ ) {
        if ( speed == name_arr[i] ) {
            tcflush ( fd, TCIOFLUSH );
            cfsetispeed ( &Opt, speed_arr[i] );
            cfsetospeed ( &Opt, speed_arr[i] );
            status = tcsetattr ( fd, TCSANOW, &Opt );
​
            if ( status != 0 ) {
                perror ( "tcsetattr fd1" );
                return;
            }
​
            tcflush ( fd, TCIOFLUSH );
        }
    }
}
​
int set_Parity ( int fd, int databits, int stopbits, int parity ) {
    struct termios options;
​
    if ( tcgetattr ( fd, &options ) != 0 ) {
        perror ( "SetupSerial 1" );
        return ( FALSE );
    }
​
    options.c_cflag &= ~CSIZE;
​
    switch ( databits ) {
        case 7:
            options.c_cflag |= CS7;
            break;
​
        case 8:
            options.c_cflag |= CS8;
            break;
​
        default:
            fprintf ( stderr, "Unsupported data size\n" );
            return ( FALSE );
    }
​
    switch ( parity ) {
        case 'n':
        case 'N':
            options.c_cflag &= ~PARENB; /* Clear parity enable */
            options.c_iflag &= ~INPCK; /* Enable parity checking */
            break;
​
        case 'o':
        case 'O':
            options.c_cflag |= ( PARODD | PARENB );
            options.c_iflag |= INPCK; /* Disnable parity checking */
            break;
​
        case 'e':
        case 'E':
            options.c_cflag |= PARENB; /* Enable parity */
            options.c_cflag &= ~PARODD;
            options.c_iflag |= INPCK; /* Disnable parity checking */
            break;
​
        case 'S':
        case 's': /* as no parity */
            options.c_cflag &= ~PARENB;
            options.c_cflag &= ~CSTOPB;
            break;
​
        default:
            fprintf ( stderr, "Unsupported parity\n" );
            return ( FALSE );
    }
​
    switch ( stopbits ) {
        case 1:
            options.c_cflag &= ~CSTOPB;
            break;
​
        case 2:
            options.c_cflag |= CSTOPB;
            break;
​
        default:
            fprintf ( stderr, "Unsupported stop bits\n" );
            return ( FALSE );
    }
​
    if ( parity != 'n' ) { /* Set input parity option */
        options.c_iflag |= INPCK;
    }
​
    tcflush ( fd, TCIFLUSH );
    options.c_cc[VTIME] = 150;
    options.c_cc[VMIN] = 0; /* Update the options and do it NOW */
​
    if ( tcsetattr ( fd, TCSANOW, &options ) != 0 ) {
        perror ( "SetupSerial 3" );
        return ( FALSE );
    }
​
    return ( TRUE );
}
​
int main ( void ) {
    printf ( "This program updates last time at %s  %s\n", __TIME__, __DATE__ );
    printf ( "STDIO COM1\n" );
    int fd;
    fd = open ( "/dev/ttyUSB0", O_RDWR ); /* 我用的是USB转串口设备 */
​
    if ( fd == -1 ) {
        perror ( "serialport error\n" );
    } else {
        printf ( "open " );
        printf ( "%s", ttyname ( fd ) );
        printf ( " succesfully!\n" );
    }
​
    set_speed ( fd, 9600 );
​
    if ( set_Parity ( fd, 8, 1, 'N' ) == FALSE ) {
        printf ( "Set Parity Error\n" );
        exit ( 0 );
    }
​
    char buf[] = "fe55aa07bc010203040506073d";
    write ( fd, &buf, 26 );
    char buff[512];
    int nread;
​
    while ( 1 ) {
        if ( ( nread = read ( fd, buff, 512 ) ) > 0 ) {
            printf ( "\nLen: %d\n", nread );
            buff[nread + 1] = '\0';
            printf ( "%s", buff );
        }
    }
​
    close ( fd );
    return 0;
}
```

---

&emsp;&emsp;串口的设置主要是设置`struct termios`结构体的各成员值，`termios`是在`Posix`规范中定义的标准接口，表示终端设备(包括虚拟终端、串口等)。因为串口是一种终端设备，所以通过终端编程接口对其进行配置和控制。
&emsp;&emsp;终端是指用户与计算机进行对话的接口，如键盘、显示器和串口设备等物理设备，`X Window`上的虚拟终端。类`UNIX`操作系统都有文本式虚拟终端，使用`Ctrl + Alt + F1~F6`键可以进入文本式虚拟终端，在`X Window`上可以打开几十个以上的图形式虚拟终端。类`UNIX`操作系统的虚拟终端有`xterm`、`rxvt`、`zterm`、`eterm`等，而`Windows`上有`crt`、`putty`等虚拟终端。
&emsp;&emsp;终端有三种工作模式，分别为规范模式(`canonical mode`)、非规范模式(`non-canonical mode`)和原始模式(`raw mode`)。通过在`termios`结构的`c_lflag`中设置`ICANNON`标志来定义终端是以规范模式(设置`ICANNON`标志)还是以非规范模式(清除`ICANNON`标志)工作，默认情况为规范模式。
&emsp;&emsp;在规范模式下，所有的输入是基于行进行处理的。在用户输入一个行结束符(回车符、`EOF`等)之前，系统调用`read`函数是读不到用户输入的任何字符的。除了`EOF`之外的行结束符(回车符等)与普通字符一样会被`read`函数读取到缓冲区中。在规范模式中，行编辑是可行的，而且一次调用`read`函数最多只能读取一行数据。如果在`read`函数中被请求读取的数据字节数小于当前行可读取的字节数，则`read`函数只会读取被请求的字节数，剩下的字节下次再被读取。
&emsp;&emsp;在非规范模式下，所有的输入是即时有效的，不需要用户另外输入行结束符，而且不可进行行编辑。在非规范模式下，对参数`MIN(c_cc[VMIN])`和`TIME(c_cc[VTIME])`的设置决定`read`函数的调用方式。设置可以有`4`种不同的情况。

- `MIN = 0`和`TIME = 0`：`read`函数立即返回。若有可读数据，则读取数据并返回被读取的字节数，否则读取失败并返回`0`。
- `MIN > 0`和`TIME = 0`：`read`函数会被阻塞，直到`MIN`个字节数据可被读取。
- `MIN = 0`和`TIME > 0`：只要有数据可读或者经过`TIME`个十分之一秒的时间，`read`函数则立即返回，返回值为被读取的字节数。如果超时并且未读到数据，则`read`函数返回`0`。
- `MIN > 0`和`TIME > 0`：当有`MIN`个字节可读或者两个输入字符之间的时间间隔超过`TIME`个十分之一秒时，`read`函数才返回。因为在输入第一个字符后系统才会启动定时器，所以在这种情况下，`read`函数至少读取一个字节后才返回。

&emsp;&emsp;按照严格意义来讲，原始模式是一种特殊的非规范模式。在原始模式下，所有的输入数据以字节为单位被处理。在这个模式下，终端是不可回显的，而且所有特定的终端输入/输出控制处理不可用。通过调用`cfmakeraw`函数可以将终端设置为原始模式，而且该函数通过以下代码可以得到实现：

``` cpp
termios_p->c_iflag &= ~ ( IGNBRK | BRKINT | PARMRK | ISTRIP | \
                          INLCR | IGNCR | ICRNL | IXON );
termios_p->c_oflag &= ~OPOST;
termios_p->c_lflag &= ~ ( ECHO | ECHONL | ICANON | ISIG | IEXTEN );
termios_p->c_cflag &= ~ ( CSIZE | PARENB );
termios_p->c_cflag |= CS8;
```

&emsp;&emsp;现在讲解设置串口的基本方法。如上所述，串口设置最基本的操作包括波特率设置、校验位和停止位设置。在这个结构中最为重要的是`c_cflag`，通过对它的赋值，用户可以设置波特率、字符大小、数据位、停止位、奇偶校验位和硬软流控等。另外，`c_iflag`和`c_cc`也是比较常用的标志。在此主要对这`3`个成员进行详细说明。`c_cflag`支持的常量名称如下表所示。其中设置波特率宏名为相应的波特率数值前加上`B`，由于数值较多，本表没有全部列出。

参数      | 说明
----------|-----
`CBAUD`   | 波特率的位掩码
`B0`      | `0`波特率(放弃`DTR`)
`CBAUD`   | 波特率的位掩码
`B1800`   | `1800`波特率
`B2400`   | `2400`波特率
`B4800`   | `4800`波特率
`B9600`   | `9600`波特率
`B19200`  | `19200`波特率
`B38400`  | `38400`波特率
`B57600`  | `57600`波特率
`EXTA`    | 外部时钟率
`EXTB`    | 外部时钟率
`CSIZE`   | 数据位的位掩码
`CS5`     | `5`个数据位
`CS6`     | `6`个数据位
`CS7`     | `7`个数据位
`CS8`     | `8`个数据位
`CSTOPB`  | `2`个停止位(不设则是`1`个停止位)
`CREAD`   | 接收使能
`PARENB`  | 校验位使能
`PARODD`  | 使用奇校验而不使用偶校验
`HUPCL`   | 最后关闭时挂线(放弃`DTR`)
`CLOCAL`  | 本地连接(不改变端口所有者)
`CRTSCTS` | 硬件流控

&emsp;&emsp;在这里，对于`c_cflag`成员不能直接对其初始化，而要将其通过`与`、`或`操作使用其中的某些选项。输入模式标志`c_iflag`用于控制端口接收端的字符输入处理。`c_iflag`支持的常量名称如下：

参数      | 说明
----------|-----
`INPCK`   | 奇偶校验使能
`IGNPAR`  | 忽略奇偶校验错误
`PARMRK`  | 奇偶校验错误掩码
`ISTRIP`  | 裁减掉第`8`位比特
`IXON`    | 启动输出软件流控
`IXOFF`   | 启动输入软件流控
`IXANY`   | 允许输入任意字符可以重新启动输出(默认为输入起始字符才重启输出)
`IGNBRK`  | 忽略输入终止条件
`BRKINT`  | 当检测到输入终止条件时发送`SIGINT`信号
`INLCR`   | 将接收到的`NL`(换行符)转换为`CR`(回车符)
`IGNCR`   | 忽略接收到的`CR`(回车符)
`ICRNL`   | 将接收到的`CR`(回车符)转换为`NL`(换行符)
`IUCLC`   | 将接收到的大写字符映射为小写字符
`IMAXBEL` | 当输入队列满时响铃

&emsp;&emsp;`c_oflag`用于控制终端端口发送出去的字符处理，`c_oflag`支持的常量名称如下表所示。因为现在终端的速度比以前快得多，所以大部分延时掩码几乎没什么用途。

参数     | 说明
---------|-----
`OPOST`  | 启用输出处理功能，如果不设置该标志则其他标志都被忽略
`OLCUC`  | 将输出中的大写字符转换成小写字符
`ONLCR`  | 将输出中的换行符`\n`转换成回车符`\r`
`ONOCR`  | 如果当前列号为`0`，则不输出回车符
`OCRNL`  | 将输出中的回车符`\r`转换成换行符`\n`
`ONLRET` | 不输出回车符
`OFILL`  | 发送填充字符以提供延时
`OFDEL`  | 如果设置该标志，则表示填充字符为`DEL`字符，否则为`NUL`字符
`NLDLY`  | 换行符延时掩码
`CRDLY`  | 回车符延时掩码
`TABDLY` | 制表符延时掩码
`BSDLY`  | 水平退格符延时掩码
`VTDLY`  | 垂直退格符延时掩码
`FFLDY`  | 换页符延时掩码

&emsp;&emsp;`c_lflag`用于控制终端的本地数据处理和工作模式，`c_lflag`所支持的常量名称如下：

参数      | 说明
----------|-----
`ISIG`    | 若收到信号字符(`INTR`、`QUIT`等)，则会产生相应的信号
`ICANON`  | 启用规范模式
`ECHO`    | 启用本地回显功能
`ECHOE`   | 若设置`ICANON`，则允许退格操作
`ECHOK`   | 若设置`ICANON`，则`KILL`字符会删除当前行
`ECHONL`  | 若设置`ICANON`，则允许回显换行符
`ECHOCTL` | 若设置`ECHO`，则控制字符(制表符、换行符等)会显示成`^X`，其中`X`的`ASCII`码等于给相应控制字符的`ASCII`码加上`0x40`。例如退格字符`0x08`会显示为`^H`(`H`的`ASCII`码为`0x48`)
`ECHOPRT` | 若设置`ICANON`和`IECHO`，则删除字符(退格符等)和被删除的字符都会被显示
`ECHOKE`  | 若设置`ICANON`，则允许回显在`ECHOE`和`ECHOPRT`中设定的`KILL`字符
`NOFLSH`  | 在通常情况下，当接收到`INTR`、`QUIT`和`SUSP`控制字符时，会清空输入和输出队列。如果设置该标志，则所有的队列不会被清空
`TOSTOP`  | 若一个后台进程试图向它的控制终端进行写操作，则系统向该后台进程的进程组发送`SIGTTOU`信号。该信号通常终止进程的执行
`IEXTEN`  | 启用输入处理功能

&emsp;&emsp;`c_cc`定义特殊控制特性，`c_cc`所支持的常量名称如下：

参数     | 说明
---------|-----
`VINTR`  | 中断控制字符，对应键为`Ctrl + C`
`VQUIT`  | 退出操作符，对应键为`Ctrl + Z`
`VERASE` | 删除操作符，对应键为`Backspace`(`BS`)
`VKILL`  | 删除行符，对应键为`Ctrl + U`
`VEOF`   | 文件结尾符，对应键为`Ctrl + D`
`VEOL`   | 附加行结尾符，对应键为`Carriage return`(`CR`)
`VEOL2`  | 第二行结尾符，对应键为`Line feed`(`LF`)
`VMIN`   | 指定最少读取的字符数
`VTIME`  | 指定读取的每个字符之间的超时时间

&emsp;&emsp;下面就详细讲解设置串口属性的基本流程。
&emsp;&emsp;1. 保存原先串口配置：首先为了安全起见和以后调试程序方便，可以先保存原先串口的配置，在这里可以使用函数`tcgetattr ( fd, &old_cfg );`。该函数得到由`fd`指向的终端的配置参数，并将它们保存于`termios`结构变量`old_cfg`中。该函数还可以测试配置是否正确、该串口是否可用等。若调用成功，函数返回值为`0`；若调用失败，函数返回值为`-1`。

``` cpp
if ( tcgetattr ( fd, &old_cfg ) != 0 ) {
    perror ( "tcgetattr" );
    return -1;
}
```

&emsp;&emsp;2. 激活选项：`CLOCAL`和`CREAD`分别用于本地连接和接收使能，因此首先要通过位掩码的方式激活这两个选项：

``` cpp
newtio.c_cflag |= CLOCAL | CREAD;
```

调用`cfmakeraw`函数可以将终端设置为原始模式，在后面的实例中，采用原始模式进行串口数据通信：

``` cpp
cfmakeraw ( &new_cfg );
```

&emsp;&emsp;3. 设置波特率：设置波特率有专门的函数，用户不能直接通过位掩码来操作。设置波特率的主要函数有`cfsetispeed`和`cfsetospeed`。

``` cpp
cfsetispeed ( &new_cfg, B115200 );
cfsetospeed ( &new_cfg, B115200 );
```

`cfsetispeed`函数在`termios`结构中设置数据输入波特率，而`cfsetospeed`函数在`termios`结构中设置数据输入波特率。一般来说，用户需将终端的输入和输出波特率设置成一样的。这几个函数在成功时返回`0`，失败时返回`-1`。
&emsp;&emsp;4. 设置字符大小：与设置波特率不同，设置字符大小并没有现成可用的函数，需要用位掩码。一般首先去除数据位中的位掩码，再重新按要求设置：

``` cpp
new_cfg.c_cflag &= ~CSIZE; /* 用数据位掩码清空数据位设置 */
new_cfg.c_cflag |= CS8;
```

&emsp;&emsp;5. 设置奇偶校验位：设置奇偶校验位需要用到`termios`中的两个成员：`c_cflag`和`c_iflag`。首先要激活`c_cflag`中的校验位使能标志`PARENB`和确认是否要进行校验，这样会对输出数据产生校验位，而对输入数据进行校验检查。同时还要激活`c_iflag`中的对于输入数据的奇偶校验使能(`INPCK`)。如使能奇校验时，代码如下：

``` cpp
new_cfg.c_cflag |= ( PARODD | PARENB );
new_cfg.c_iflag |= INPCK;
```

而使能偶校验时，代码如下：

``` cpp
new_cfg.c_cflag |= PARENB;
new_cfg.c_cflag &= ~PARODD; /* 清除偶奇校验标志，则配置为偶校验 */
new_cfg.c_iflag |= INPCK;
```

&emsp;&emsp;6. 设置停止位：设置停止位是通过激活`c_cflag`中的`CSTOPB`而实现的。若停止位为一个比特，则清除`CSTOPB`；若停止位为两个，则激活`CSTOPB`。以下分别是停止位为一个和两个比特时的代码：

``` cpp
new_cfg.c_cflag &= ~CSTOPB; /* 将停止位设置为一个比特 */
new_cfg.c_cflag |= CSTOPB; /* 将停止位设置为两个比特 */
```

&emsp;&emsp;7. 设置最少字符和等待时间：在对接收字符和等待时间没有特别要求的情况下，可以将其设置为`0`，则在任何情况下`read`函数立即返回，此时串口操作会设置为非阻塞方式：

``` cpp
new_cfg.c_cc[VTIME] = 0;
new_cfg.c_cc[VMIN] = 0;
```

&emsp;&emsp;8. 清除串口缓冲：由于串口在重新设置后，需要对当前的串口设备进行适当的处理，这时就可调用在`termios.h`中声明的`tcdrain`、`tcflow`、`tcflush`等函数来处理目前串口缓冲中的数据：

``` cpp
int tcdrain ( int fd ); /* 使程序阻塞，直到输出缓冲区的数据全部发送完毕 */
int tcflow ( int fd, int action ); /* 用于暂停或重新开始输出 */
int tcflush ( int fd, int queue_selector ); /* 用于清空输入/输出缓冲区 */
```

在本实例中使用`tcflush`函数，对于在缓冲区中尚未传输的数据，或者收到的但是尚未读取的数据，其处理方法取决于`queue_selector`的值，它可能的取值有以下几种：

- `TCIFLUSH`：对接收到而未被读取的数据进行清空处理。
- `TCOFLUSH`：对尚未传送成功的输出数据进行清空处理。
- `TCIOFLUSH`：包括前两种功能，即对尚未处理的输入/输出数据进行清空处理。

如在本例中所采用的是第一种方法，当然可以使用`TCIOFLUSH`参数：

``` cpp
tcflush ( fd, TCIFLUSH );
```

&emsp;&emsp;9. 激活配置：在完成全部串口配置后，要激活刚才的配置并使配置生效。这里用到的函数是`tcsetattr`：

``` cpp
tcsetattr ( int fd, int optional_actions, const struct termios *termios_p );
```

参数`termios_p`是`termios`类型的新配置变量；参数`optional_actions`可能的取值有以下`3`种：

- `TCSANOW`：配置的修改立即生效。
- `TCSADRAIN`：配置的修改在所有写入`fd`的输出都传输完毕之后生效。
- `TCSAFLUSH`：所有已接收但未读入的输入都将在修改生效之前被丢弃。

该函数若调用成功则返回`0`，若失败则返回`-1`：

``` cpp
if ( ( tcsetattr ( fd, TCSANOW, &new_cfg ) ) != 0 ) {
    perror ( "tcsetattr" );
    return -1;
}
```

&emsp;&emsp;下面给出了串口配置的完整函数。为了函数的通用性，通常将常用的选项都在函数中列出，这样可以大大方便以后用户的调试使用。

``` cpp
int set_com_config ( int fd, int baud_rate, int data_bits, char parity, int stop_bits ) {
    struct termios new_cfg, old_cfg;
    int speed;
​
    /* 保存并测试现有串口参数设置，在这里如果串口号等出错，会有相关的出错信息 */
    if ( tcgetattr ( fd, &old_cfg ) != 0 ) {
        perror ( "tcgetattr" );
        return -1;
    }
​
    new_cfg = old_cfg;
    cfmakeraw ( &new_cfg ); /* 配置为原始模式 */
    new_cfg.c_cflag &= ~CSIZE;
​
    switch ( baud_rate ) { /* 设置波特率 */
        case 2400:
            speed = B2400;
            break;
​
        case 4800:
            speed = B4800;
            break;
​
        case 9600:
            speed = B9600;
            break;
​
        case 19200:
            speed = B19200;
            break;
​
        case 38400:
            speed = B38400;
            break;
​
        case 115200:
            speed = B115200;
            break;
    }
​
    cfsetispeed ( &new_cfg, speed );
    cfsetospeed ( &new_cfg, speed );
​
    switch ( data_bits ) { /* 设置数据位 */
        case 7:
            new_cfg.c_cflag |= CS7;
            break;
​
        case 8:
            new_cfg.c_cflag |= CS8;
            break;
    }
​
    switch ( parity ) { /* 设置奇偶校验位 */
        default:
        case 'n':
        case 'N':
            new_cfg.c_cflag &= ~PARENB;
            new_cfg.c_iflag &= ~INPCK;
            break;
​
        case 'o':
        case 'O':
            new_cfg.c_cflag |= ( PARODD | PARENB );
            new_cfg.c_iflag |= INPCK;
            break;
​
        case 'e':
        case 'E':
            new_cfg.c_cflag |= PARENB;
            new_cfg.c_cflag &= ~PARODD;
            new_cfg.c_iflag |= INPCK;
            break;
​
        case 's': /* as no parity */
        case 'S':
            new_cfg.c_cflag &= ~PARENB;
            new_cfg.c_cflag &= ~CSTOPB;
            break;
    }
​
    switch ( stop_bits ) { /* 设置停止位 */
        case 1:
            new_cfg.c_cflag &= ~CSTOPB;
            break;
​
        case 2:
            new_cfg.c_cflag |= CSTOPB;
            break;
    }
​
    /* 设置等待时间和最小接收字符 */
    new_cfg.c_cc[VTIME] = 0;
    new_cfg.c_cc[VMIN] = 1;
    tcflush ( fd, TCIFLUSH ); /* 处理未接收字符 */
​
    if ( ( tcsetattr ( fd, TCSANOW, &new_cfg ) ) != 0 ) { /* 激活新配置 */
        perror ( "tcsetattr" );
        return -1;
    }
​
    return 0;
}
```

&emsp;&emsp;使用`termios`结构的密码程序如下：

``` cpp
#include <termios.h>
#include <stdio.h>
#include <stdlib.h>
​
#define PASSWORD_LEN 8
​
int main() {
    struct termios initialrsettings, newrsettings;
    char password[PASSWORD_LEN + 1];
    tcgetattr ( fileno ( stdin ), &initialrsettings );
    newrsettings = initialrsettings;
    newrsettings.c_lflag &= ~ECHO;
    printf ( "Enter password: " );
​
    if ( tcsetattr ( fileno ( stdin ), TCSAFLUSH, &newrsettings ) != 0 ) {
        fprintf ( stderr, "Could not set attributes\n" );
    } else {
        fgets ( password, PASSWORD_LEN, stdin );
        tcsetattr ( fileno ( stdin ), TCSANOW, &initialrsettings );
        fprintf ( stdout, "\nYou entered %s\n", password );
    }
​
    exit ( 0 );
}
```

&emsp;&emsp;读取每一个字符的代码如下：

``` cpp
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <termios.h>
​
char *menu[] = {
    "a - add new record",
    "d - delete record",
    "q - quit",
    NULL,
};
​
int getchoice ( char *greet, char *choices[], FILE *in, FILE *out );
​
int main() {
    int choice = 0;
    FILE *input;
    FILE *output;
    struct termios initial_settings, new_settings;
​
    if ( !isatty ( fileno ( stdout ) ) ) {
        fprintf ( stderr, "You are not a terminal, OK.\n" );
    }
​
    input = fopen ( "/dev/tty", "r" );
    output = fopen ( "/dev/tty", "w" );
​
    if ( !input || !output ) {
        fprintf ( stderr, "Unable to open /dev/tty\n" );
        exit ( 1 );
    }
​
    tcgetattr ( fileno ( input ), &initial_settings );
    new_settings = initial_settings;
    new_settings.c_lflag &= ~ICANON;
    new_settings.c_lflag &= ~ECHO;
    new_settings.c_cc[VMIN] = 1;
    new_settings.c_cc[VTIME] = 0;
    new_settings.c_lflag &= ~ISIG;
​
    if ( tcsetattr ( fileno ( input ), TCSANOW, &new_settings ) != 0 ) {
        fprintf ( stderr, "could not set attributes\n" );
    }
​
    do {
        choice = getchoice ( "Please select an action", menu, input, output );
        printf ( "You have chosen: %c\n", choice );
    } while ( choice != 'q' );
​
    tcsetattr ( fileno ( input ), TCSANOW, &initial_settings );
    exit ( 0 );
}
​
int getchoice ( char *greet, char *choices[], FILE *in, FILE *out ) {
    int chosen = 0;
    int selected;
    char **option;
​
    do {
        fprintf ( out, "Choice: %s\n", greet );
        option = choices;
​
        while ( *option ) {
            fprintf ( out, "%s\n", *option );
            option++;
        }
​
        do {
            selected = fgetc ( in );
        } while ( selected == '\n' || selected == '\r' );
​
        option = choices;
​
        while ( *option ) {
            if ( selected == *option[0] ) {
                chosen = 1;
                break;
            }
​
            option++;
        }
​
        if ( !chosen ) {
            fprintf ( out, "Incorrect choice, select again\n" );
        }
    } while ( !chosen );
​
    return selected;
}
```

---

&emsp;&emsp;`setupterm`将当前终端类型设置为参数`term`指向的值，如果`term`是空指针，就使用环境变量`TERM`的值。

``` cpp
#include <term.h>
int setupterm ( char *term, int fd, int *errret );
```

参数`fd`为一个打开的文件描述符，它用于向终端写数据；如果参数`errret`不是一个空指针，则函数的返回值保存在该参数指向的整数变量中，下面给出了可能写入的值：

- `-1`：`terminfo`数据库不存在。
- `0`：`terminfo`数据库中没有匹配的数据项。
- `1`：成功。

`setupterm`函数在成功的时候返回常量`OK`，失败时返回`ERR`。如果`errret`被设置为空指针，`setupterm`函数会在失败时输出一条诊断信息，并导致程序直接退出。

``` cpp
#include <stdio.h>
#include <term.h>
#include <curses.h>
#include <stdlib.h>
​
int main() {
    setupterm ( "unlisted", fileno ( stdout ), ( int * ) 0 );
    printf ( "Done.\n" );
    exit ( 0 );
}
```

使用如下命令进行编译：

``` cpp
gcc -o badterm badterm.c -lncurses
```

执行结果：

``` cpp
'unlisted': unknown terminal type.
```

在编译时可能会出现错误`term.h: No such file or directory`，解决方法是使用如下命令：

``` bash
sudo apt-get install libncurses5-dev
```

&emsp;&emsp;`tigetflag`、`tigetnum`、`tigetstr`函数分别返回`terminfo`中的布尔功能标志、数值功能标志和字符串功能标志的值。失败时，`tigetflag`返回`-1`，`tigetnum`函数返回`-2`，`tigetstr`函数返回`(char*)-1`。
&emsp;&emsp;使用程序取得`cols`与`lines`功能标志来确定终端尺寸：

``` cpp
#include <stdio.h>
#include <term.h>
#include <ncurses.h>
#include <stdlib.h>
​
int main() {
    int nrows, ncolumns;
    setupterm ( NULL, fileno ( stdout ), ( int * ) 0 );
    nrows = tigetnum ( "lines" );
    ncolumns = tigetnum ( "cols" );
    printf ( "This terminal has %d columns and %d rows \n", ncolumns, nrows );
    exit ( 0 );
}
```

执行结果：

``` cpp
This terminal has 216 columns and 53 rows
```

&emsp;&emsp;如果使用`tigetstr`来取得`xterm`终端类型的光标移动功能标志(`cup`)，我们会得到一个参数化的答案`/E[%p1%d;%p2%dH`。这个功能需要两个参数：光标要移动到的行与列。这两个坐标都是由屏幕左上角的零点处开始计量的。我们可以使用`tparm`函数用实际的值来代替功能中的参数，最多可以替换九个参数，并且会返回一个可用的转义序列。

``` cpp
#include <term.h>
char *tparm ( char *cap, long p1, long p2, ..., long p9 );
```

一旦使用`tparm`来组织终端转义序列，我们必须将其发送到终端。要想正确地完成这一操作，不能使用`printf`来向终端发送字符串，而必须使用特殊的函数，这些函数为终端完成一个操作提供了必要的延时。

``` cpp
#include <term.h>
int putp ( char *const str );
int tputs ( char *const str, int affcnt, int ( *putfunc ) ( int ) );
```

如果成功，`putp`返回`OK`，否则会返回`ERR`。`putp`函数将终端控制字符串作为参数并且将其发送到标准输出。所以要移动到屏幕的第`5`行，第`30`列，我们可以使用下面的代码块：

``` cpp
char *cursor;
char *esc_sequence;
cursor = tigetstr ( "cup" );
esc_sequence = tparm ( cursor, 5, 30 );
putp ( esc_sequence );
```

`tputs`函数是为那些不能通过`stdout`访问终端的情况而提供的，它可以指定一个用于输出字符的函数。`tputs`函数的返回值是函数`putfunc`的返回结果。`affcnt`参数用来指明更改会影响到的行数，通常将其设置为`1`。用于输出字符串的函数必须与`putchar`函数具有相同的参数与返回结果。事实上，`putp(string)`等同于调用`tputs(string, 1, putchar)`。
&emsp;&emsp;注意，一些老的`Linux`发行版本将`tputs`函数的最后一个参数定义为`int (*putfunc)(char)`。如果我们查看`tparm`与终端功能的信息手册页，我们也许会遇到一个`tgoto`函数。它为移动光标提供了一个更为简单的方案，但是不使用这个函数的原因是因为`X/Open`规范并没有将其包含在`1997`版本中。所以我们推荐不要在新程序中使用这些函数。