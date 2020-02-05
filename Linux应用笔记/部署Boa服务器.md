---
title: 部署Boa服务器
categories: Linux应用笔记
abbrlink: aeebde75
date: 2019-02-02 17:29:37
---
&emsp;&emsp;部署详细步骤如下：<!--more-->
&emsp;&emsp;1. 到`http://www.boa.org/`下载`boa-0.94.13.tar.gz`，解压后得到`boa-0.94.13`文件夹。
&emsp;&emsp;2. 进入其中的`src`文件夹，运行`./configure`生成`Makefile`文件。
&emsp;&emsp;3. 在`boa-0.94.13`文件夹的`src`目录下，做如下修改：

- 将`defines.h`中的`#define SERVER_ROOT "/etc/boa"`修改为`#define SERVER_ROOT "/boa"`(这样`boa`程序在启动时会在`/boa`目录下寻找`boa.conf`配置文件，并且将`/boa`文件夹作为服务器的根目录)。
- 将`boa.c`文件中的：

``` cpp
if ( setuid ( 0 ) != -1 ) {
    DIE ( "icky Linux kernel bug!" );
}
```

这三行注释掉，否则`boa`启动时会出现`boa.c:226 - icky Linux kernel bug!: No suchfile or directory`的错误。

- 将`compat.h`中的`#define TIMEZONE_OFFSET(foo) foo##->tm_gmtoff`改为`#define TIMEZONE_OFFSET(foo) (foo)->tm_gmtoff`。

&emsp;&emsp;4. 运行`make`命令。
&emsp;&emsp;5. 建立安装目录，使用如下命令：

``` bash
mkdir -p /boa /boa/www /boa/cgi-bin /boa/log
```

&emsp;&emsp;6. 将需要的文件复制到安装目录中：将`boa-0.94.13/src`目录下生成的`boa`、`boa_indexer`二进制文件复制到`/boa`下；将`boa-0.04.13`目录下的`boa.conf`文件复制到`/boa`下；将`/etc/mime.types`复制到`/boa`目录下。
&emsp;&emsp;7. 修改`boa.conf`配置文件，boa启动时会加载该文件。按照如下格式修改(下面的文件去除了官方注释)：

``` bash
Port 80
User 0
Group 0
​
# error_log和access_log会自动生成，只要指定生成路径就可以了
ErrorLog /boa/log/error_log
AccessLog /boa/log/access_log
​
# 存放HTML文件的根路径
DocumentRoot /boa/www
UserDir public_html

# 默认页面，若输入“http://127.0.0.1/”，则会自动返回给浏览器默认页面index.html
DirectoryIndex index.html

# 保持默认
DirectoryMaker /boa/boa_indexer
KeepAliveMax 1000
KeepAliveTimeout 10
MimeTypes /boa/mime.types
DefaultType text/plain

# 指定传给cgi程序的PATH环境变量
CGIPath /bin:/usr/bin:/usr/local/bin

# 保持默认
Alias /doc /usr/doc
​
# 如果输入“http://127.0.0.1/cgi-bin/test.cgi”，
# 则boa服务器会到“/boa/cgi-bin”中寻找test.cgi程序
ScriptAlias /cgi-bin/ /boa/cgi-bin/
```

&emsp;&emsp;8. 建立测试页面。

- 将`index.html`放在`/boa/www`目录下，代码如下：

``` xml
<html>
    <body>
        <h3>this is a test!</h3><br/>
        <img src="image.jpg"/>
        <h3>tree picture</h3><br/>
        <a href="/cgi-bin/test.cgi">to cgi page</a>
    </body>
</html>
```

- 以下是`test.c`的代码，使用`gcc -o test.cgi test.c`编译后得到的`test.cgi`放在`/boa/cgi-bin`目录下：

``` cpp
#include <stdio.h>
​
int main ( void ) {
    printf ( "Content-type:text/html\n\n" ); /* 这句一定要加上 */
    printf ( "<html><body>" );
    printf ( "<font style=\"color:red; font-size:30px;\">Hello, CGI!</font><br/>" );
    printf ( "<a href=\"/index.html\">return index.html</a>" );
    printf ( "</body></html>" );
    return 0;
}
```

&emsp;&emsp;9. 测试效果。进入`/boa`目录，使用命令`./boa`来运行`boa`服务器。在浏览器中输入`http://127.0.0.1/`便可访问到默认的页面`index.html`。点击`index.html`页面中的超链接便可访问到`cgi`测试页面，点击`test.cgi`中的超链接又可返回`index.html`页面。

&emsp;&emsp;**补充说明**：
&emsp;&emsp;1. 移植`boa`到嵌入式`Linux`上的方法和上面几乎一样，具体做法是在`./configure`生成`Makefile`后，将`Makefile`中的`CC = gcc`改为`CC = arm-linux-gcc`，将`CPP = gcc -E`改为`CPP = arm-linux-gcc -E`，然后`make`就可以了。
&emsp;&emsp;2. 移植`boa`到嵌入式`linux`后，在启动时若出现`gethostbyname:: Success`，然后程序退出，则需在原`boa.conf`文件中增加一行`ServerName www.your.org.here`。然后重新运行`boa`就可以了。
&emsp;&emsp;3. 关于`boa`服务器的`ipv6`移植步骤：在运行`./configure`后，会在其`src/`目录下产生一个`config.h`文件，在其中增加一个宏定义`#define INET6`。然后修改`compat.h`文件中的第`74`行：将`#define S_FAMILY __s_family`修改为`#define S_FAMILY ss_family`。最后将源代码重新编译即可。
&emsp;&emsp;4. 一定不要忘记将`/boa/log`目录的权限设置为可写，否则会出现`log.c:73 unable to dup2 the error log:bad file descriptor`的错误。
&emsp;&emsp;5. 如果出现了`boa.c:194 - unable to bind: Permission denied`的错误，说明`boa.conf`中设置的端口被占用了，需要设置为其他端口。
&emsp;&emsp;6. 使用`CGI`脚本显示图片：我的`boa.conf`将`DocumentRoot`设置为`/home/httpd/html`，然后在`/home/httpd/html/images/`下放置图片，`cgi`脚本中的`src`设置为`/images/图片名称`，注意`images`前有个`/`。