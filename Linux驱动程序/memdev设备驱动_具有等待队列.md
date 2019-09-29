---
title: memdev设备驱动_具有等待队列
categories: Linux驱动程序
abbrlink: 22df4bae
date: 2019-02-04 11:04:47
---
&emsp;&emsp;`memdev.h`如下：

``` cpp
#ifndef _MEMDEV_H_
#define _MEMDEV_H_
​
#ifndef MEMDEV_MAJOR
    #define MEMDEV_MAJOR 251
#endif
​
#ifndef MEMDEV_NR_DEVS
    #define MEMDEV_NR_DEVS 2
#endif
​
#ifndef MEMDEV_SIZE
    #define MEMDEV_SIZE 4096
#endif
​
struct mem_dev {
    bool canRead; /* 设备可读标识 */
    bool canWrite; /* 设备可写标识 */
    char *data;
    unsigned long size;
    unsigned long rpos; /* 读定位标识 */
    unsigned long wpos; /* 写定位标识 */
    unsigned long nattch;
    wait_queue_head_t rwq;
    wait_queue_head_t wwq;
};
​
#endif
```

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
    dev->nattch++;
    return 0;
}
​
int mem_release ( struct inode *inode, struct file *filp ) { /* 文件释放函数 */
    struct mem_dev *dev = filp->private_data; /* 获得设备结构体指针 */
    dev->nattch--;
​
    if ( 0 == dev->nattch ) {
        /* 使读写位置标志重新指到开头，并修改可读可写标志 */
        dev->rpos = 0;
        dev->wpos = 0;
        dev->canRead  = false;
        dev->canWrite = true;
    }
​
    return 0;
}
​
/* 读函数 */
static ssize_t mem_read ( struct file *filp, char __user *buf, size_t size, loff_t *ppos ) {
    unsigned int count;
    int ret = 0;
    struct mem_dev *dev = filp->private_data; /* 获得设备结构体指针 */
​
    if ( !dev->canRead ) {
        if ( filp->f_flags & O_NONBLOCK ) {
            return -EAGAIN;
        }
​
        wait_event_interruptible ( dev->rwq, dev->canRead );
    }
​
    if ( dev->rpos < dev->wpos ) {
        count = dev->wpos - dev->rpos;
        count = count > size ? size : count;
    } else {
        count = MEMDEV_SIZE - dev->rpos - 1;
​
        if ( count >= size ) {
            count = size;
        } else {
            if ( copy_to_user ( buf, ( void * ) ( dev->data + dev->rpos ), count ) ) {
                ret = -EFAULT;
            }
​
            ret += count;
            dev->rpos = 0;
            buf += count;
            size -= count;
            count = dev->wpos > size ? size : dev->wpos;
        }
    }
​
    if ( copy_to_user ( buf, ( void * ) ( dev->data + dev->rpos ), count ) ) { /* 读数据到用户空间 */
        ret = -EFAULT;
    } else {
        dev->rpos += count;
        ret += count;
    }
​
    if ( ret ) {
        dev->canWrite = true; /* 有空间可写 */
        wake_up ( & ( dev->wwq ) ); /* 唤醒写等待队列 */
​
        if ( dev->rpos == dev->wpos ) {
            dev->canRead = false; /* 无数据可读 */
        }
    }
​
    return ret;
}
​
/* 写函数 */
static ssize_t mem_write ( struct file *filp, const char __user *buf, size_t size, loff_t *ppos ) {
    unsigned int count;
    int ret = 0;
    struct mem_dev *dev = filp->private_data; /* 获得设备结构体指针 */
    printk ( "<1> [Before mem_write: rpos = %lu, wpos = %lu, canR = %d, canW = %d]\n",
             dev->rpos, dev->wpos, dev->canRead, dev->canWrite );
​
    if ( !dev->canWrite ) { /* 没有空间可写，则进入睡眠 */
        if ( filp->f_flags & O_NONBLOCK ) {
            return -EAGAIN;
        }
​
        wait_event_interruptible ( dev->wwq, dev->canWrite );
    }
​
    if ( dev->rpos > dev->wpos ) {
        count = dev->rpos - dev->wpos;
        count = count > size ? size : count;
    } else {
        count = MEMDEV_SIZE - dev->wpos - 1;
​
        if ( count >= size ) {
            count = size;
        } else {
            if ( copy_from_user ( dev->data + dev->wpos, buf, count ) ) {
                ret = -EFAULT;
            }
​
            ret += count;
            dev->wpos = 0;
            buf  += count;
            size -= count;
            count = dev->rpos > size ? size : dev->rpos;
        }
    }
​
    if ( copy_from_user ( dev->data + dev->wpos, buf, count ) ) { /* 从用户空间写入数据 */
        ret = -EFAULT;
    } else {
        dev->wpos += count;
        ret += count;
    }
​
    if ( ret ) {
        dev->canRead = true; /* 有空间可写 */
        wake_up ( & ( dev->rwq ) ); /* 唤醒读等待队列 */
​
        if ( dev->rpos == dev->wpos ) {
            dev->canWrite = false; /* 无数据可写 */
        }
    }
​
    printk ( "<1> [After mem_write: rpos=%lu, wpos=%lu, canR=%d, canW=%d]\n",
             dev->rpos, dev->wpos, dev->canRead, dev->canWrite );
    return ret;
}
​
static const struct file_operations mem_fops = { /* 文件操作结构体 */
    .owner   = THIS_MODULE,
    .read    = mem_read,
    .write   = mem_write,
    .open    = mem_open,
    .release = mem_release,
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
    for ( i = 0; i < MEMDEV_NR_DEVS; i++ ) {  /* 为设备分配内存 */
        mem_devp[i].size = MEMDEV_SIZE;
        mem_devp[i].data = kmalloc ( MEMDEV_SIZE, GFP_KERNEL );
        memset ( mem_devp[i].data, 0, MEMDEV_SIZE );
        mem_devp[i].canRead  = false; /* 一开始设备没有数据可供读 */
        mem_devp[i].canWrite = true; /* 一开始设备有空间可供写 */
        /* 初始化读写指针 */
        mem_devp[i].rpos = 0;
        mem_devp[i].wpos = 0;
        /* 初始化等待队列 */
        init_waitqueue_head ( & ( mem_devp[i].rwq ) );
        init_waitqueue_head ( & ( mem_devp[i].wwq ) );
        mem_devp[i].nattch = 0;
    }
​
    return 0;
fail_malloc:
    unregister_chrdev_region ( devno, 1 );
    return result;
}
​
static void memdev_exit ( void ) { /* 模块卸载函数 */
    cdev_del ( &cdev ); /* 注销设备 */
    kfree ( mem_devp ); /* 释放设备结构体内存 */
    unregister_chrdev_region ( MKDEV ( mem_major, 0 ), 2 ); /* 释放设备号 */
}
​
MODULE_LICENSE ( "GPL" );
​
module_init ( memdev_init );
module_exit ( memdev_exit );
```

&emsp;&emsp;`app-block.c`如下：

``` cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
​
#define MEMDEV_SIZE 4096

