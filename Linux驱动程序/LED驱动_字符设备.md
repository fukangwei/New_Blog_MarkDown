---
title: LED驱动_字符设备
date: 2019-02-04 10:59:29
categories: Linux驱动程序
---
&emsp;&emsp;实现`LED`驱动测试案例及要求：

``` bash
led_test on    /* 对应四个LED全亮           */
led_test off   /* 对应四个LED全灭           */
led_test run   /* 运行跑马灯实验            */
led_test shine /* 4个LED灯全灭、全亮交替闪烁 */
led_test 1 on  /* 对应LED1点亮              */
led_test 1 off /* 对应LED1熄灭              */
...
led_test 4 on  /* 对应LED4点亮              */
led_test 4 off /* 对应LED4熄灭              */
```

&emsp;&emsp;设备驱动如下：

``` cpp
#include <linux/errno.h>
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/slab.h>
#include <linux/input.h>
#include <linux/init.h>
#include <linux/serio.h>
#include <linux/delay.h>
#include <linux/clk.h>
#include <linux/miscdevice.h>
#include <linux/gpio.h>
#include <asm/io.h>
#include <asm/irq.h>
#include <asm/uaccess.h>
#include <mach/regs-clock.h>
#include <plat/regs-timer.h>
#include <linux/cdev.h>
#include <mach/regs-gpio.h> /* 包含GPIO口的封装 */
​
static int led_major = 0; /* 主设备号 */
static struct cdev LedDevs;
​
/* 应用程序执行ioctl(fd, cmd, arg)时的第2个参数 */
#define LED_MAGIC 'k'
#define IOCTL_LED_ON     _IOW (LED_MAGIC, 1, int)
#define IOCTL_LED_OFF    _IOW (LED_MAGIC, 2, int)
#define IOCTL_LED_RUN    _IOW (LED_MAGIC, 3, int)
#define IOCTL_LED_SHINE  _IOW (LED_MAGIC, 4, int)
#define IOCTL_LED_ALLON  _IOW (LED_MAGIC, 5, int)
#define IOCTL_LED_ALLOFF _IOW (LED_MAGIC, 6, int)
​
static unsigned long led_table [] = { /* 用来指定LED所用的GPIO引脚 */
    S3C2410_GPB ( 5 ),
    S3C2410_GPB ( 6 ),
    S3C2410_GPB ( 7 ),
    S3C2410_GPB ( 8 ),
};
​
/* 应用程序对设备文件“/dev/led”执行open时，就会调用s3c24xx_leds_open函数 */
static int s3c2440_leds_open ( struct inode *inode, struct file *file ) {
    int i;
​
    for ( i = 0; i < 4; i++ ) {
        /* 设置GPIO引脚的功能：本驱动中LED所涉及的GPIO引脚设为输出功能 */
        s3c2410_gpio_cfgpin ( led_table[i], S3C2410_GPIO_OUTPUT );
    }
​
    return 0;
}
​
void leds_all_on() { /* LEDS all light on */
    int i;
​
    for ( i = 0; i < 4; i++ ) {
        s3c2410_gpio_setpin ( led_table[i], 0 );
    }
}
​
void leds_all_off() { /* LEDs all light off */
    int i;
​
    for ( i = 0; i < 4; i++ ) {
        s3c2410_gpio_setpin ( led_table[i], 1 );
    }
}
​
/* 应用程序对设备文件“/dev/leds”执行ioctl时，就会调用s3c24xx_leds_ioctl函数 */
static int s3c2440_leds_ioctl ( struct inode *inode, struct file *file,
                                unsigned int cmd, unsigned long arg ) {
    unsigned int data;
​
    /*
     * if ( copy_from_user ( &data, ( unsigned int __user * ) arg, sizeof ( int ) ) ) {
     *     return -EFAULT;
     * }
​     *
     * data = ( unsigned int ) arg; // 方法一：数据传递
     */
    if ( __get_user ( data, ( unsigned int __user * ) arg ) ) { /* 方法二：指针参数传递 */
        return -EFAULT;
    }
​
    switch ( cmd ) {
        case IOCTL_LED_ON:
            s3c2410_gpio_setpin ( led_table[data], 0 ); /* 设置指定引脚的输出电平为0 */
            return 0;
​
        case IOCTL_LED_OFF:
            s3c2410_gpio_setpin ( led_table[data], 1 ); /* 设置指定引脚的输出电平为1 */
            return 0;
​
        case IOCTL_LED_RUN: { /* 跑马灯 */
            int i, j;
            leds_all_off();
​
            for ( i = 0; i < data; i++ ) /* 跑data次 */
                for ( j = 0; j < 4; j++ ) {
                    s3c2410_gpio_setpin ( led_table[j], 0 ); /* 亮 */
                    mdelay ( 400 ); /* mdelay一直等待，msleep休眠则执行别的进程 */
                    s3c2410_gpio_setpin ( led_table[j], 1 ); /* 灭 */
                    mdelay ( 400 );
                }
​
            return 0;
        }
​
        case IOCTL_LED_SHINE: { /* LED闪烁 */
            int i, j;
            leds_all_off();
            printk ( "IOCTL_LED_SHINE\n" );
​
            for ( i = 0; i < data; i++ ) {
                for ( j = 0; j < 4; j++ ) {
                    s3c2410_gpio_setpin ( led_table[j], 0 );
                }
​
                mdelay ( 400 ); /* 延时400ms */
​
                for ( j = 0; j < 4; j++ ) {
                    s3c2410_gpio_setpin ( led_table[j], 1 );
                }
​
                mdelay ( 400 );
            }
​
            return 0;
        }
​
        case IOCTL_LED_ALLON: /* 设置指定引脚的输出电平为0 */
            leds_all_on();
            return 0;
​
        case IOCTL_LED_ALLOFF: /* 设置指定引脚的输出电平为1 */
            leds_all_off();
            return 0;
​
        default:
            return -EINVAL;
    }
}
​
/* 这个结构是字符设备驱动程序的核心，当应用程序操作设备文件时所调用的
   open、read、write等函数，最终会调用这个结构中指定的对应函数 */
static struct file_operations s3c2440_leds_fops = {
    .owner = THIS_MODULE, /* 这是一个宏，指向编译模块时自动创建的__this_module变量 */
    .open  = s3c2440_leds_open,
    .ioctl = s3c2440_leds_ioctl,
};
​
/* Set up the cdev structure for a device */
static void led_setup_cdev ( struct cdev *dev, int minor, struct file_operations *fops ) {
    int err, devno = MKDEV ( led_major, minor );
    cdev_init ( dev, fops ); /* 设备的注册 */
    dev->owner = THIS_MODULE;
    dev->ops = fops;
    err = cdev_add ( dev, devno, 1 );
​
    if ( err ) { /* Fail gracefully if need be */
        printk ( KERN_NOTICE "Error %d adding Led%d", err, minor );
    }
}
​
/* 执行“insmod s3c24xx_leds.ko”命令时就会调用这个函数 */
static int __init s3c2440_leds_init ( void ) {
    int result;
    dev_t dev = MKDEV ( led_major, 0 );
    char dev_name[] = "led"; /* 加载模式后，执行“cat /proc/devices”命令看到的设备名称 */
​
    if ( led_major ) { /* Figure out our device number */
        result = register_chrdev_region ( dev, 1, dev_name );
    } else {
        result = alloc_chrdev_region ( &dev, 0, 1, dev_name );
        led_major = MAJOR ( dev );
    }
​
    if ( result < 0 ) {
        printk ( KERN_WARNING "leds: unable to get major %d\n", led_major );
        return result;
    }
​
    if ( led_major == 0 ) {
        led_major = result;
    }
​
    led_setup_cdev ( &LedDevs, 0, &s3c2440_leds_fops ); /* Now set up cdev */
    printk ( "Led device installed, with major %d\n", led_major );
    printk ( "The device name is: %s\n", dev_name );
    return 0;
}
​
/* 执行“rmmod s3c24xx_leds”命令时就会调用这个函数 */
static void __exit s3c2440_leds_exit ( void ) {
    cdev_del ( &LedDevs ); /* 卸载驱动程序 */
    unregister_chrdev_region ( MKDEV ( led_major, 0 ), 1 );
    printk ( "Led device uninstalled\n" );
}
​
/* 这两行指定驱动程序的初始化函数和卸载函数 */
module_init ( s3c2440_leds_init );
module_exit ( s3c2440_leds_exit );
​
/* 描述驱动程序的一些信息 */
MODULE_AUTHOR ( "http://embedclub.taobao.com" ); /* 驱动程序的作者 */
MODULE_DESCRIPTION ( "s3c2440 LED Driver" ); /* 一些描述信息 */
MODULE_LICENSE ( "Dual BSD/GPL" ); /* 遵循的协议 */
```

