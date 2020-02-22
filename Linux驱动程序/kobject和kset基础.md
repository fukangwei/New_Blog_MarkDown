---
title: kobject和kset基础
categories: Linux驱动程序
date: 2019-02-04 10:22:53
---
&emsp;&emsp;`kobject.c`如下：<!--more-->

``` cpp
#include <linux/device.h>
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/string.h>
#include <linux/sysfs.h>
#include <linux/stat.h>

void obj_test_release ( struct kobject *kobject );
ssize_t kobj_test_show ( struct kobject *kobject, struct attribute *attr, char *buf );
ssize_t kobj_test_store ( struct kobject *kobject, struct attribute *attr, const char *buf, size_t count );

struct attribute test_attr = {
    .name = "kobj_config",
    .mode = S_IRWXUGO,
};

static struct attribute *def_attrs[] = {
    &test_attr,
    NULL,
};

struct sysfs_ops obj_test_sysops = {
    .show = kobj_test_show,
    .store = kobj_test_store,
};

struct kobj_type ktype = {
    .release = obj_test_release,
    .sysfs_ops = &obj_test_sysops,
    .default_attrs = def_attrs,
};

void obj_test_release ( struct kobject *kobject ) {
    printk ( "eric_test: release .\n" );
}

ssize_t kobj_test_show ( struct kobject *kobject, struct attribute *attr, char *buf ) {
    printk ( "have show.\n" );
    printk ( "attrname:%s.\n", attr->name );
    sprintf ( buf, "%s\n", attr->name );
    return strlen ( attr->name ) + 2;
}

ssize_t kobj_test_store ( struct kobject *kobject, struct attribute *attr,
                          const char *buf, size_t count ) {
    printk ( "havestore\n" );
    printk ( "write: %s\n", buf );
    return count;
}

struct kobject kobj;

static int kobj_test_init() {
    printk ( "kboject test init.\n" );
    kobject_init_and_add ( &kobj, &ktype, NULL, "kobject_test" );
    return 0;
}

static int kobj_test_exit() {
    printk ( "kobject test exit.\n" );
    kobject_del ( &kobj );
    return 0;
}

module_init ( kobj_test_init );
module_exit ( kobj_test_exit );
MODULE_LICENSE ( "Dual BSD/GPL" );
```

生成`kobject.ko`并下载进开发板，然后使用命令`insmod kobject.ko`，观察`/sys`目录下有没有生成`kobject_test`文件夹。然后进入`kobject_test`，观察是否生成`kobj_config`文件，再使用命令`cat kobj_config`观察结果。
&emsp;&emsp;`kset.c`如下：

``` cpp
#include <linux/device.h>
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/string.h>
#include <linux/sysfs.h>
#include <linux/stat.h>
#include <linux/kobject.h>

struct kset kset_p;
struct kset kset_c;

int kset_filter ( struct kset *kset, struct kobject *kobj ) {
    printk ( "Filter: kobj %s.\n", kobj->name );
    return 1;
}

const char *kset_name ( struct kset *kset, struct kobject *kobj ) {
    static char buf[20];
    printk ( "Name: kobj %s.\n", kobj->name );
    sprintf ( buf, "%s", "kset_name" );
    return buf;
}

int kset_uevent ( struct kset *kset, struct kobject *kobj, struct kobj_uevent_env *env ) {
    int i = 0;
    printk ( "uevent: kobj %s.\n", kobj->name );

    while ( i < env->envp_idx ) {
        printk ( "%s.\n", env->envp[i] );
        i++;
    }

    return 0;
}

struct kset_uevent_ops uevent_ops = {
    .filter = kset_filter,
    .name   = kset_name,
    .uevent = kset_uevent,
};

int kset_test_init() {
    printk ( "kset test init.\n" );
    kobject_set_name ( &kset_p.kobj, "kset_p" );
    kset_p.uevent_ops = &uevent_ops;
    kset_register ( &kset_p );
    kobject_set_name ( &kset_c.kobj, "kset_c" );
    kset_c.kobj.kset = &kset_p;
    kset_register ( &kset_c );
    return 0;
}

int kset_test_exit() {
    printk ( "kset test exit.\n" );
    kset_unregister ( &kset_p );
    kset_unregister ( &kset_c );
    return 0;
}

module_init ( kset_test_init );
module_exit ( kset_test_exit );
MODULE_LICENSE ( "Dual BSD/GPL" );
```

生成`kset.ko`模块并下载进开发板，然后安装该模块，观察`/sys`目录下有没有生成`kset_p`目录，`kset_p`目录下有没有生成`kset_c`目录。