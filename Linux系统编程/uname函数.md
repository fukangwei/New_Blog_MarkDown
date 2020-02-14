---
title: uname函数
categories: Linux系统编程
abbrlink: 32cb2964
date: 2018-12-29 14:40:07
---
&emsp;&emsp;其功能是获取当前内核名称和其它信息：<!--more-->

``` cpp
#include <sys/utsname.h>
extern int uname ( struct utsname *__name ) __THROW;
```

参数`__name`指向存放系统信息的缓冲区，其原型如下：

``` cpp
struct utsname {
    char sysname[_UTSNAME_SYSNAME_LENGTH];   /* 当前操作系统名  */
    char nodename[_UTSNAME_NODENAME_LENGTH]; /* 网络上的名称    */
    char release[_UTSNAME_RELEASE_LENGTH];   /* 当前发布级别    */
    char version[_UTSNAME_VERSION_LENGTH];   /* 当前发布版本    */
    char machine[_UTSNAME_MACHINE_LENGTH];   /* 当前硬件体系类型 */
#if _UTSNAME_DOMAIN_LENGTH - 0
    /* Name of the domain of this node on the network */
    #ifdef __USE_GNU
        char domainname[_UTSNAME_DOMAIN_LENGTH]; /* 当前域名 */
    #else
        char __domainname[_UTSNAME_DOMAIN_LENGTH];
    #endif
#endif
};
```

执行成功返回`0`，失败返回`-1`，`errno`被设为`EFAULT`，表示`buf`无效。

``` cpp
#include <sys/utsname.h>
#include <stdio.h>
#include <stdlib.h>
​
int main() {
    struct utsname testbuff;
    int fb = 0;
    fb = uname ( &testbuff );
​
    if ( fb < 0 ) {
        perror ( "uname" );
        return 0;
    } else {
        printf ( \
            "sysname: %s\n nodename: %s\n release: %s\n version: %s\n machine: %s\n", \
            testbuff.sysname, \
            testbuff.nodename, \
            testbuff.release, \
            testbuff.version, \
            testbuff.machine );
​
#if _UTSNAME_DOMAIN_LENGTH - 0
    # ifdef __USE_GNU
        printf ( " domainame: %s\n ", testbuff.domainname );
    # else
        printf ( " __domainame: %s\n ", testbuff.__domainname );
    # endif
#endif
    }
​
    return 0;
}
```