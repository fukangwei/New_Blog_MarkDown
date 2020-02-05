---
title: Linux设备控制接口
categories: Linux驱动程序
abbrlink: 9f0762fe
date: 2019-02-04 11:24:01
---
&emsp;&emsp;设备驱动程序的一个基本功能就是管理和控制设备，同时为用户应用程序提供管理和控制设备的接口，在`Linux`中这个接口是通过`ioctl`函数来实现的。<!--more-->

``` cpp
int ( *ioctl ) ( struct inode *, struct file *, unsigned int, unsigned long );
```

`struct inode *`和`struct file *`描述了操作的文件，`unsigned int`描述了`ioctl`命令号，最后一个参数是`unsigned long`数据类型，描述了`ioctl`命令可能带有的参数，它可能是一个整数或指针数据。

### ioctl命令号

&emsp;&emsp;`ioctl`命令号是这个函数中最重要的参数，它描述的`ioctl`要处理的命令。`Linux`中使用一个`32`位的数据来编码`ioctl`命令，它包含四个部分：`dir`、`type`、`nr`和`size`。

- `dir`：代表数据传输的方向，占`2`位，可以是`_IOC_NONE`(无数据传输，`0U`)、`_IOC_WRITE`(向设备写数据，`1U`)或`_IOC_READ`(从设备读数据，`2U`)或他们的逻辑或组合，当然只有`_IOC_WRITE`和`_IOC_READ`的逻辑或才有意义。
- `type`：描述了`ioctl`命令的类型，`8`位。每种设备或系统都可以指定自己的一个类型号，`ioctl`用这个类型来表示`ioctl`命令所属的设备或驱动。一般用`ASCII`码字符来表示，如`a`。
- `nr`：`ioctl`命令序号，一般为`8`位。对于一个指定的设备驱动，可以对它的`ioctl`命令做一个顺序编码，一般从零开始，这个编码就是`ioctl`命令的序号。
- `size`：`ioctl`命令的参数大小，一般为`14`位。`ioctl`命令号的这个数据成员不是强制使用的，你可以不使用它，但是我们建议你指定这个数据成员，通过它我们可以检查用户空间数据的大小以避免错误的数据操作，也可以实现兼容旧版本的`ioctl`命令。

&emsp;&emsp;我们可以自己来直接指定一个`ioctl`命令号，它可能仅仅是一个整数集，但`Linux`中的`ioctl`命令号都是有特定含义的，因此通常我们不推荐这么做。其实`Linux`内核已经提供了相应的宏来自动生成`ioctl`命令号：

``` cpp
_IO ( type, nr )
_IOR ( type, nr, size )
_IOW ( type, nr, size )
_IOWR ( type, nr, size )
```

宏`_IO`用于无数据传输，宏`_IOR`用于从设备读数据，宏`_IOW`用于向设备写数据，宏`_IOWR`用于同时有读写数据的`IOCTL`命令。相对的，`Linux`内核也提供了相应的宏来从`ioctl`命令号种解码相应的域值：

``` cpp
_IOC_DIR ( nr )
_IOC_TYPE ( nr )
_IOC_NR ( nr )
_IOC_SIZE ( nr )
```

这些宏都定义在`asm/ioctl.h`(或`asm-generic.h`)。在使用时，先指定各个`IOCTL`命令的顺序编号(一般从`0`开始)，然后根据使用的环境用这些宏来自动生成`IOCTL`命令号。

### ioctl返回值

&emsp;&emsp;`ioctl`函数的返回值是一个整数类型的值，如果命令执行成功，`ioctl`返回`0`；如果出现错误，`ioctl`函数应该返回一个负值。这个负值会作为`errno`值反馈给调用此`ioctl`的用户空间程序。关于返回值的具体含义，请参考`linux/errno.h`和`asm/errno.h`。

### ioctl参数

&emsp;&emsp;这里有必要说明一下`ioctl`命令的参数，因为它很容易犯错误。如果`ioctl`命令参数仅仅是一个整数，那么事情很简单了，我们可以在`ioctl`函数中直接使用它。但如果它是一个指针数据，那么使用上就要小心了。首先要说明这个参数是有用户空间的程序传递过来的，因此这个指针指向的地址是用户空间地址。在`Linux`中，用户空间地址是一个虚拟地址，在内核空间是无法直接使用它的。为了解决在内核空间使用用户空间地址的数据，`Linux`内核提供了以下函数，它们用于在内核空间访问用户空间的数据，定义在`asm/uaccess.h`：

