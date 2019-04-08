---
title: platform设备驱动
date: 2019-02-04 10:00:45
tags:
---
&emsp;&emsp;`device.c`如下：

``` cpp
#include <linux/device.h>
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/string.h>
#include <linux/platform_device.h>
​
static struct platform_device *my_device;
​
static int __init my_device_init ( void ) {
    int ret = 0;
    my_device = platform_device_alloc ( "my_dev", -1 ); /* 分配结构 */
    ret = platform_device_add ( my_device ); /* 注册设备 */
​
    if ( ret ) { /* 注册失败，释放相关内存 */
        platform_device_put ( my_device );
    }
​
    return ret;
}
​
static void my_device_exit ( void ) {
    platform_device_unregister ( my_device );
}
​
module_init ( my_device_init );
module_exit ( my_device_exit );
MODULE_LICENSE ( "Dual BSD/GPL" );
```

&emsp;&emsp;`driver.c`如下：

``` cpp
#include <linux/device.h>
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/string.h>
#include <linux/platform_device.h>
​
static int my_probe ( struct device *dev ) {
    printk ( "Driver found device which my driver can handle!\n" );
    return 0;
}
​
static int my_remove ( struct device *dev ) {
    printk ( "Driver found device unpluged!\n" );
    return 0;
}
​
static struct platform_driver my_driver = {
    .probe  = my_probe,
    .remove = my_remove,
    .driver = {
        .owner = THIS_MODULE,
        .name  = "my_dev",
    },
};
​
static int __init my_driver_init ( void ) {
    return platform_driver_register ( &my_driver ); /* 注册平台驱动 */
}
​
static void my_driver_exit ( void ) {
    platform_driver_unregister ( &my_driver );
}
​
module_init ( my_driver_init );
module_exit ( my_driver_exit );
MODULE_LICENSE ( "Dual BSD/GPL" );
```

首先生成`device.ko`和`driver.ko`，然后下载进开发板。使用命令`ls -al /sys/bus/platform/devices/`，观察是否生成`my_device`文件；使用命令`ls -al /sys/bus/platform/drivers/`，观察是否生成`my_dev`文件夹。