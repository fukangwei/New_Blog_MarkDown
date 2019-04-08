---
title: setsockopt函数
date: 2018-12-29 14:46:55
tags:
---
&emsp;&emsp;当客户端保持着与服务器端的连接，这时服务器端断开，再开启服务器时会出现`Address already in use`，可以用`netstat -anp | more`看到客户端还保持着与服务器的连接(还在使用服务器`bind`的端口)。这是由于`client`没有执行`close`，连接还会等待`client`的`FIN`包一段时间。
&emsp;&emsp;解决方法是使用`setsockopt`，使得`socket`可以被重用，它是最常用的服务器编程要点。具体的做法为，在`socket`调用和`bind`调用之间加上一段对`socket`设置的代码：

``` c
int opt = 1;
setsockopt ( socket_fd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof ( opt ) );
```

其函数原型如下所示：

``` c
#include <sys/types.h>
#include <sys/socket.h>
int setsockopt ( int s, int level, int optname, const void *optval, socklen_toptlen );
```

`setsockopt`用来设置参数`s`所指定的`socket`状态。`level`代表欲设置的网络层，一般设成`SOL_SOCKET`以存取`socket`层；`optname`代表欲设置的选项，有下列几种数值：

- `SO_DEBUG`：打开或关闭排错模式。
- `SO_REUSEADDR`：允许在`bind`过程中本地地址可重复使用。
- `SO_TYPE`：返回`socket`形态。
- `SO_ERROR`：返回`socket`已发生的错误原因。
- `SO_DONTROUTE`：送出的数据包不要利用路由设备来传输。
- `SO_BROADCAST`：使用广播方式传送。
- `SO_SNDBUF`：设置送出的暂存区大小。
- `SO_RCVBUF`：设置接收的暂存区大小。
- `SO_KEEPALIVE`：定期确定连线是否已终止。
- `SO_OOBINLINE`：当接收到`OOB`数据时会马上送至标准输入设备。
- `SO_LINGER`：确保数据安全且可靠的传送出去。

`optval`代表欲设置的值，`optlen`则为`optval`的长度。函数执行成功则返回`0`，若有错误则返回`-1`，错误原因存于`errno`，如下说明：

- `EBADF`：参数`s`并非合法的`socket`处理代码。
- `ENOTSOCK`：参数`s`为一文件描述词，非`socket`。
- `ENOPROTOOPT`：参数`optname`指定的选项不正确。
- `EFAULT`：参数`optval`指针指向无法存取的内存空间。