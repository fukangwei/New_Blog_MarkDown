---
title: cgic教程
date: 2019-01-18 11:34:34
categories: 嵌入式笔记
---
### 使用CGIC的基本思路

&emsp;&emsp;`C`语言编程是一项复杂且容易出错的工作，所以在完成复杂任务时，一定要选择合适的库，对于用`C`语言编写`CGI`程序则更是如此。`CGIC`是非常优秀的`C`语言`CGI`库函数，其下载地址为`www.boutell.com/cgic/#obtain`，现在的版本号是`2.05`。从上面提供的官方网址下载了`CGIC`库之后，解开压缩包，里面有大约`10`个文件，有用的如下：

文件         | 说明
-------------|----
`cgic.h`     | 头文件
`cgic.c`     | `CGIC`的源代码文件
`cgictest.c` | `CGIC`库的作者提供的一个`CGI`程序例子
`capture.c`  | 用于调试`CGI`程序的工具
`Makefile`   | 编译`CGIC`的脚本文件

&emsp;&emsp;可以看到，整个库实际上就是`cgic.c`一个文件，可以说是非常得精炼。我们可以把`CGIC`安装为操作系统的一个动态链接库，这样每次编译的时候，就不需要用到`cgic.c`。但是由于有特殊需求，我们将修改`cgic.c`代码，所以不把它安装进系统。每次编译的时候，只要把`cgic.c`和`cgic.h`放到当前文件夹就好了。
&emsp;&emsp;使用`cgic`自带`cgictest.c`来实现第一个`C`语言`CGI`程序。你可以新建一个工作目录，用于存放你的`CGI`程序源代码，把`cgic.h`、`cgic.c`和`cgictest.c`三个文件拷贝到这个目录，然后建立一个`Makefile`文件：

``` makefile
test.cgi: cgictest.c cgic.h cgic.c
    gcc -wall cgictest.c cgic.c -o test.cgi
```

保存好`Makefile`的内容之后，执行`make`命令。可以看到当前目录下应该多了一个`test.cgi`文件。
&emsp;&emsp;在你的网站根目录下建立一个`cgi-bin`目录(当然名字可以任意取，但作为习惯，一般叫做`cgi-bin`)，然后在`Apache`的配置文件里赋予其执行`CGI`代码的权限，权限修改完之后要重启`Apache`。完成之后，把刚才生成的`test.cgi`放到`cgi-bin`目录中。此时我们可以在浏览器中输入以下地址进行访问：

``` xml
http://127.0.0.1/cgi-bin/test.cgi
```

如果正常的话，应该看到一个网页被展示出来，这样第一个`C`语言的`CGI`程序就运行起来了。
&emsp;&emsp;从`cgic.c`的代码可以看出，它定义了`main`函数，而在`cgictest.c`中定义了一个`cgiMain`函数。也就是说，对于使用`CGIC`编写的`CGI`程序，都是从`cgic.c`中的代码进入，在库函数完成了一系列必要的操作(比如解析参数、获取系统环境变量)之后，它才会调用你的代码(从你定义的`cgiMain`进入)。另外一点就是，`cgi`程序输出`HTML`页面的方式都是使用`printf`把页面一行一行地打印出来，比如`cgictest.c`中的这一段代码：

``` c
fprintf ( cgiOut, "<textarea NAME="address" ROWS=4 COLS=40>\n" );
fprintf ( cgiOut, "Default contents go here. \n" );
fprintf ( cgiOut, "</textarea>\n" );
```

上面这段代码的运行结果就是在页面上输出一个`textarea`。第一个参数`cgiOut`实际上就是`stdin`，所以我们可以直接使用`printf`，而不必使用`fprintf`。不过在调试的时候会用到`fprintf`来重定向输出。这种方式与`Java Servlet`非常类似，`Servlet`也是通过调用打印语句`System.out.println(...)`来输出一个页面，不过后来`Java`推出了`JSP`来克服这种不便。但是与`Servlet`不同的地方在于，使用`C`语言的我们还要自己输出`HTML`头部(声明文档类型)：

``` c
cgiHeaderContentType ( "text/html" );
```

这个语句的调用一定要在所有`printf`语句之前。而这个语句执行的任务实际上就是：

``` c
void cgiHeaderContentType ( char *mimeType ) {
    fprintf ( cgiOut, "Content-type: %s\r\n\r\n", mimeType );
}
```