int main ( void ) {
    int fd, ret;
    char buf[MEMDEV_SIZE];
    printf ( "One processes read, Another write ...\n" );
​
    if ( -1 == ( ret = fork() ) ) {
        printf ( "fork() error\n" );
        _exit ( EXIT_FAILURE );
    } else if ( 0 == ret ) { /* child process */
        if ( -1 == ( fd = open ( "/dev/memdev", O_RDWR ) ) ) {
            printf ( "open() error" );
            _exit ( EXIT_FAILURE );
        }
​
        printf ( "Child sleep 5s ...\n\n" );
        sleep ( 5 );
        strcpy ( buf, "Here is the CHILD writing ..." );
        ret = write ( fd, buf, strlen ( buf ) );
        printf ( "Child write1(%d bytes): %s\n", strlen ( buf ), buf );
        sleep ( 5 );
        printf ( "Child sleep 10s ...\n\n" );
        sleep ( 10 );
        ret = write ( fd, buf, strlen ( buf ) );
        printf ( "Child write2(%d bytes): %s\n", ret, buf );
        sleep ( 5 );
        ret = write ( fd, buf, sizeof ( buf ) );
        printf ( "Child write3(%d bytes -- buf is full)\n", ret );
        printf ( "Child try to write4, but there is no space, blocking ...\n" );
        ret = write ( fd, buf, strlen ( buf ) );
        buf[ret] = 0;
        usleep ( 30000 );
        printf ( "After Father read4, Child write4(%d bytes): %s\n", ret, buf );
        close ( fd );
        _exit ( EXIT_SUCCESS );
    } else { /* father process */
        if ( -1 == ( fd = open ( "/dev/memdev", O_RDWR ) ) ) {
            printf ( "open() error" );
            _exit ( EXIT_FAILURE );
        }
​
        printf ( "Father try to read, but there is no data, blocking ...\n" );
        int ret = 0;
        ret = read ( fd, buf, 20 );
        usleep ( 30000 );
        printf ( "After Child write1, Father can read:\n" );
        buf[ret] = 0;
        printf ( "Father read1(%d bytes): %s\n", ret, buf );
        ret = read ( fd, buf, 20 );
        buf[ret] = 0;
        printf ( "Father read2(%d bytes): %s\n", ret, buf );
        printf ( "Father read3, no data to read, blocking ...\n" );
        ret = read ( fd, buf, 50 );
        usleep ( 30000 );
        buf[ret] = 0;
        printf ( "After Child write2, Father read3(%d bytes): %s\n", ret, buf );
        printf ( "Father sleep 10s ...\n\n" );
        sleep ( 10 );
        ret = read ( fd, buf, 20 );
        printf ( "Father read4(%d bytes)\n", ret );
        wait ( NULL );
        close ( fd );
    }
​
    _exit ( EXIT_SUCCESS );
}
```