&emsp;&emsp;测试程序如下：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/ioctl.h>
​
#define LED_MAGIC 'k'
#define IOCTL_LED_ON     _IOW (LED_MAGIC, 1, int)
#define IOCTL_LED_OFF    _IOW (LED_MAGIC, 2, int)
#define IOCTL_LED_RUN    _IOW (LED_MAGIC, 3, int)
#define IOCTL_LED_SHINE  _IOW (LED_MAGIC, 4, int)
#define IOCTL_LED_ALLON  _IOW (LED_MAGIC, 5, int)
#define IOCTL_LED_ALLOFF _IOW (LED_MAGIC, 6, int)
​
void usage ( char *exename ) {
    printf ( "Usage:\n" );
    printf ( "    %s <led_no> <on/off>\n", exename );
    printf ( "    led_no = 1, 2, 3 or 4\n" );
}
​
int main ( int argc, char **argv ) {
    unsigned int led_no;
    int fd = -1;
    unsigned int count = 10;
​
    if ( argc > 3 || argc == 1 ) {
        goto err;
    }
​
    fd = open ( "/dev/led", 0 ); /* 打开设备 */
​
    if ( fd < 0 ) {
        printf ( "Can't open /dev/leds\n" );
        return -1;
    }
​
    if ( argc == 2 ) {
        if ( !strcmp ( argv[1], "on" ) ) {
            ioctl ( fd, IOCTL_LED_ALLON, &count ); /* 点亮它 */
        } else if ( !strcmp ( argv[1], "off" ) ) {
            ioctl ( fd, IOCTL_LED_ALLOFF, &count ); /* 熄灭它 */
        } else if ( !strcmp ( argv[1], "run" ) ) {
            ioctl ( fd, IOCTL_LED_RUN, &count ); /* 运行跑马灯 */
        } else if ( !strcmp ( argv[1], "shine" ) ) {
            ioctl ( fd, IOCTL_LED_SHINE, &count ); /* 闪烁 */
        } else {
            goto err;
        }
    }
​
    if ( argc == 3 ) {
        led_no = strtoul ( argv[1], NULL, 0 ) - 1; /* 操作哪个LED */
​
        if ( led_no > 3 ) {
            goto err;
        }
​
        if ( !strcmp ( argv[2], "on" ) ) {
            ioctl ( fd, IOCTL_LED_ON, &led_no ); /* 点亮 */
        } else if ( !strcmp ( argv[2], "off" ) ) {
            ioctl ( fd, IOCTL_LED_OFF, &led_no ); /* 熄灭 */
        } else {
            goto err;
        }
    }
​
    close ( fd );
    return 0;
err:
​
    if ( fd > 0 ) {
        close ( fd );
    }
​
    usage ( argv[0] );
    return -1;
}
```