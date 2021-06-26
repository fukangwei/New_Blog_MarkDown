---
title: 共享内存_mmap
date: 2021-06-26 06:16:18
categories: Linux系统编程
---
&emsp;&emsp;共享内存的含义可以参考{% post_link "Linux系统编程/共享内存_shmget" %}。<!--more-->

### mmap

&emsp;&emsp;`mmap`函数使得进程之间通过映射同一个文件实现共享内存。进程可以像访问内存一样对文件进行访问，不必调用`read`、`write`等操作。
&emsp;&emsp;`mmap`的函数原型如下，其返回值为文件映射到进程空间的地址：

``` cpp
void *mmap ( void *addr, size_t len, int prot, int flags, int fd, off_t offset );
```

- `addr`：一般设置为`NULL`。
- `len`：映射到进程地址空间的字节数，从被映射文件头后`offset`个字节开始算起。
- `prot`：共享内存的访问权限。可以取如下几个值，也可以进行`或`计算：

权限        | 说明   | 权限         | 说明
------------|--------|-------------|----
`PROT_READ` | 可读   | `PROT_WRITE` | 可写
`PROT_EXEC` | 可执行 | `PROT_NONE`  | 不可访问

- `flags`：可以取`MAP_SHARED`或`MAP_PRIVATE`。
- `offset`：一般设为`0`，表示从文件头开始映射。
- `fd`：映射到进程空间的文件描述符，有如下`2`种获取方式：

1. 一般由`open`打开一个文件后返回。
2. 可以指定为`-1`，但同时`flags`要与`MAP_ANONYMOUS`进行`或`运算，表明进行的是`匿名映射`。

&emsp;&emsp;`匿名映射`不涉及具体的文件名，避免了文件的创建及打开，只能用于具有亲缘关系的进程。

### 创建共享内存

&emsp;&emsp;创建共享内存有如下`2`种方式：

- 使用普通文件提供的内存映射：

1. 适用于任何进程之间。
2. 需要打开或创建一个文件，然后再调用`mmap`：

- 使用特殊文件提供匿名内存映射：

1. 适用于具有亲缘关系的进程。
2. 在父进程中先调用`mmap`，然后调用`fork`。

### munmap

&emsp;&emsp;使用`munmap`在进程地址空间中解除映射关系：

``` cpp
int munmap ( void *addr, size_t len );
```

- `addr`是调用`mmap`时返回的地址。
- `len`是映射区的大小。

在调用`munmap`后，共享内存中的内容将被写到`fd`对应的文件中。

### 普通映射

&emsp;&emsp;两个进程通过映射普通文件实现共享内存通信：
&emsp;&emsp;`map_write.c`如下：

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
    p_map = ( people * ) mmap ( NULL, sizeof ( people ) * 5, \
                                PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0 );
    close ( fd );
    temp = 'a';

    for ( i = 0; i < 5; i++ ) {
        sprintf ( ( * ( p_map + i ) ).name, "%c", temp );
        ( * ( p_map + i ) ).age = 20 + i;
        temp += 1;
    }

    printf ( "initialize over\n" );
    sleep ( 10 );
    munmap ( p_map, sizeof ( people ) * 5 );
    printf ( "umap ok\n" );
}
```

&emsp;&emsp;`map_read.c`如下：

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
    p_map = ( people * ) mmap ( NULL, sizeof ( people ) * 5, \
                                PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0 );

    for ( i = 0; i < 5; i++ ) {
        printf ( "name: %s age %d;\n", ( * ( p_map + i ) ).name, ( * ( p_map + i ) ).age );
    }

    munmap ( p_map, sizeof ( people ) * 5 );
}
```

&emsp;&emsp;在一个终端上运行`./map_write /tmp/test_shm`，执行结果如下：

``` cpp
initialize over
等待10秒
umap ok
```

&emsp;&emsp;在另一个终端上运行`./map_read /tmp/test_shm`，执行结果如下：

``` cpp
name: a age 20;
name: b age 21;
name: c age 22;
name: d age 23;
name: e age 24;
```

### 匿名映射

&emsp;&emsp;父子进程通过匿名映射实现共享内存通信：

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

    if ( fork() == 0 ) { /* 子进程 */
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

执行结果：

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