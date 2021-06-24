---
title: 共享内存_mmap
date: 2021-06-26 06:16:18
categories: Linux系统编程
---

&emsp;&emsp;共享内存可以说是最有用的进程间通信方式，也是最快的`IPC`形式。两个不同进程`A`、`B`共享内存的意思是，同一块物理内存被映射到进程`A`、`B`各自的进程地址空间。进程`A`可以即时看到进程`B`对共享内存中数据的更新，反之亦然。由于多个进程共享同一块内存区域，必然需要某种同步机制，互斥锁和信号量都可以。<!--more-->

### mmap及其相关系统调用

&emsp;&emsp;`mmap`系统调用使得进程之间通过映射同一个普通文件实现共享内存。普通文件被映射到进程地址空间后，进程可以向访问普通内存一样对文件进行访问，不必再调用`read`、`write`等操作。
&emsp;&emsp;实际上，`mmap`系统调用并不是完全为了用于共享内存而设计的。它本身提供了不同于一般对普通文件的访问方式，进程可以像读写内存一样对普通文件的操作。而`Posix`或系统`V`的共享内存`IPC`则纯粹用于共享目的，当然`mmap`实现共享内存也是其主要应用之一。
&emsp;&emsp;1. `mmap`系统调用形式如下：

``` cpp
void *mmap ( void *addr, size_t len, int prot, int flags, int fd, off_t offset );
```

参数`fd`为即将映射到进程空间的文件描述字，一般由`open`返回，同时`fd`可以指定为`-1`，此时须指定`flags`参数中的`MAP_ANON`，表明进行的是匿名映射(不涉及具体的文件名，避免了文件的创建及打开，很显然只能用于具有亲缘关系的进程间通信)。

- `len`：映射到调用进程地址空间的字节数，它从被映射文件开头`offset`个字节开始算起。
- `prot`：指定共享内存的访问权限。可取如下几个值，可以以进行`或`计算：`PROT_READ`(可读)、`PROT_WRITE`(可写)、`PROT_EXEC`(可执行)、`PROT_NONE`(不可访问)。
- `flags`：由以下几个常值指定：`MAP_SHARED`、`MAP_PRIVATE`。
- `offset`：一般设为`0`，表示从文件头开始映射。
- `addr`：指定文件应被映射到进程空间的起始地址，一般被指定一个空指针，此时选择起始地址的任务留给内核来完成。

函数的返回值为最后文件映射到进程空间的地址，进程可直接操作起始地址为该值的有效地址。
&emsp;&emsp;2. 系统调用`mmap`用于共享内存的两种方式：

- 使用普通文件提供的内存映射：适用于任何进程之间；此时，需要打开或创建一个文件，然后再调用`mmap`。典型调用代码如下：

``` cpp
fd = open ( name, flag, mode );
ptr = mmap ( NULL, len, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0 );
```

- 使用特殊文件提供匿名内存映射：适用于具有亲缘关系的进程之间；由于父子进程特殊的亲缘关系，在父进程中先调用`mmap`，然后调用`fork`。那么在调用`fork`之后，子进程继承父进程匿名映射后的地址空间，同样也继承`mmap`返回的地址，这样父子进程就可以通过映射区域进行通信了。注意，这里不是一般的继承关系。一般来说，子进程单独维护从父进程继承下来的一些变量，而`mmap`返回的地址，却由父子进程共同维护。对于具有亲缘关系的进程实现共享内存，最好的方式应该是采用匿名内存映射的方式。此时，不必指定具体的文件，只要设置相应的标志即可。

&emsp;&emsp;3. 系统调用`munmap`：

``` cpp
int munmap ( void *addr, size_t len );
```

该调用在进程地址空间中解除一个映射关系，`addr`是调用`mmap`时返回的地址，`len`是映射区的大小。当映射关系解除后，对原来映射地址的访问将导致段错误发生。
&emsp;&emsp;4. 系统调用`msync`：

``` cpp
int msync ( void *addr, size_t len, int flags );
```

一般说来，进程在映射空间的对共享内容的改变并不直接写回到磁盘文件中，往往在调用`munmap`后才执行该操作。可以通过调用`msync`实现磁盘上文件内容与共享内存区的内容一致。

### mmap范例

&emsp;&emsp;范例`1`为两个进程通过映射普通文件实现共享内存通信。范例`1`包含两个子程序：`map_normalfile1.c`和`map_normalfile2.c`。编译两个程序，可执行文件分别为`map_normalfile1`和`map_normalfile2`。两个程序通过命令行参数指定同一个文件来实现共享内存方式的进程间通信。`map_normalfile2`试图打开命令行参数指定的一个普通文件，把该文件映射到进程的地址空间，并对映射后的地址空间进行写操作。`map_normalfile1`把命令行参数指定的文件映射到进程地址空间，然后对映射后的地址空间执行读操作。这样，两个进程通过命令行参数指定同一个文件来实现共享内存方式的进程间通信。
&emsp;&emsp;`map_normalfile1.c`如下：

``` cpp
#include <sys/mman.h>
#include <sys/types.h>
#include <fcntl.h>
#include <unistd.h>
#include "stdio.h"
#include "string.h"

typedef struct {
    char name[4];
    int age;
} people;

int main ( int argc, char **argv ) {
    int fd, i;
    people *p_map;
    char temp;
    fd = open ( argv[1], O_CREAT | O_RDWR | O_TRUNC, 00777 );
    lseek ( fd, sizeof ( people ) * 5 - 1, SEEK_SET );
    write ( fd, " ", 1 );
    p_map = ( people * ) mmap ( NULL, sizeof ( people ) * 10, \
                                PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0 );
    close ( fd );
    temp = 'a';

    for ( i = 0; i < 10; i++ ) {
        sprintf ( ( * ( p_map + i ) ).name, "%c", temp );
        ( * ( p_map + i ) ).age = 20 + i;
        temp += 1;
    }

    printf ( "initialize over\n" );
    sleep ( 10 );
    munmap ( p_map, sizeof ( people ) * 10 );
    printf ( "umap ok\n" );
}
```

