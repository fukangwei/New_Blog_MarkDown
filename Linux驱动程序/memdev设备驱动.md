---
title: memdev设备驱动
date: 2019-02-04 10:45:51
categories: Linux驱动程序
---
&emsp;&emsp;`memdev.h`如下：

``` cpp
#ifndef _MEMDEV_H_
#define _MEMDEV_H_
​
#ifndef MEMDEV_MAJOR
    #define MEMDEV_MAJOR 251 /* 预设的mem的主设备号 */
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
#endif /* _MEMDEV_H_ */
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
    return 0;
}
​
int mem_release ( struct inode *inode, struct file *filp ) { /* 文件释放函数 */
    return 0;
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
        printk ( KERN_INFO "written %d bytes(s) from %ld\n", count, p );
    }
​
    return ret;
}
​
static loff_t mem_llseek ( struct file *filp, loff_t offset, int whence ) { /* seek文件定位函数 */
    loff_t newpos;
​
    switch ( whence ) {
        case 0: /* SEEK_SET */
            newpos = offset;
            break;
        case 1: /* SEEK_CUR */
            newpos = filp->f_pos + offset;
            break;
        case 2: /* SEEK_END */
            newpos = MEMDEV_SIZE - 1 + offset;
            break;
        default: /* can't happen */
            return -EINVAL;
    }
​
    if ( ( newpos < 0 ) || ( newpos > MEMDEV_SIZE ) ) {
        return -EINVAL;
    }
​
    filp->f_pos = newpos;
    return newpos;
}
​
static const struct file_operations mem_fops = { /* 文件操作结构体 */
    .owner   = THIS_MODULE,
    .llseek  = mem_llseek,
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
​
static void memdev_exit ( void ) { /* 模块卸载函数 */
    cdev_del ( &cdev ); /* 注销设备 */
    kfree ( mem_devp ); /* 释放设备结构体内存 */
    unregister_chrdev_region ( MKDEV ( mem_major, 0 ), 2 ); /* 释放设备号 */
}
​
MODULE_AUTHOR ( "www.enjoylinux.cn" );
MODULE_LICENSE ( "GPL" );
​
module_init ( memdev_init );
module_exit ( memdev_exit );
```

&emsp;&emsp;`app-mem.c`如下：

``` cpp
#include <stdio.h>
#include <string.h>
​
int main() {
    FILE *fp0 = NULL;
    char Buf[4096];
    strcpy ( Buf, "Mem is char dev!" ); /* 初始化Buf */
    printf ( "BUF: %s\n", Buf );
    fp0 = fopen ( "/dev/memdev", "r+" ); /* 打开设备文件 */
​
    if ( fp0 == NULL ) {
        printf ( "Open Memdev0 Error!\n" );
        return -1;
    }
​
    fwrite ( Buf, sizeof ( Buf ), 1, fp0 ); /* 写入设备 */
    fseek ( fp0, 0, SEEK_SET ); /* 重新定位文件位置(思考没有该指令，会有何后果) */
    strcpy ( Buf, "Buf is NULL!" ); /* 清除Buf */
    printf ( "BUF: %s\n", Buf );
    fread ( Buf, sizeof ( Buf ), 1, fp0 ); /* 读出设备 */
    printf ( "BUF: %s\n", Buf ); /* 检测结果 */
    return 0;
}
```