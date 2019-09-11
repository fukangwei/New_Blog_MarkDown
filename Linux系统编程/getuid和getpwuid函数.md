---
title: getuid和getpwuid函数
date: 2018-12-29 16:34:02
categories: Linux系统编程
---
&emsp;&emsp;`getuid`函数原型如下：

``` c
#include <sys/types.h>
#include <unistd.h>
uid_t getuid ( void );
```

`uid_t`定义在头文件`sys/types.h`中，它通常是一个`int`类型。函数返回一个调用程序的真实用户`ID`，一般来说这个函数都是会调用成功的。
&emsp;&emsp;`getpwuid`函数原型如下：

``` c
#include <sys/types.h>
#include <pwd.h>
struct passwd *getpwuid ( uid_t uid );
```

`getpwuid`函数是通过用户的`uid`查找用户的`passwd`数据。如果出错，它们都返回一个空指针，并设置`errno`的值，用户可以根据`perror`函数查看出错的信息。
&emsp;&emsp;`passwd`结构体定义在头文件`pwd.h`中：

``` c
struct passwd {
    char *pw_name;   /* 用户名登录   */
    char *pw_passwd; /* 用户密码     */
    uid_t pw_uid;    /* 用户id       */
    gid_t pw_gid;    /* 组id         */
    char *pw_gecos;  /* 用户全名      */
    char *pw_dir;    /* 用户家目录    */
    char *pw_shell;  /* 用户默认shell */
};
```

代码示例：

``` c
#include <sys/types.h>
#include <pwd.h>
#include <stdio.h>
#include <unistd.h>
​
int main() {
    uid_t uid;
    uid = getuid();
    printf ( "User IDs: uid=%d\n", uid );
    uid_t my_uid;
    struct passwd *my_info;
    my_info = getpwuid ( getuid() );
    printf ( "my name = [%s]\n", my_info->pw_name );
    printf ( "my passwd = [%s]\n", my_info->pw_passwd );
    printf ( "my uid = [%d]\n", my_info->pw_uid );
    printf ( "my gid = [%d]\n", my_info->pw_gid );
    printf ( "my gecos = [%s]\n", my_info->pw_gecos );
    printf ( "my dir = [%s]\n", my_info->pw_dir );
    printf ( "my shell = [%s]\n", my_info->pw_shell );
    return 0;
}
```

执行结果：

``` bash
User IDs: uid=1000
my name = [fuxinzi]
my passwd = [x]
my uid = [1000]
my gid = [1000]
my gecos = [fuxinzi,,,]
my dir = [/home/fuxinzi]
my shell = [/bin/bash]
```

可以看到先用`getuid`获取到用户`id`号，然后使用`getpwuid`获取到了用户的相关信息。这个函数的实现机制其实和`/etc/passwd`文件相关，其内容如下：

``` bash
rtkit:x:119:127:RealtimeKit,,,:/proc:/bin/false
saned:x:120:128::/var/lib/saned:/bin/false
usbmux:x:121:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
fuxinzi:x:1000:1000:fuxinzi,,,:/home/fuxinzi:/bin/bash
sshd:x:122:65534::/var/run/sshd:/usr/sbin/nologin
```

结构体`passwd`和这个文件中是一一对应的，实际上这个函数实现的就是获取`passwd`文件中的信息。