这个语句告诉浏览器，这次传来的数据的类型。如果是个`HTML`文档，就通过浏览器窗口显示；如果是一个`bin`(二进制)文件，则打开下载窗口，让用户选择是否保存文件以及保存文件的路径。理解了这几点之后，你就可以编写自己的`CGIC`程序了：

``` c
#include <stdio.h>
#include "cgic.h"
#include <string.h>
#include <stdlib.h>
​
int cgiMain ( void ) {
    cgiHeaderContentType ( "text/html" );
    fprintf ( cgiOut, "<HTML><HEAD>\n" );
    fprintf ( cgiOut, "<TITLE>My First CGI</TITLE></HEAD>\n" );
    fprintf ( cgiOut, "<BODY><H1>Hello CGIC</H1></BODY>\n" );
    fprintf ( cgiOut, "</HTML>\n" );
    return 0;
}
```

把`Makefile`文件中的`cgitest.c`全部换成`test.c`，再执行`make`命令即可。此时通过浏览器访问，会在页面上看到一个大大的`Hello CGIC`。

### 获取Get请求字符串

&emsp;&emsp;`Get`请求就是我们在浏览器地址栏输入`URL`时发送请求的方式，或者我们在`HTML`中定义一个表单`form`时，把`action`属性设为`Get`时的工作方式。`Get`请求字符串就是跟在`URL`后面以问号`?`开始的字符串，但不包括问号。比如这样的一个请求：

``` xml
http://127.0.0.1/cgi-bin/out.cgi?ThisIsTheGetString
```

在上面这个`URL`中，`ThisIsTheGetString`就是`Get`请求字符串。
&emsp;&emsp;在进入我们自己编写的`cgi`代码之前，`CGIC`库已经事先把这个字符串取到了，我们可以在程序中直接获得，要做的仅仅是在你编写的`cgiMain`方法前面加入以下声明：

``` c
extern char *cgiQueryString;
```

现在给出一个简单的例子，这个例子跟上一篇的测试程序非常相似，只不过程序的输出是使用者输入的`Get`请求字符串：

``` c
#include <stdio.h>
#include "cgic.h"
#include <string.h>
#include <stdlib.h>
​
extern char *cgiQueryString;
​
int cgiMain() {
    cgiHeaderContentType ( "text/html" );
    fprintf ( cgiOut, "<HTML><HEAD>\n" );
    fprintf ( cgiOut, "<TITLE>My CGIC</TITLE></HEAD>\n" );
    fprintf ( cgiOut, "<BODY>" );
    fprintf ( cgiOut, "<H1>%s</H1>", cgiQueryString );
    fprintf ( cgiOut, "</BODY>\n" );
    fprintf ( cgiOut, "</HTML>\n" );
    return 0;
}
```

假设把这个程序编译成`out.cgi`，并部署到`Web`服务器的`cgi-bin`目录下，当用户在浏览器地址栏输入本文开头给出的`URL`字符串时，浏览器页面上会显示`ThisIsTheGetString`。
&emsp;&emsp;我们也可以编写一个用于测试的`HTML`页面：

``` xml
<html>
    <head>
        <title>Test</title>
    </head>
    <body>
        <form action="cgi-bin/test.cgi" method="get">
            <input type="text" name="theText">
            <input type="submit" value="Continue &rarr;">
        </form>
    </body>
</html>
```

通过浏览器访问`http://127.0.0.1/test.html`，在文本框内输入一些字符，并点击提交按钮，然后就可以看到`cgi`把在文本框输入的字符原样显示在浏览器上。

### 反转义

&emsp;&emsp;浏览器在发送`Get`请求时，会把请求字符串进行转义操作(英文术语为`escape`)。比如，我们在地址栏输入(注意最后`it's me`中的空格)：

``` xml
http://localhost/~Jack/cgi-bin/out.cgi?it's me
```

浏览器会把它转义为：

``` xml
http://localhost/~Jack/cgi-bin/out.cgi?it%27s%20me
```

在上一篇最后给出的例子中，如果在文本框内输入`it's me`。你会发现，浏览器最终发送的请求为：

``` xml
http://localhost/~Jack/cgi-bin/out.cgi?theText=it%27s+me
```

通过`CGIC`，可以把这些被转义后的字符还原为我们本来的输入，这个过程就叫`反转义`(`Unescape`)。整个过程分三个步骤：
&emsp;&emsp;1. 打开`cgic.c`，找到这一行语句：

``` c
static cgiUnescapeResultType cgiUnescapeChars ( char **sp, char *cp, int len );
```