``` cpp
unsigned long __must_check copy_to_user ( void __user *to, const void *from, unsigned long n );
unsigned long __must_check copy_from_user ( void *to, const void __user *from, unsigned long n );
```

`copy_from_user`和`copy_to_user`一般用于复杂的或大数据交换，对于简单的数据类型，如`int`或`char`，内核提供了简单的宏来实现这个功能：

``` cpp
#define get_user(x, ptr)
#define put_user(x, ptr)
```

其中`x`是内核空间的简单数据类型地址，`ptr`是用户空间地址指针。
&emsp;&emsp;我们需要牢记，在内核中是无法直接访问用户空间地址数据的。因此凡是从用户空间传递过来的指针数据，务必使用内核提供的函数来访问它们。这里有必要再一次强调的是，在内核模块或驱动程序的编写中，我们强烈建议你使用内核提供的接口来生成并操作`ioctl`命令号，这样可以对命令号赋予特定的含义，使我们的程序更加的健壮；另一方面也可以提高程序的可移植性。

### 举例

&emsp;&emsp;将`helloworld`驱动添加到`ioctl`函数，首先添加一个头文件来定义`ioctl`接口需要用到的数据(`hello.h`)：

``` cpp
#ifndef _HELLO_H
#define _HELLO_H
​
#include <asm/ioctl.h>
​
#define MAXBUF 20
​
typedef struct _buf_data {
    int size;
    char data [MAXBUF];
} buf_data;
​
#define HELLO_IOCTL_NR_BASE     0
#define HELLO_IOCTL_NR_SET_DATA (HELLO_IOCTL_NR_BASE + 1)
#define HELLO_IOCTL_NR_MAX      (HELLO_IOCTL_NR_GET_BUFF + 1)
#define HELLO_IOCTL_SET_DATA    _IOR('h', HELLO_IOCTL_NR_SET_DATA, buf_data*)
​
#endif
```

然后为驱动程序添加`ioctl`接口`hello_ioctl`：

``` cpp
static int hello_ioctl ( struct inode *inode, struct file *filp,
                         unsigned int cmd, unsigned long arg ) {
    int cmd_nr;
    int err;
    buf_data buff;
    err = 0;
    cmd_nr = _IOC_NR ( cmd );
​
    switch ( cmd_nr ) {
        case HELLO_IOCTL_NR_SET_DATA:
            if ( copy_from_user ( &buff, ( unsigned char * ) arg, sizeof ( buf_data ) ) ) {
                err = -ENOMEM;
                goto error;
            }
​
            memset ( hello_buf, 0, sizeof ( hello_buf ) );
            memcpy ( hello_buf, buff.data, buff.size );
            break;
        default:
            printk ( "hello_ioctl: Unknown ioctl command (%d)\n", cmd );
            break;
    }
​
error:
    return err;
}
​
static struct file_operations hello_fops = {
    .read = hello_read,
    .write = hello_write,
    .open = hello_open,
    .ioctl = hello_ioctl,
    .release = hello_release,
};
```

---

### ioctl控制设备

&emsp;&emsp;`memdev.c`如下：

