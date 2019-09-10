---
title: LED驱动_杂项设备
date: 2019-02-04 10:27:51
categories: Linux驱动程序
---
&emsp;&emsp;`memdev.h`如下：

``` cpp
#ifndef _MEMDEV_H_
#define _MEMDEV_H_
​
#include <linux/ioctl.h>
​
#define MEMDEV_IOC_MAGIC 'k' /* 定义幻数 */
​
/* 定义命令 */
#define MEMDEV_IOCON  _IO(MEMDEV_IOC_MAGIC, 1)
#define MEMDEV_IOCOFF _IO(MEMDEV_IOC_MAGIC, 2)
​
#define MEMDEV_IOC_MAXNR 2
​
#endif
```

&emsp;&emsp;`mini2440_leds_misc.c`如下：

``` cpp
#include <linux/miscdevice.h>
#include <linux/delay.h>
#include <asm/irq.h>
#include <mach/regs-gpio.h>
#include <mach/hardware.h>
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/init.h>
#include <linux/mm.h>
#include <linux/fs.h>
#include <linux/types.h>
#include <linux/delay.h>
#include <linux/moduleparam.h>
#include <linux/slab.h>
#include <linux/errno.h>
#include <linux/ioctl.h>
#include <linux/cdev.h>
#include <linux/string.h>
#include <linux/list.h>
#include <linux/pci.h>
#include <asm/uaccess.h>
#include <asm/atomic.h>
#include <asm/unistd.h>
#include <asm/io.h>
#include <asm/system.h>
#include <asm/uaccess.h>
#include "linux/gpio.h"
#include "memdev.h"
​
#define DEVICE_NAME "led"
​
static unsigned long led_table [] = {
    S3C2410_GPB ( 5 ),
    S3C2410_GPB ( 6 ),
    S3C2410_GPB ( 7 ),
    S3C2410_GPB ( 8 ),
};
​
static unsigned int led_cfg_table [] = {
    S3C2410_GPIO_OUTPUT,
    S3C2410_GPIO_OUTPUT,
    S3C2410_GPIO_OUTPUT,
    S3C2410_GPIO_OUTPUT,
};
​
static int sbc2440_leds_ioctl ( struct inode *inode, struct file *file, unsigned int cmd, unsigned long arg ) {
    int i = 0;
​
    /* 检测命令的有效性 */
    if ( _IOC_TYPE ( cmd ) != MEMDEV_IOC_MAGIC ) {
        return -EINVAL;
    }
​
    if ( _IOC_NR ( cmd ) > MEMDEV_IOC_MAXNR ) {
        return -EINVAL;
    }
​
    switch ( cmd ) { /* 根据命令，执行相应的操作 */
        case MEMDEV_IOCOFF: /* 灯全灭 */
            cmd = 1;
​
            for ( i = 0; i < 4; i++ ) {
                s3c2410_gpio_setpin ( led_table[i], cmd );
            }
​
            return 0;
​
        case MEMDEV_IOCON: /* 灯全亮 */
            for ( i = 0; i < 4; i++ ) {
                s3c2410_gpio_setpin ( led_table[i], !cmd );
            }
​
            return 0;
​
        default:
            return -EINVAL;
    }
}
​
static struct file_operations dev_fops = { /* 文件操作结构体 */
    .owner = THIS_MODULE,
    .ioctl = sbc2440_leds_ioctl,
};
​
static struct miscdevice misc = {
    .minor = MISC_DYNAMIC_MINOR,
    .name  = DEVICE_NAME,
    .fops  = &dev_fops,
};
​
static int __init dev_init ( void ) {
    int ret;
    int i;
​
    /* 设置GPIO控制寄存器，GPIO设置为输出模式，默认下灯全灭 */
    for ( i = 0; i < 4; i++ ) {
        s3c2410_gpio_cfgpin ( led_table[i], led_cfg_table[i] );
        s3c2410_gpio_setpin ( led_table[i], 1 );
    }
​
    ret = misc_register ( &misc ); /* 注册混杂型字符设备驱动 */
    printk ( DEVICE_NAME"\tinitialized\n" );
    return ret;
}
​
static void __exit dev_exit ( void ) {
    misc_deregister ( &misc ); /* 注销混杂型字符设备驱动 */
}
​
module_init ( dev_init );
module_exit ( dev_exit );
MODULE_LICENSE ( "GPL" );
```

&emsp;&emsp;`app-led.c`如下：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/ioctl.h>
#include "memdev.h" /* 包含命令定义 */
​
int main ( int argc, char **argv ) {
    int on;
    int fd;
​
    if ( argc != 2 || sscanf ( argv[1], "%d", &on ) != 1 || on < 0 || on > 1 ) {
        fprintf ( stderr, "Usage:%s 0|1\n", argv[0] );
        exit ( 1 );
    }
​
    fd = open ( "/dev/led", 0 );
​
    if ( fd < 0 ) {
        perror ( "open device leds" );
        exit ( 1 );
    }
​
    /* 通过ioctl来控制灯的亮、灭 */
    if ( on ) {
        printf ( "turn on all leds!\n" );
        ioctl ( fd,  MEMDEV_IOCON, 0 );
    } else {
        printf ( "turn off all leds!\n" );
        ioctl ( fd,  MEMDEV_IOCOFF, 0 );
    }
​
    close ( fd );
    return 0;
}
```

查看`/dev`文件夹，看是否有`/dev/led`文件。如果有，则执行程序`app-led`。