&emsp;&emsp;2. 在这个函数声明语句的上方，你会看到一个结构体定义：

``` c
typedef enum {
    cgiUnescapeSuccess,
    cgiUnescapeMemory
} cgiUnescapeResultType;
```

把这几行语句复制到`cgic.h`文件中，并在这里把它注释掉。同时还要删除在第一步中找到的函数声明语句中的`static`关键字。
&emsp;&emsp;3. 我们现在就可以使用反转义函数`cgiUnescapeChars`了。在你自己的代码中，加入以下声明语句：

``` c
extern cgiUnescapeResultType cgiUnescapeChars ( char **sp, char *cp, int len );
```

接下来给出一段完整的`test.c`代码：

``` c
#include <stdio.h>
#include "cgic.h"
#include <string.h>
#include <stdlib.h>
​
extern char *cgiQueryString;
extern cgiUnescapeResultType cgiUnescapeChars ( char **sp, char *cp, int len );
​
int cgiMain() {
    char *buffer;
    cgiHeaderContentType ( "text/html" );
    fprintf ( cgiOut, "<HTML><HEAD>\n" );
    fprintf ( cgiOut, "<TITLE>My CGI</TITLE></HEAD>\n" );
    fprintf ( cgiOut, "<BODY>" );
    cgiUnescapeChars ( &buffer, cgiQueryString, strlen ( cgiQueryString ) );
    fprintf ( cgiOut, "<H1>%s</H1>", buffer );
    fprintf ( cgiOut, "</BODY>\n" );
    fprintf ( cgiOut, "</HTML>\n" );
    free ( buffer );
    return 0;
}
```

值得注意的是，`buffer`的存储空间是`cgiUnescapeChars`帮你分配的，但最后要由你自己来释放，这一点千万不可忘记。下面你可以结合上一篇给出的测试用`html`代码试试该`cgi`程序的运行结果，也可以直接在浏览器地址栏输入一些带有特殊符号的字符串。
&emsp;&emsp;最后讲一下为什么不得不用这种方式来完成该任务，而`CGIC`不显式提供。`CGIC`的出发点是，我们平时只需要解析请求中的键值对，比如`?q=nice&client=IE`，当我们在服务端查询`q`的值时，就能得到`nice`。`CGIC`有一族函数帮助我们完成这个任务，比如`cgiFormString`。在解析这种请求格式的时候，如果我们提供的参数值含有被转义的字符，那么`CGIC`就会在内部调用`cgiUnescapeChars`完成反转义。但是，有时候我们会发送非常复杂的`Get`请求字符串，但并不是`键值对`的格式。这就需要直接使用`cgiUnescapeChars`进行反转义了。例如假设我们有个服务端`cgi`程序`chat.cgi`，这是个网络聊天机器人，如果我们发送如下请求：

``` xml
http://127.0.0.1/cgi-bin/chat.cgi? "this is a cgi user"
```

那么`chat.cgi`就会把`this is a cgi user`当做你对它说的话，经过处理，它会回复一段语句。为了方便，我们并没有写成`键值对`的形式。这个时候，被我们修改过的的`cgiUnescapeChars`就能派上用场了。

### 获取请求中的参数值

&emsp;&emsp;我们在提交一个表单`form`时，怎样把表单内的值提取出来呢？比如下面这个表单：

``` xml
<form action="cgi-bin/out.cgi" method="POST">
    <input type="text" name="name" />
    <input type="text" name="number" />
    <input type="submit" value="Submit" />
</form>
```

当`out.cgi`收到请求时，需要把输入框`name`和输入框`number`内的值提取出来，而且不管`form`中的`action`是`GET`还是`POST`，都要有效。下面给出示例代码：

``` c
#include <stdio.h>
#include "cgic.h"
#include <string.h>
#include <stdlib.h>
​
int cgiMain() {
    char name[241];
    char number[241];
    cgiHeaderContentType ( "text/html" );
    fprintf ( cgiOut, "<HTML><HEAD>\n" );
    fprintf ( cgiOut, "<TITLE>My CGI</TITLE></HEAD>\n" );
    fprintf ( cgiOut, "<BODY>" );
    cgiFormString ( "name", name, 241 );
    cgiFormString ( "number", number, 241 );
    fprintf ( cgiOut, "<H1>%s</H1>", name );
    fprintf ( cgiOut, "<H1>%s</H1>", number );
    fprintf ( cgiOut, "</BODY>\n" );
    fprintf ( cgiOut, "</HTML>\n" );
    return 0;
}
```