&emsp;&emsp;`map_normalfile2.c`如下：

``` cpp
#include <sys/mman.h>
#include <sys/types.h>
#include <fcntl.h>
#include <unistd.h>
#include "stdio.h"

typedef struct {
    char name[4];
    int age;
} people;

int main ( int argc, char **argv ) {
    int fd, i;
    people *p_map;
    fd = open ( argv[1], O_CREAT | O_RDWR, 00777 );
    p_map = ( people * ) mmap ( NULL, sizeof ( people ) * 10, \
                                PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0 );

    for ( i = 0; i < 10; i++ ) {
        printf ( "name: %s age %d;\n", ( * ( p_map + i ) ).name, ( * ( p_map + i ) ).age );
    }

    munmap ( p_map, sizeof ( people ) * 10 );
}
```

&emsp;&emsp;`map_normalfile1.c`首先定义了一个`people`数据结构，在这里采用数据结构的方式是因为，共享内存区的数据往往是有固定格式的，这由通信的各个进程决定，采用结构的方式有普遍代表性。`map_normfile1`首先打开或创建一个文件，并把文件的长度设置为`5`个`people`结构大小。然后从`mmap`的返回地址开始，设置了`10`个`people`结构。然后，进程睡眠`10`秒钟，等待其他进程映射同一个文件，最后解除映射。`map_normfile2.c`只是简单的映射一个文件，并以`people`数据结构的格式从`mmap`返回的地址处读取`10`个`people`结构，并输出读取的值，然后解除映射。
&emsp;&emsp;分别把两个程序编译成可执行文件`map_normalfile1`和`map_normalfile2`后，在一个终端上先运行`./map_normalfile1 /tmp/test_shm`，执行结果如下：

``` cpp
initialize over
umap ok
```

在`map_normalfile1`输出`initialize over`之后，输出`umap ok`之前，在另一个终端上运行`./map_normalfile2 /tmp/test_shm`，将会产生如下输出：

``` cpp
name: a age 20;
name: b age 21;
name: c age 22;
name: d age 23;
name: e age 24;
name: f age 25;
name: g age 26;
name: h age 27;
name: i age 28;
name: j age 29;
```

在`map_normalfile1`输出`umap ok`后，运行`map_normalfile2`则输出如下结果：

``` cpp
name: a age 20;
name: b age 21;
name: c age 22;
name: d age 23;
name: e age 24;
name:  age 0;
name:  age 0;
name:  age 0;
name:  age 0;
name:  age 0;
```

从程序的运行结果中可以得出的结论：
&emsp;&emsp;1. 最终被映射文件的内容的长度不会超过文件本身的初始大小，即映射不能改变文件的大小。
&emsp;&emsp;2. 可以用于进程通信的有效地址空间大小大体上受限于被映射文件的大小，但不完全受限于文件大小。打开文件被截短为`5`个`people`结构大小，而在`map_normalfile1`中初始化了`10`个`people`数据结构，在恰当时候(`map_normalfile1`输出`initialize over`之后，输出`umap ok`之前)调用`map_normalfile2`会发现`map_normalfile2`将输出全部`10`个`people`结构的值。
&emsp;&emsp;3. 文件一旦被映射后，调用`mmap`的进程对返回地址的访问是对某一内存区域的访问，暂时脱离了磁盘上文件的影响。所有对`mmap`返回地址空间的操作只在内存中有意义，只有在调用了`munmap`后或者`msync`时，才把内存中的相应内容写回磁盘文件，所写内容仍然不能超过文件的大小。
&emsp;&emsp;范例`2`为父子进程通过匿名映射实现共享内存：

``` cpp
#include <sys/mman.h>
#include <sys/types.h>
#include <fcntl.h>
#include <unistd.h>
#include "stdio.h"
#include "stdlib.h"
#include "string.h"

typedef struct {
    char name[4];
    int age;
} people;

int main ( int argc, char **argv ) {
    int i;
    people *p_map;
    char temp;
    p_map = ( people * ) mmap ( NULL, sizeof ( people ) * 10, \
                                PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0 );

    if ( fork() == 0 ) {
        sleep ( 2 );

        for ( i = 0; i < 5; i++ ) {
            printf ( "child read: the %d people's age is %d\n", i + 1, ( * ( p_map + i ) ).age );
        }

        ( *p_map ).age = 100;
        munmap ( p_map, sizeof ( people ) * 10 ); /* 实际上，进程终止时，会自动解除映射 */
        exit ( 0 );
    }

    temp = 'a';

    for ( i = 0; i < 5; i++ ) {
        temp += 1;
        memcpy ( ( * ( p_map + i ) ).name, &temp, 2 );
        ( * ( p_map + i ) ).age = 20 + i;
    }

    sleep ( 5 );
    printf ( "parent read: the first people's age is %d\n", ( *p_map ).age );
    printf ( "umap\n" );
    munmap ( p_map, sizeof ( people ) * 10 );
    printf ( "umap ok\n" );
}
```

查看程序的输出结果，体会父子进程匿名共享内存：

``` cpp
child read: the 1 people's age is 20
child read: the 2 people's age is 21
child read: the 3 people's age is 22
child read: the 4 people's age is 23
child read: the 5 people's age is 24
parent read: the first people's age is 100
umap
umap ok
```