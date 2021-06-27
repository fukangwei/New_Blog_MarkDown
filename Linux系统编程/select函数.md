---
title: select函数
categories: Linux系统编程
date: 2019-02-03 13:59:39
---
### 阻塞和非阻塞

&emsp;&emsp;阻塞函数在完成其指定的任务以前，不允许程序继续向下执行，这种情况称为`阻塞`。例如，当服务器运行到`accept`语句时，如果没有客户请求连接，服务器就会停止在`accept`语句上，等待连接请求的到来。<!--more-->
&emsp;&emsp;非阻塞操作则可以立即从函数返回，让程序继续向下执行。例如要求服务器仅仅检查是否有客户在等待连接，如果有就接受连接，否则就继续做其他事情。

### select函数

&emsp;&emsp;非阻塞操作可以通过`select`函数来实现：

``` cpp
int select (
    int maxfdp, fd_set *readfds, fd_set *writefds,
    fd_set *errorfds, struct timeval *timeout
);
```

&emsp;&emsp;先说明两个结构体：

- `struct fd_set`可以理解为一个集合，这个集合中存放的是文件描述符。`fd_set`集合可以使用如下宏来操作：

1. 清空集合：`FD_ZERO ( fd_set * )`。
2. 将一个给定的文件描述符加入集合之中：`FD_SET ( int, fd_set * )`。
3. 将一个给定的文件描述符从集合中删除：`FD_CLR ( int, fd_set * )`。
4. 检查集合中指定的文件描述符是否可以读写：`FD_ISSET ( int, fd_set * )`。

- `struct timeval`用来代表时间值，有两个成员，一个是秒数，另一个是毫秒数。

### select参数

&emsp;&emsp;`int maxfdp`：它是一个整数值，是所有文件描述符的最大值加`1`。
&emsp;&emsp;`fd_set *readfds`：这个集合包括文件描述符，检测是否可以从这些文件中读取数据。

- 如果这个集合中有一个文件可读，`select`就会返回一个大于`0`的值。
- 如果没有可读的文件，则根据`timeout`再判断是否超时：

1. 若超出`timeout`的时间，则函数返回`0`。
2. 若发生错误，则返回负值。
3. 可以传入`NULL`值，表示不关心任何文件的读变化。

&emsp;&emsp;`fd_set *writefds`：这个集合包括文件描述符，检查是否可以向这些文件中写入数据。

- 如果这个集合中有一个文件可写，`select`就会返回一个大于`0`的值。
- 如果没有可写的文件，则根据`timeout`参数再判断是否超时：

1. 若超出`timeout`的时间，则`select`返回`0`。
2. 若发生错误，则返回负值。
3. 可以传入`NULL`值，表示不关心任何文件的写变化。

&emsp;&emsp;`fd_set *errorfds`：它和上面`2`个参数的意思一样，用来监视文件错误异常。
&emsp;&emsp;`struct timeval* timeout`：它是`select`的超时时间，可以使`select`处于`3`种状态：

- 若设置为`NULL`，则将`select`置于阻塞状态，一定等到监视文件描述符集合中某个文件描述符发生变化为止。
- 若设为`0`秒`0`毫秒，就是一个非阻塞函数，不管文件描述符是否有变化，都立刻返回。文件无变化时，返回`0`；有变化时，则返回一个正值。
- 若`timeout`的时间大于`0`，则`select`将会阻塞`timeout`时间。如果文件有变化，函数就返回一个正值，否则在超时后立即返回`0`。

### 代码实例

&emsp;&emsp;代码实例：

``` cpp
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <assert.h>
#include <unistd.h>

int main() {
    int keyboard;
    int ret, i;
    char c;
    fd_set readfd;
    struct timeval timeout;
    keyboard = open ( "/dev/tty", O_RDONLY | O_NONBLOCK );
    assert ( keyboard > 0 );

    while ( 1 ) {
        timeout.tv_sec = 5;
        timeout.tv_usec = 0;
        FD_ZERO ( &readfd );
        FD_SET ( keyboard, &readfd );
        ret = select ( keyboard + 1, &readfd, NULL, NULL, &timeout );

        if ( ret == -1 ) { /* select error when ret = -1 */
            perror ( "select error" );
        } else if ( ret ) { /* data coming when ret > 0 */
            if ( FD_ISSET ( keyboard, &readfd ) ) {
                i = read ( keyboard, &c, 1 );

                if ( '\n' == c ) {
                    continue;
                }

                printf ( "The input is %c\n", c );

                if ( 'q' == c ) {
                    break;
                }
            }
        } else if ( ret == 0 ) { /* time out when ret = 0 */
            printf ( "time out\n" );
        }
    }
}
```