从上面的代码可以看出，第`13`行和第`14`行获取了输入框的值。获取输入参数值在`CGIC`中其实有一族函数，`cgiFormString`是其中最常用的一个。`cgiFormStringNoNewlines`用来去掉换行符(如果用户是在一个`TextArea`里输入字符的话)；`cgiFormStringSpaceNeeded`用于测试输入值的长度，可以以此为依据，然后按需精确分配缓冲区。

### CGI实现文件上传

&emsp;&emsp;不少网站都有文件上传的功能，本文展示如何用`CGIC`库编写文件上传的服务端程序，最后给出一段简单的`HTML`代码，供大家测试使用。`cgi`脚本代码如下所示：

``` c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/stat.h>
#include "cgic.h"
​
#define BufferLen 1024
​
int cgiMain ( void ) {
    cgiFilePtr file;
    int targetFile;
    mode_t mode;
    char name[128];
    char fileNameOnServer[64];
    char contentType[1024];
    char buffer[BufferLen];
    char *tmpStr = NULL;
    int size;
    int got, t;
    // cgiHeaderContentType ( "text/html" );
    printf ( "%s%c%c ", "Content-Type:text/html;charset=utf-8\n\n", 13, 10 );
​
    /* 取得html页面中file元素的值，应该是文件在客户机上的路径名 */
    if ( cgiFormFileName ( "file", name, sizeof ( name ) ) != cgiFormSuccess ) {
        fprintf ( stderr, "could not retrieve filename/n" );
        goto FAIL;
    }
​
    cgiFormFileSize ( "file", &size );
    /* 取得文件类型，不过本例中并未使用 */
    cgiFormFileContentType ( "file", contentType, sizeof ( contentType ) );
​
    /* 目前文件存在于系统临时文件夹中，通常为/tmp，通过该命令打开临时文件。
       临时文件的名字与用户文件的名字不同，所以不能通过路径/tmp/userfilename的方式获得文件 */
    if ( cgiFormFileOpen ( "file", &file ) != cgiFormSuccess ) {
        fprintf ( stderr, "could not open the file/n" );
        goto FAIL;
    }
​
    t = -1;
​
    while ( 1 ) { /* 从路径名解析出用户文件名 */
        tmpStr = strstr ( name + t + 1, "//" );
​
        if ( NULL == tmpStr ) {
            /* if "//" is not path separator, try "/" */
            tmpStr = strstr ( name + t + 1, "/" );
        }
​
        if ( NULL != tmpStr ) {
            t = ( int ) ( tmpStr - name );
        } else {
            break;
        }
    }
​
    strcpy ( fileNameOnServer, name + t + 1 );
    mode = S_IRWXU | S_IRGRP | S_IROTH;
    /* 在当前目录下建立新的文件，第一个参数实际上是路径名，
       此处的含义是在cgi程序所在的目录(当前目录)建立新文件 */
    targetFile = open ( fileNameOnServer, O_RDWR | O_CREAT | O_TRUNC | O_APPEND, mode );
​
    if ( targetFile < 0 ) {
        fprintf ( stderr, "could not create the new file,%s/n", fileNameOnServer );
        goto FAIL;
    }
​
    /* 从系统临时文件中读出文件内容，并放到刚创建的目标文件中 */
    while ( cgiFormFileRead ( file, buffer, BufferLen, &got ) == cgiFormSuccess ) {
        if ( got > 0 ) {
            write ( targetFile, buffer, got );
        }
    }
​
    cgiFormFileClose ( file );
    close ( targetFile );
    goto END;
FAIL:
    fprintf ( stderr, "Failed to upload" );
    return 1;
END:
    printf ( "File \"%s\" has been uploaded", fileNameOnServer );
    return 0;
}
```

假设该文件存储为`upload.c`，则使用如下命令编译：

``` bash
gcc -Wall upload.c cgic.c -o upload.cgi
```

编译完成后，把`upload.cgi`复制到你部署`cgi`程序的目录。正式部署时，请务必修改用`open`创建新文件那一行代码，把`open`的第一个参数设置为目标文件在服务器上存储的绝对路径，或者相对于`cgi`程序的相对路径。本例中，出于简单考虑，在`cgi`程序所在目录下创建新文件。测试用`HTML`代码如下：