``` cpp
#include <linux/module.h>
#include <linux/types.h>
#include <linux/fs.h>
#include <linux/errno.h>
#include <linux/mm.h>
#include <linux/sched.h>
#include <linux/init.h>
#include <linux/cdev.h>
#include <asm/io.h>
#include <asm/system.h>
#include <asm/uaccess.h>
#include "memdev.h"
​
static int mem_major = MEMDEV_MAJOR;
module_param ( mem_major, int, S_IRUGO );
​
struct mem_dev *mem_devp; /* 设备结构体指针 */
struct cdev cdev;
​
int mem_open ( struct inode *inode, struct file *filp ) { /* 文件打开函数 */
    struct mem_dev *dev;
    int num = MINOR ( inode->i_rdev ); /* 获取次设备号 */
​
    if ( num >= MEMDEV_NR_DEVS ) {
        return -ENODEV;
    }
​
    dev = &mem_devp[num];
    filp->private_data = dev; /* 将设备描述结构指针赋值给文件私有数据指针 */
    return 0;
}
​
int mem_release ( struct inode *inode, struct file *filp ) { /* 文件释放函数 */
    return 0;
}
​
/* IO操作 */
int memdev_ioctl ( struct inode *inode, struct file *filp,
                   unsigned int cmd, unsigned long arg ) {
    int err = 0;
    int ret = 0;
    int ioarg = 0;
​
    if ( _IOC_TYPE ( cmd ) != MEMDEV_IOC_MAGIC ) { /* 检测命令的有效性 */
        return -EINVAL;
    }
​
    if ( _IOC_NR ( cmd ) > MEMDEV_IOC_MAXNR ) {
        return -EINVAL;
    }
​
    if ( _IOC_DIR ( cmd ) & _IOC_READ ) { /* 根据命令类型，检测参数空间是否可以访问 */
        err = !access_ok ( VERIFY_WRITE, ( void * ) arg, _IOC_SIZE ( cmd ) );
    } else if ( _IOC_DIR ( cmd ) & _IOC_WRITE ) {
        err = !access_ok ( VERIFY_READ, ( void * ) arg, _IOC_SIZE ( cmd ) );
    }
​
    if ( err ) {
        return -EFAULT;
    }
​
    switch ( cmd ) { /* 根据命令，执行相应的操作 */
        case MEMDEV_IOCPRINT: /* 打印当前设备信息 */
            printk ( "<--- CMD MEMDEV_IOCPRINT Done--->\n\n" );
            break;
        case MEMDEV_IOCGETDATA: /* 获取参数 */
            ioarg = filp->f_pos;
            ret = __put_user ( ioarg, ( int * ) arg );
            break;
        case MEMDEV_IOCSETDATA: /* 设置参数 */
            ret = __get_user ( ioarg, ( int * ) arg );
​
            if ( ( ioarg < 0 ) || ( ioarg > MEMDEV_SIZE ) ) {
                return -EINVAL;
            }
​
            filp->f_pos = ioarg;
            printk ( "<--- In Kernel MEMDEV_IOCSETDATA ioarg = %d --->\n\n", ioarg );
            break;
        default:
            return -EINVAL;
    }
​
    return ret;
}
​
/* 读函数 */
static ssize_t mem_read ( struct file *filp, char __user *buf, size_t size, loff_t *ppos ) {
    unsigned long p = *ppos;
    unsigned int count = size;
    int ret = 0;
    struct mem_dev *dev = filp->private_data; /* 获得设备结构体指针 */
​
    if ( p >= MEMDEV_SIZE ) { /* 判断读位置是否有效 */
        return 0;
    }
​
    if ( count > MEMDEV_SIZE - p ) {
        count = MEMDEV_SIZE - p;
    }
​
    if ( copy_to_user ( buf, ( void * ) ( dev->data + p ), count ) ) { /* 读数据到用户空间 */
        ret = -EFAULT;
    } else {
        *ppos += count;
        ret = count;
        printk ( KERN_INFO "read %d bytes(s) from %ld\n", count, p );
    }
​
    return ret;
}
​
/* 写函数 */
static ssize_t mem_write ( struct file *filp, const char __user *buf, size_t size, loff_t *ppos ) {
    unsigned long p = *ppos;
    unsigned int count = size;
    int ret = 0;
    struct mem_dev *dev = filp->private_data; /* 获得设备结构体指针 */
​
    if ( p >= MEMDEV_SIZE ) { /* 分析和获取有效的写长度 */
        return 0;
    }
​
    if ( count > MEMDEV_SIZE - p ) {
        count = MEMDEV_SIZE - p;
    }
​
    if ( copy_from_user ( dev->data + p, buf, count ) ) { /* 从用户空间写入数据 */
        ret = -EFAULT;
    } else {
        *ppos += count;
        ret = count;
    }
​
    return ret;
}
​
static const struct file_operations mem_fops = { /* 文件操作结构体 */
    .owner   = THIS_MODULE,
    .open    = mem_open,
    .release = mem_release,
    .ioctl   = memdev_ioctl,
    .read    = mem_read,
    .write   = mem_write,
};
​
static int memdev_init ( void ) { /* 设备驱动模块加载函数 */
    int result;
    int i;
    dev_t devno = MKDEV ( mem_major, 0 );
​
    if ( mem_major ) {
        result = register_chrdev_region ( devno, 2, "memdev" ); /* 静态申请设备号 */
    } else {
        result = alloc_chrdev_region ( &devno, 0, 2, "memdev" ); /* 动态分配设备号 */
        mem_major = MAJOR ( devno );
    }
​
    if ( result < 0 ) {
        return result;
    }
​
    cdev_init ( &cdev, &mem_fops ); /* 初始化cdev结构 */
    cdev.owner = THIS_MODULE;
    cdev.ops = &mem_fops;
    cdev_add ( &cdev, MKDEV ( mem_major, 0 ), MEMDEV_NR_DEVS ); /* 注册字符设备 */
    /* 为设备描述结构分配内存 */
    mem_devp = kmalloc ( MEMDEV_NR_DEVS * sizeof ( struct mem_dev ), GFP_KERNEL );
​
    if ( !mem_devp ) { /* 申请失败 */
        result = -ENOMEM;
        goto fail_malloc;
    }
​
    memset ( mem_devp, 0, sizeof ( struct mem_dev ) );
​
    for ( i = 0; i < MEMDEV_NR_DEVS; i++ ) { /* 为设备分配内存 */
        mem_devp[i].size = MEMDEV_SIZE;
        mem_devp[i].data = kmalloc ( MEMDEV_SIZE, GFP_KERNEL );
        memset ( mem_devp[i].data, 0, MEMDEV_SIZE );
    }
​
    return 0;
fail_malloc:
    unregister_chrdev_region ( devno, 1 );
    return result;
}

static void memdev_exit ( void ) { /* 模块卸载函数 */
    cdev_del ( &cdev ); /* 注销设备 */
    kfree ( mem_devp ); /* 释放设备结构体内存 */
    unregister_chrdev_region ( MKDEV ( mem_major, 0 ), 2 ); /* 释放设备号 */
}
​
MODULE_AUTHOR ( "David Xie" );
MODULE_LICENSE ( "GPL" );
​
module_init ( memdev_init );
module_exit ( memdev_exit );
```

