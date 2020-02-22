---
title: current指针
categories: Linux系统编程
date: 2018-12-29 14:35:53
---
&emsp;&emsp;`current`指针的用法`1`：<!--more-->

``` cpp
#include <linux/init.h>
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/sched.h>

static int __init my_init ( void ) {
    printk ( "[kernel init] %s %d\n", __func__, __LINE__ );
    printk ( "[kernel init] %s %d\n", current->comm, current->pid );
    return 0;
}

static void __exit my_exit ( void ) {
    printk ( "[kernel init] %s %d\n", __func__, __LINE__ );
    printk ( "[kernel init] %s %d\n", current->comm, current->pid );
}

module_init ( my_init );
module_exit ( my_exit );

MODULE_LICENSE ( "GPL" );
MODULE_AUTHOR ( "Millet" );
MODULE_VERSION ( "MI 3S" );
MODULE_DESCRIPTION ( "test for thread info" );
```

&emsp;&emsp;`current`指针的用法`2`：

``` cpp
#include <linux/init.h>
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/sched.h>

static int __init my_init ( void ) {
    int a;
    struct thread_info *ti;
    struct task_struct *mycurrent;
    ti = ( struct thread_info * ) ( ( ( unsigned long ) &a ) & ( ~0x1fff ) );
    mycurrent = ti->task;
    printk ( "[*kernel init*] %s %d\n", mycurrent->comm, mycurrent->pid );
    printk ( "[kernel init] %s %d\n", __func__, __LINE__ );
    printk ( "[kernel init] %s %d\n", current->comm, current->pid );
    return 0;
}

static void __exit my_exit ( void ) {
    printk ( "[kernel init] %s %d\n", __func__, __LINE__ );
    printk ( "[kernel init] %s %d\n", current->comm, current->pid );
}

module_init ( my_init );
module_exit ( my_exit );

MODULE_LICENSE ( "GPL" );
MODULE_AUTHOR ( "Millet" );
MODULE_VERSION ( "MI 3S" );
MODULE_DESCRIPTION ( "test for thread info" );
```