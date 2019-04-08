---
title: Linux内核链表
date: 2018-12-29 14:32:07
tags:
---
&emsp;&emsp;`linux`内核链表不是在链表节点中包含数据，而是在数据结构中包含链表节点。其在`linux`内核中的定义为：

``` c
struct autofs {
    char *name;
    int len;
    struct list_head list;
}
```

使用示例如下：

``` c
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/init.h>
#include "linux/fs.h"
#include <linux/slab.h>
#include <linux/list.h>
​
MODULE_LICENSE ( "GPL" );
​
struct student {
    char name[100];
    int num;
    struct list_head list;
};
​
struct student *pstudent;
struct student *tmp_student;
struct list_head student_list;
struct list_head *pos;
​
int mylist_init ( void ) {
    int i = 0;
    INIT_LIST_HEAD ( &student_list );
    pstudent = kmalloc ( sizeof ( struct student ) * 5, GFP_KERNEL );
    memset ( pstudent, 0, sizeof ( struct student ) * 5 );
​
    for ( i = 0; i < 5; i++ ) {
        sprintf ( pstudent[i].name, "Student%d", i + 1 );
        pstudent[i].num = i + 1;
        list_add ( & ( pstudent[i].list ), &student_list );
    }
​
    list_for_each ( pos, &student_list ) {
        tmp_student = list_entry ( pos, struct student, list );
        printk ( "student %d name: %s\n", tmp_student->num, tmp_student->name );
    }
    return 0;
}
​
void mylist_exit ( void ) {
    int i;
​
    for ( i = 0; i < 5; i++ ) {
        list_del ( & ( pstudent[i].list ) );
    }
​
    kfree ( pstudent );
}
​
module_init ( mylist_init );
module_exit ( mylist_exit );
```