``` xml
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>Test Upload</title>
        <meta name="author" content="Jack">
    </head>
    <body>
        <form action="cgi-bin/test.cgi" method="post"
              enctype="multipart/form-data" target="_blank">
            <input type="file" name="file" value="" />
            <input type="submit" name="submit" value="OK">
        </form>
    </body>
</html>
```

当然，你必须配置`cgi`脚本程序有权限在`cgi-bin`目录下创建文件，因为此例把文件上传到`cgi-bin`目录下。
&emsp;&emsp;那么如何控制上传文件的大小呢？因为你有时会不允许用户上传太大的文件。通过分析`cgic.c`的源代码，我们发现它定义了一个变量`cgiContentLength`，表示请求的长度。但我们需要首先判断这是一个上传文件的请求，然后才能根据`cgiContentLength`来检查用户是否要上传一个太大的文件。`cgic.c`的`main`函数中进行了一系列`if-else`判断来检查请求的类型，首先确定这是一个`post`请求，然后确定数据的编码方式为`multipart/form-data`。这个判断通过之后，就要开始准备接收数据了。所以我们要在接收数据开始之前使用`cgiContentLength`判断大小，如果超过标准，就立即返回，不允许继续操作。下面贴出修改后代码片段：

``` c
else if ( cgiStrEqNc ( cgiContentType, "multipart/form-data" ) ) {
#ifdef CGICDEBUG
    CGICDEBUGSTART
    fprintf ( dout, "Calling PostMultipartInput\n" );
    CGICDEBUGEND
#endif
    /* UpSize为文件长度上限值，以byte为单位。UpSize是
       一个int变量，因为cgiContentLength的类型为int */
    if ( cgiContentLength > UpSize ) {
        cgiHeaderContentType ( "text/html" );
        printf ( "File too large!\n" );
        cgiFreeResources();
        return -1;
    }
    if ( cgiParsePostMultipartInput() != cgiParseSuccess ) {
#ifdef CGICDEBUG
        CGICDEBUGSTART
        fprintf ( dout, "PostMultipartInput failed\n" );
        CGICDEBUGEND
#endif
        cgiFreeResources();
        return -1;
    }
#ifdef CGICDEBUG
    CGICDEBUGSTART
    fprintf ( dout, "PostMultipartInput succeeded\n" );
    CGICDEBUGEND
#endif
}
```

变量`UpSize`表示文件大小的上限。在`cgic.c`的`main`中找到相关代码，并修改成上面这样即可。你可以在`cgic.c`中定义`UpSize`，也可以在刚才完成的`upload.c`中定义，然后在`cgic.c`中用`extern`方式引用。
&emsp;&emsp;使用`BOA`和`CGIC`上传文件时只能传`1MB`大小左右，后来发现是`BOA`搞的鬼，方法有如下`2`种：

- 修改源代码的`defines.h`里面的宏`SINGLE_POST_LIMIT_DEFAULT`。
- 修改`boa.conf`里面的`SinglePostLimit`。

实践证明第一种是可以的，然后重新编译生成`boa`。

---

### cgic在ARM上移植步骤

&emsp;&emsp;从`CGIC`的主站点`http://www.boutell.com/cgic/`下载源码，当前最新版本是`2.05`版。将`cgic205.tar.gz`拷贝到`Linux`系统中，解压得到`cgic205`目录。进入该目录，修改`Makefile`文件的内容：

- `CC=gcc`改为`CC=arm-linux-gcc`。
- `AR=ar`改为`AR=arm-linux-ar`。
- `RANLIB=ranlib`改为`RANLIB=arm-linux-ranlib`。
- `gcc cgictest.o -o cgictest.cgi ${LIBS}`改为`$(CC) $(CFLAGS) cgictest.o -o cgictest.cgi ${LIBS}`。
- `gcc capture.o -o capture ${LIBS}`改为`$(CC) $(CFLAGS) capture.o -o capture ${LIBS}`。

&emsp;&emsp;执行`make`命令，得到的`CGIC`库`libcgic.a`，并生成调试辅助程序`capture`和测试程序`cgictest.cgi`文件。执行`arm-linux-strip capture`命令，对生成的`capture`文件进行优化，减小其大小，`capture`由原来的`100K`左右变成`29K`左右。
&emsp;&emsp;拷贝生成的`capture`和`cgictest.cgi`到根文件系统的`www/cgi-bin`目录下，然后将开发板复位，运行`boa`服务器。在浏览器输入`http://IP地址/cgi-bin/cgitest.cgi`，如果打开了`cgi`测试网页，表示`CGIC`库和测试脚本都移植成功。