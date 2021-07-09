---
title: socket模块
categories: Python语法
date: 2019-02-09 18:47:09
---
### socket方法

&emsp;&emsp;`socket`函数使用给定的地址族、套接字类型、协议编号来创建套接字：<!--more-->

``` python
socket(family, type[, protocal])
```

`type`有以下选项：

socket类型              | 描述                         | socket类型           | 描述
------------------------|------------------------------|---------------------|-----
`socket.AF_UNIX`        | 只能用于`Unix`系统的进程间通信 | `socket.AF_INET`     | 服务器之间的网络通信
`socket.AF_INET6`       | `IPv6`                       | `socket.SOCK_STREAM` | 流式`socket`(`for TCP`)
`socket.SOCK_DGRAM`     | 数据报式`socket`(`for UDP`)   | `socket.SOCK_RAW`    | 原始套接字
`socket.SOCK_SEQPACKET` | 可靠的连续数据包服务

&emsp;&emsp;创建`TCP Socket`：

``` python
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

&emsp;&emsp;创建`UDP Socket`：

``` python
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
```

### socket函数

&emsp;&emsp;注意以下几点：

- `TCP`发送数据前，已建立好`TCP`连接，因此不需要指定目的地址。
- `UDP`是面向无连接的，每次发送都要指定目的地址。
- 服务端与客户端不能直接发送列表、元组、字典，需要将其字符串化。

&emsp;&emsp;服务端`socket`函数如下：

- `s.bind(address)`：将套接字绑定到地址，在`AF_INET`下，以元组`(host, port)`的形式表示地址。
- `s.listen(backlog)`：开始监听`TCP`传入连接。`backlog`指定在拒绝连接之前，操作系统可以监听的最大连接数量。
- `s.accept()`：接受`TCP`连接，并返回`(conn, address)`：

1. `conn`是新的套接字对象，可以用来接收和发送数据。
2. `address`是连接客户端的地址。

&emsp;&emsp;客户端`socket`函数如下：

- `s.connect(address)`：连接到`address`的套接字。`address`的格式一般为元组`(hostname, port)`，如果连接出错，则返回`socket.error`。

&emsp;&emsp;公共`socket`函数如下：

- `s.recv(bufsize[, flag])`：接受`TCP`套接字的数据，数据以字符串形式返回。

1. `bufsize`指定要接收的最大数据量。
2. `flag`提供有关消息的其他信息，通常可以忽略。

- `s.send(string[, flag])`：发送`TCP`数据，返回值是要发送的字节数量，该数量可能小于`string`的字节大小。
- `s.sendall(string[, flag])`：完整发送`TCP`数据。将`string`中的数据发送到连接的套接字，但在返回之前会尝试发送所有数据。
- `s.recvfrom(bufsize[, flag])`：接受`UDP`套接字的数据。与`recv`类似，但返回值是`(data, address)`：

1. `data`是包含接收数据的字符串。
2. `address`是发送数据的套接字地址。

- `s.sendto(string[, flag], address)`：发送`UDP`数据。`address`是格式为`(ipaddr, port)`的元组，指定远程地址和端口。返回值是发送的字节数。
- `s.close()`：关闭套接字。
- `s.fileno()`：返回套接字的文件描述符。
- `s.setdefaulttimeout(timeout)`：设置`Socket`默认超时时间，以秒为单位。

### TCP服务器

&emsp;&emsp;`TCP`服务器如下：

``` python
import socket

# 创建socket对象
serversocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

host = socket.gethostname()  # 获取本地主机名
port = 9999

serversocket.bind((host, port))
serversocket.listen(5)

while True:
    clientsocket, addr = serversocket.accept()  # 建立客户端连接
    print("连接地址: %s" % str(addr))
    msg = '欢迎访问菜鸟教程！' + "\r\n"
    clientsocket.send(msg.encode('utf-8'))
    clientsocket.close()
```

### TCP客户端

&emsp;&emsp;`TCP`客户端如下：

``` python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
host = socket.gethostname()
port = 9999
s.connect((host, port))
msg = s.recv(1024)  # 接收小于1024字节的数据
s.close()
print(msg.decode('utf-8'))
```

&emsp;&emsp;捕捉超时错误的代码如下：

``` python
import socket

socket.setdefaulttimeout(0.01)
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
    sock.connect(("123123231", 12345))
except socket.timeout as e:
    print("timeout")
```

### UDP服务器

&emsp;&emsp;`UDP`服务器如下：

``` python
from socket import *

host = "127.0.0.1"  # 服务器地址
port = 12345  # 服务器端口
bufsiz = 2048  # 缓存大小
adds = (host, port)  # 地址和端口

udpsersock = socket(AF_INET, SOCK_DGRAM)  # 创建UDP的套接字类型
udpsersock.bind(adds)  # 绑定到地址和端口

while True:
    msg = input('服务器说：')  # 输入数据
    data, addc = udpsersock.recvfrom(bufsiz)
    udpsersock.sendto(msg.encode('utf-8'), addc)

    if not data:
        break

    print('客户端回答：', data.decode('utf-8'))

udpsersock.close()
```

### UDP客户端

&emsp;&emsp;`UDP`客户端如下：

``` python
from socket import *

host = 'localhost'  # 本地服务器地址
port = 12345  # 客户端端口(确保和服务器的端口一致)
bufsiz = 2048  # 缓存大小
addc = (host, port)  # 地址和端口

udpclisock = socket(AF_INET, SOCK_DGRAM)  # 创建UDP的套接字类型

while True:
    msg = input('客户端说：')  # 输入数据
    udpclisock.sendto(msg.encode('utf-8'), addc)
    data, adds = udpclisock.recvfrom(bufsiz)

    if not data:
        break

    print('服务器回答：', data.decode('utf-8'))

udpclisock.close()
```

&emsp;&emsp;**补充说明**：`serversocket.bind((host, port))`的`host`参数最好用`IP`地址的字符串形式，例如`192.168.1.108`。