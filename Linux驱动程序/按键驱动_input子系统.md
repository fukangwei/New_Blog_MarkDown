---
title: 按键驱动_input子系统
date: 2019-02-04 10:50:27
categories: Linux驱动程序
---
&emsp;&emsp;`input_button.c`如下：

``` cpp
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/fs.h>
#include <linux/init.h>
#include <linux/delay.h>
#include <linux/poll.h>
#include <linux/irq.h>
#include <asm/irq.h>
#include <linux/interrupt.h>
#include <asm/uaccess.h>
#include <mach/regs-gpio.h>
#include <mach/hardware.h>
#include <linux/platform_device.h>
#include <linux/cdev.h>
#include <linux/miscdevice.h>
#include <linux/input.h>
#include <linux/gpio.h>
​
struct input_dev *button_dev;
​
struct button_irq_desc {
    int irq;
    int pin;
    int pin_setting;
    int number;
    char *name;
};
​
static struct button_irq_desc button_irqs [] = {
    {IRQ_EINT8,  S3C2410_GPG ( 0 ),  S3C2410_GPG0_EINT8,   0, "KEY0"},
    {IRQ_EINT11, S3C2410_GPG ( 3 ),  S3C2410_GPG3_EINT11,  1, "KEY1"},
    {IRQ_EINT13, S3C2410_GPG ( 5 ),  S3C2410_GPG5_EINT13,  2, "KEY2"},
    {IRQ_EINT14, S3C2410_GPG ( 6 ),  S3C2410_GPG6_EINT14,  3, "KEY3"},
    {IRQ_EINT15, S3C2410_GPG ( 7 ),  S3C2410_GPG7_EINT15,  4, "KEY4"},
    {IRQ_EINT19, S3C2410_GPG ( 11 ), S3C2410_GPG11_EINT19, 5, "KEY5"},
};
​
static int key_values = 0;
​
static irqreturn_t buttons_interrupt ( int irq, void *dev_id ) {
    struct button_irq_desc *button_irqs = ( struct button_irq_desc * ) dev_id;
    int down;
    udelay ( 0 );
    down = !s3c2410_gpio_getpin ( button_irqs->pin ); /* down：1(按下)，0(弹起) */
​
    if ( !down ) {
        key_values = button_irqs->number; /* 报告事件 */
​
        if ( key_values == 0 ) {
            input_report_key ( button_dev, KEY_1, 0 );
        }
​
        if ( key_values == 1 ) {
            input_report_key ( button_dev, KEY_2, 0 );
        }
​
        if ( key_values == 2 ) {
            input_report_key ( button_dev, KEY_3, 0 );
        }
​
        if ( key_values == 3 ) {
            input_report_key ( button_dev, KEY_4, 0 );
        }
​
        if ( key_values == 4 ) {
            input_report_key ( button_dev, KEY_5, 0 );
        }
​
        if ( key_values == 5 ) {
            input_report_key ( button_dev, KEY_6, 0 );
        }
​
        input_sync ( button_dev );
    } else {
        key_values = button_irqs->number;
​
        if ( key_values == 0 ) {
            input_report_key ( button_dev, KEY_1, 1 );
        }
​
        if ( key_values == 1 ) {
            input_report_key ( button_dev, KEY_2, 1 );
        }
​
        if ( key_values == 2 ) {
            input_report_key ( button_dev, KEY_3, 1 );
        }
​
        if ( key_values == 3 ) {
            input_report_key ( button_dev, KEY_4, 1 );
        }
​
        if ( key_values == 4 ) {
            input_report_key ( button_dev, KEY_5, 1 );
        }
​
        if ( key_values == 5 ) {
            input_report_key ( button_dev, KEY_6, 1 );
        }
​
        input_sync ( button_dev );
    }
​
    return IRQ_RETVAL ( IRQ_HANDLED );
}

static int s3c24xx_request_irq ( void ) {
    int i;
    int err = 0;
​
    for ( i = 0; i < sizeof ( button_irqs ) / sizeof ( button_irqs[0] ); i++ ) {
        if ( button_irqs[i].irq < 0 ) {
            continue;
        }
​
        /* IRQ_TYPE_EDGE_FALLING、IRQ_TYPE_EDGE_RISING、IRQ_TYPE_EDGE_BOTH */
        err = request_irq ( button_irqs[i].irq, buttons_interrupt, IRQ_TYPE_EDGE_BOTH, \
                            button_irqs[i].name, ( void * ) &button_irqs[i] );
​
        if ( err ) {
            break;
        }
    }
​
    if ( err ) {
        i--;
​
        for ( ; i >= 0; i-- ) {
            if ( button_irqs[i].irq < 0 ) {
                continue;
            }
​
            disable_irq ( button_irqs[i].irq );
            free_irq ( button_irqs[i].irq, ( void * ) &button_irqs[i] );
        }
​
        return -EBUSY;
    }
​
    return 0;
}
​
static int __init dev_init ( void ) {
    s3c24xx_request_irq(); /* request irq */
    button_dev = input_allocate_device(); /* Initialise input stuff */
​
    if ( !button_dev ) {
        printk ( KERN_ERR "Unable to allocate the input device !!\n" );
        return -ENOMEM;
    }
​
    button_dev->name = "s3c2440_button";
    button_dev->id.bustype = BUS_RS232;
    button_dev->id.vendor = 0xDEAD;
    button_dev->id.product = 0xBEEF;
    button_dev->id.version = 0x0100;
    button_dev->evbit[0] = BIT_MASK ( EV_KEY ) | BIT ( EV_SYN );
    // set_bit ( EV_KEY, button_dev->evbit ) /* 支持EV_KEY事件 */
    set_bit ( KEY_1, button_dev->keybit );
    set_bit ( KEY_2, button_dev->keybit );
    set_bit ( KEY_3, button_dev->keybit );
    set_bit ( KEY_4, button_dev->keybit );
    set_bit ( KEY_5, button_dev->keybit );
    set_bit ( KEY_6, button_dev->keybit );
    // printk ( "KEY_RESERVED = %d, KEY_1 = %d", KEY_RESERVED, KEY_1 );
    input_register_device ( button_dev ); /* 注册input设备 */
    printk ( "initialized\n" );
    return 0;
}

static void __exit dev_exit ( void ) {
    int i;
​
    for ( i = 0; i < sizeof ( button_irqs ) / sizeof ( button_irqs[0] ); i++ ) {
        if ( button_irqs[i].irq < 0 ) {
            continue;
        }
​
        free_irq ( button_irqs[i].irq, ( void * ) &button_irqs[i] );
    }
​
    input_unregister_device ( button_dev );
}
​
module_init ( dev_init );
module_exit ( dev_exit );
MODULE_LICENSE ( "GPL" );
```

&emsp;&emsp;`app_input.c`如下：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/ioctl.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <sys/select.h>
#include <sys/time.h>
#include <errno.h>
#include <linux/input.h>
​
int main ( void ) {
    int buttons_fd;
    int key_value, i = 0, count;
    struct input_event ev_key;
    buttons_fd = open ( "/dev/input/event1", O_RDWR );
​
    if ( buttons_fd < 0 ) {
        perror ( "open device buttons" );
        exit ( 1 );
    }
​
    for ( ;; ) {
        count = read ( buttons_fd, &ev_key, sizeof ( struct input_event ) );
​
        for ( i = 0; i < ( int ) count / sizeof ( struct input_event ); i++ ) {
            if ( EV_KEY == ev_key.type ) {
                printf ( "type:%d,code:%d,value:%d\n", ev_key.type, ev_key.code - 1, ev_key.value );
            }
​
            if ( EV_SYN == ev_key.type ) {
                printf ( "syn event\n\n" );
            }
        }
    }
​
    close ( buttons_fd );
    return 0;
}
```

生成`input_button.ko`，然后下载并安装。我在开发板上生成的是`/dev/input/event1`，于是应用程序打开的对应文件是`/dev/input/event1`。