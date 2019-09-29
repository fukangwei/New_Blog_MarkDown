---
title: URL格式
categories: HTML笔记
abbrlink: 37a40044
date: 2019-03-07 19:19:42
---
&emsp;&emsp;在`WWW`上，每一个信息资源都有在网上唯一的地址，该地址就叫`URL`(`Uniform Resource Locator`，统一资源定位符)。`URL`由三部分组成：资源类型、存放资源的主机域名、资源文件名。其一般语法格式为：

``` xml
protocol://hostname[:port]/path/[;parameters][?query]#fragment (带方括号的为可选项)
```

例如：

``` xml
http://www.imailtone.com:80/WebApplication1/WebForm1.aspx?name=tom&;age=20#resume
```

格式说明如下：

- `protocol`：指定使用的传输协议，最常用的是`HTTP`协议，它也是目前`WWW`应用最广的协议。

1. `file`：资源是本地计算机上的文件，格式为`file://`。
2. `ftp`：通过`FTP`访问资源，格式为`FTP://`。
3. `gopher`：通过Gopher协议访问该资源。
4. `http`：通过`HTTP`访问该资源，格式为`HTTP://`。
5. `https`：通过`HTTPS`访问该资源，格式为`HTTPS://`。
6. `mailto`：资源为电子邮件地址，通过`SMTP`访问，格式为`mailto://`。
7. `MMS`：通过支持`MMS`(流媒体)协议的播放该资源，代表软件是`Windows Media Player`，格式为`MMS://`。
8. `ed2k`：通过支持`ed2k`(专用下载链接)协议的`P2P`软件访问该资源，代表软件是电驴，格式为`ed2k://`。
9. `news`：通过`NNTP`访问该资源。

- `hostname`：指存放资源的服务器的域名系统(`DNS`)主机名或`IP`地址。在主机名前也可以包含连接到服务器所需的用户名和密码，格式为`username@password`。
- `:port`：它是一个整数，省略时使用方案的默认端口，各种传输协议都有默认的端口号，如http的默认端口为80。有时候出于安全或其他考虑，可以在服务器上对端口进行重定义，即采用非标准端口号，此时URL中就不能省略端口号这一项。
- `path`：由零或多个`/`符号隔开的字符串，一般用来表示主机上的一个目录或文件地址。
- `;parameters`：这是用于指定特殊参数的可选项。
- `?query`：可选项，用于给动态网页(如使用`CGI`、`ISAPI`、`PHP/JSP/ASP/ASP.NET`等技术制作的网页)传递参数，可以有多个参数，用`&`符号隔开，每个参数的名和值用`=`符号隔开。
- `fragment`：用于指定网络资源中的片断。例如一个网页中有多个名词解释，可使用`fragment`直接定位到某一名词解释。