&emsp;&emsp;`memdev.h`如下：

``` cpp
#ifndef _MEMDEV_H_
#define _MEMDEV_H_
​
#include <linux/ioctl.h>
​
#ifndef MEMDEV_MAJOR
    #define MEMDEV_MAJOR 0 /* 预设的mem的主设备号 */
#endif
​
#ifndef MEMDEV_NR_DEVS
    #define MEMDEV_NR_DEVS 2 /* 设备数 */
#endif
​
#ifndef MEMDEV_SIZE
    #define MEMDEV_SIZE 4096
#endif
​
struct mem_dev { /* mem设备描述结构体 */
    char *data;
    unsigned long size;
};
​
#define MEMDEV_IOC_MAGIC 'k' /* 定义幻数 */
​
/* 定义命令 */
#define MEMDEV_IOCPRINT   _IO(MEMDEV_IOC_MAGIC, 1)
#define MEMDEV_IOCGETDATA _IOR(MEMDEV_IOC_MAGIC, 2, int)
#define MEMDEV_IOCSETDATA _IOW(MEMDEV_IOC_MAGIC, 3, int)
​
#define MEMDEV_IOC_MAXNR 3

#endif /* _MEMDEV_H_ */
```

&emsp;&emsp;`app-ioctl.c`如下：

``` cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include "memdev.h" /* 包含命令定义 */
​
/* 调用命令MEMDEV_IOCSETDATA */
static inline void setpos ( int fd, int pos ) {
    printf ( "ioctl: Call MEMDEV_IOCSETDATA to set position\n" );
    ioctl ( fd, MEMDEV_IOCSETDATA, &pos );
}
​
static inline int getpos ( int fd ) { /* 调用命令MEMDEV_IOCGETDATA */
    int pos;
    printf ( "ioctl: Call MEMDEV_IOCGETDATA to get position\n" );
    ioctl ( fd, MEMDEV_IOCGETDATA, &pos );
    return pos;
}
​
int main ( void ) {
    int fd = 0;
    int arg = 0;
    char buf[256];
​
    if ( -1 == ( fd = open ( "/dev/memdev", O_RDWR ) ) ) { /* 打开设备文件 */
        printf ( "Open Dev Mem0 Error!\n" );
        _exit ( EXIT_FAILURE );
    }
​
    /* 调用命令MEMDEV_IOCPRINT */
    printf ( "ioctl: Call MEMDEV_IOCPRINT to printk in driver\n" );
    ioctl ( fd, MEMDEV_IOCPRINT, &arg );
    strcpy ( buf, "This is a test for ioctl" );
    printf ( "write %d bytes in new open file\n", strlen ( buf ) );
    write ( fd, buf, strlen ( buf ) );
    printf ( "new pos is %d\n", getpos ( fd ) );
    setpos ( fd, 0 );
    printf ( "set pos = 0\n" );
    printf ( "after setpos(fd, 0), new pos is %d\n", getpos ( fd ) );
    close ( fd );
    return 0;
}
```