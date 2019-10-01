---
title: CGI编程
abbrlink: 5990a09
date: 2019-01-17 10:13:37
categories: 网络编程
---
### 为什么要进行CGI编程？

&emsp;&emsp;在`HTML`中，当客户填写了表单，并按下了发送`submit`按钮后，表单的内容被发送到了服务器端，这时就需要有一个服务器端脚本来对表单的内容进行一些处理。没有了`CGI`，`WEB`的世界就完全失去了交互性，所有的信息都变成单向的了，而不能够有任何的反馈。
&emsp;&emsp;有的人认为可以用`JavaScript`来代替`CGI`程序，这其实是一个概念上的错误。`JavaScript`只能够在客户浏览器中运行，而`CGI`却是工作在服务器上的。它们所做的工作有一些交集，比如表单数据验证一类的，但是`JavaScript`是绝对无法取代`CGI`的。但可以这样说，如果一项工作即能够用`JavaScript`来做，又可以用`CGI`来做，那么绝对要使用`JavaScript`，在执行的速度上，`JavaScript`比`CGI`有着先天的优势。只有那些在客户端解决不了的问题，比如和某个远程数据库交互，这时就应该使用`CGI`。
&emsp;&emsp;简单的说来，`CGI`是用来沟通`HTML`表单和服务器端程序的接口`interface`。说它是接口，也就是说`CGI`并不是一种语言，而是可以被其他语言所应用的一个规范集。理论上讲，你可以用任何的程序语言来编写`CGI`程序，只要在编程的时候符合`CGI`规范所定义的一些东西就可以了。由于`C`语言在平台无关性上表现不错，而且对大多数程序员而言都算得上很熟悉，因此`C`语言是`CGI`编程的首选语言之一。

### GET表单的处理

&emsp;&emsp;对于那些使用了属性`METHOD=GET`的表单(或者没有`METHOD`属性，这时候`GET`是其缺省值)，`CGI`的定义是：当表单被发送到服务器端后，表单中的数据被保存在服务器上一个叫做`QUERY_STRING`的环境变量中。这种表单的处理相对简单，只要读取环境变量就可以了，这一点对不同的语言有不同的做法。在`C`语言中，你可以用库函数`getenv`(定义在标准库函数`stdlib`)来把环境变量的值作为一个字符串来存取。你可以在取得了字符串中的数据后，运用一些小技巧进行类型的转换，这都是比较简单的了。`CGI`程序中的标准输出(比如在`C`语言中的`stdout`)也是经过重定义的，它并没有在服务器上产生任何的输出内容，而是被重定向到客户浏览器。这样如果编写一个`C`语言的`CGI`程序，把一个`HTML`文档输出到它的`stdout`，这个`HTML`文档会被在客户端的浏览器中显示出来，这也是`CGI`程序的一个基本原理。
&emsp;&emsp;下面是一段`HTML`表单，位于`index.html`：

``` xml
<form action="/cgi-bin/hello.cgi">
    <p> 请在下面填入乘数和被乘数，按下确定后可以看到结果 </p>
    <input name="m" size="5">
    <input name="n" size="5"><br>
    <input type="submit" value="确定">
</form>
```

我们要实现的功能很简单，就是把表单中输入的数值乘起来。下面就是处理这个表单的`CGI`程序，对应于`FORM`标签中的`ACTION`属性值：

``` cpp
#include <stdio.h>
#include <stdlib.h>
​
int main ( void ) {
    char *data;
    long m, n;
    printf ( "%s%c%c ", "Content-Type:text/html;charset=utf-8\n\n", 13, 10 );
    printf ( "<html>\n" );
    printf ( "<head>\n" );
    printf ( "<tile>乘法结果</tile>\n" );
    printf ( "</head>\n" );
    printf ( "<body>\n" );
    printf ( "<h3>乘法结果</h3>\n" );
    data = getenv ( "QUERY_STRING" );
​
    if ( data == NULL ) {
        printf ( "<p>错误！数据没有被输入或者数据传输有问题\n" );
    } else if ( sscanf ( data, "m=%ld&n=%ld", &m, &n ) != 2 ) {
        printf ( "<p>错误！输入数据非法。表单中输入的必须是数字。\n" );
    } else {
        printf ( "<p>%ld和%ld的成绩是：%ld.\n", m, n, m * n );
    }
​
    printf ( "</body>\n" );
    printf ( "</html>\n" );
    return 0;
}
```

前面已经提到标准输出的内容就是要被显示在浏览器中的内容。第一行的输出内容是必须的，也是一个`CGI`程序所特有的：

``` cpp
printf( "%s%c%c ", "Content-Type:text/html;charset=utf-8\n\n", 13, 10 );
```

这个输出是作为`HTML`的文件头。因为`CGI`不仅可以像浏览器输出`HTML`文本，而且可以输出图像、声音之类的东西。这一行告诉浏览器如何处理接收到的内容。在`Content-Type`的定义后面跟有两行的空行，这也是不可缺少的。程序在后面调用了用了库函数`getevn`来得到`QUERY_STRING`的内容，然后使用`sscanf`函数把每个参数值取出来。
&emsp;&emsp;将程序编译后，改名为`mult.cgi`，并放在`/cgi-bin`目录下面，就可以被表单调用了。

---

### CGI概述

&emsp;&emsp;`CGI`(`Common Gateway Interface`，公用网关接口)规定了`Web`服务器调用其他可执行程序(`CGI`程序)的接口协议标准。`Web`服务器通过调用`CGI`程序实现和`Web`浏览器的交互，也就是`CGI`程序接受`Web`浏览器发送给`Web`服务器的信息，进行处理，将响应结果再回送给`Web`服务器及`Web`浏览器。`CGI`程序一般完成`Web`网页中表单(`Form`)数据的处理、数据库查询和实现与传统应用系统的集成等工作。`CGI`程序可以用任何程序设计语言编写，如`Shell`、`Perl`、`Fortran`、`Pascal`、`C`语言等。但是用`C`语言编写的`CGI`程序具有执行速度快、安全性高(因为`C`语言程序是编译执行，且不可被修改)等特点。
&emsp;&emsp;`CGI`接口标准包括标准输入、环境变量、标准输出三部分。
&emsp;&emsp;`标准输入`：`CGI`程序像其他可执行程序一样，可通过标准输入从`Web`服务器得到输入信息，如`Form`中的数据，这就是所谓的向`CGI`程序传递数据的`POST`方法。这意味着在操作系统命令行状态可执行`CGI`程序，对`CGI`程序进行调试。`POST`方法是常用的方法，本文将以此方法为例，分析`CGI`程序设计的方法、过程和技巧。
&emsp;&emsp;`环境变量`：操作系统提供了许多环境变量，它们定义了程序的执行环境，应用程序可以存取它们。`Web`服务器和`CGI`接口又另外设置了自己的一些环境变量，用来向`CGI`程序传递一些重要的参数。`CGI`的`GET`方法还通过环境变量`QUERY_STRING`向`CGI`程序传递`Form`中的数据。
&emsp;&emsp;`标准输出`：`CGI`程序通过标准输出将输出信息传送给`Web`服务器。传送给`Web`服务器的信息可以用各种格式，通常是以纯文本或者`HTML`文本的形式，这样我们就可以在命令行状态调试`CGI`程序，并且得到它们的输出。
&emsp;&emsp;下面是一个简单的`CGI`程序，它将`HTML`中`Form`的信息直接输出到`Web`浏览器：

``` cpp
#include <stdio.h>
#include <stdib.h>
​
int main ( void ) {
    int i, n;
    printf ( "Content-type:text/plain\n\n" );
    n = 0;
​
    if ( getenv ( "CONTENT_LENGTH" ) ) {
        n = atoi ( getenv ( "CONTENT_LENGTH" ) );
    }
​
    for ( i = 0; i < n; i++ ) {
        putchar ( getchar() );
    }
​
    putchar ( '\n' );
    fflush ( stdout );
}
```

&emsp;&emsp;下面对此程序作一下简要的分析：

``` cpp
printf ( "Content-type:text/plain\n\n" );
```

此行通过标准输出将字符串`Content-type:text/plain\n\n`传送给`Web`服务器。它是一个`MIME`头信息，告诉`Web`服务器随后的输出是以纯`ASCII`文本的形式。请注意在这个头信息中有两个新行符，这是因为`Web`服务器需要在实际的文本信息开始之前先看见一个空行。

``` cpp
if ( getenv ( "CONTENT_LENGTH" ) ) {
    n = atoi ( getenv ( "CONTENT_LENGTH" ) );
}
```

此行首先检查环境变量`CONTENT_LENGTH`是否存在。`Web`服务器在调用使用`POST`方法的`CGI`程序时设置此环境变量，它的文本值表示Web服务器传送给`CGI`程序的输入中的字符数目，因此我们使用函数`atoi`将此环境变量的值转换成整数，并赋给变量`n`。请注意`Web`服务器并不以文件结束符来终止它的输出，所以如果不检查环境变量`CONTENT_LENGTH`，`CGI`程序就无法知道什么时候输入结束了。

``` cpp
for ( i = 0; i < n; i++ ) {
    putchar ( getchar() );
}
```

此行从`0`循环到`CONTENT_LENGTH - 1`次将标准输入中读到的每一个字符直接拷贝到标准输出，也就是将所有的输入以`ASCII`的形式回送给`Web`服务器。
&emsp;&emsp;通过此例，我们可将`CGI`程序的一般工作过程总结为如下几点：
&emsp;&emsp;1. 通过检查环境变量`CONTENT_LENGTH`，确定有多少输入。
&emsp;&emsp;2. 循环使用`getchar`或者其他文件读函数得到所有的输入。
&emsp;&emsp;3. 以相应的方法处理输入。
&emsp;&emsp;4. 通过`Content-type`头信息，将输出信息的格式告诉`Web`服务器。
&emsp;&emsp;5. 通过使用`printf`或者`putchar`或者其他的文件写函数，将输出传送给`Web`服务器。
&emsp;&emsp;总之，`CGI`程序的主要任务就是从`Web`服务器得到输入信息，进行处理，然后将输出结果再送回给`Web`服务器。

### 环境变量

&emsp;&emsp;环境变量是文本串(`名字/值对`)，可以被`Shell`或其他程序设置，也可以被其他程序访问。它们是`Web`服务器传递数据给`CGI`程序的简单手段，之所以称为环境变量是因为它们是全局变量，任何程序都可以存取它们。下面是`CGI`程序设计中常常要用到的一些环境变量：

环境变量             | 说明
--------------------|-----
`SERVER_NAME`       | `CGI`脚本运行时的主机名和`IP`地址
`SERVER_SOFTWARE`   | 你的服务器的类型，例如`CERN/3.0`或`NCSA/1.3`
`GATEWAY_INTERFACE` | 运行的`CGI`版本，对于`UNIX`服务器，这是`CGI/1.1`
`SERVER_PROTOCOL`   | 服务器运行的`HTTP`协议，这里是`HTTP/1.0`
`SERVER_PORT`       | 服务器运行的端口，通常`Web`服务器是`80`
`REQUEST_METHOD`    | `POST`或者`GET`，取决于你的表单是怎样递交的
`HTTP_ACCEPT`       | 浏览器能直接接收的`Content-types`，可以由`HTTP Accept header`定义
`HTTP_USER_AGENT`   | 递交表单的浏览器的名称、版本和其他平台性的附加信息
`HTTP_REFERER`      | 递交表单的文本的`URL`，不是所有的浏览器都发出这个信息，不要依赖它
`PATH_INFO`         | 附加的路径信息，由浏览器通过`GET`方法发出
`PATH_TRANSLATED`   | 在`PATH_INFO`中系统规定的路径信息
`SCRIPT_NAME`       | 指向这个`CGI`脚本的路径，是在`URL`中显示的，例如`/cgi-bin/thescript`
`QUERY_STRING`      | 脚本参数或者表单输入项(如果是用`GET`递交)，`QUERY_STRING`包含`URL`中问号后面的参数
`REMOTE_HOST`       | 递交脚本的主机名，这个值不能被设置
`REMOTE_ADDR`       | 递交脚本的主机`IP`地址
`REMOTE_USER`       | 递交脚本的用户名，如果服务器的`authentication`被激活，这个值可以设置
`REMOTE_IDENT`      | 如果`Web`服务器是在`ident`(一种确认用户连接你的协议)运行，递交表单的系统也在运行`ident`，这个变量就含有`ident`返回值
`CONTENT_TYPE`      | 如果表单是用`POST`递交，这个值将是`application/x-www-form-urlencoded`
`CONTENT_LENGTH`    | 对于用`POST`递交的表单，标准输入口的字节数

&emsp;&emsp;在`C`语言中，要访问环境变量可使用`getenv`库函数：

``` cpp
if ( getenv ( "CONTENT_LENGTH" ) ) {
    n = atoi ( getenv ( "CONTENT_LENGTH" ) );
}
```

程序中最好调用两次`getenv`，第一次检查是否存在该环境变量，第二次再使用该环境变量。这是因为函数`getenv`在给定的环境变量名不存在时，返回一个`NULL`指针，如果你不首先检查而直接引用它，当该环境变量不存在时会引起`CGI`程序崩溃。

### From输入的分析和解码

&emsp;&emsp;当用户提交一个`HTML`的`Form`时，`Web`浏览器首先对`Form`中的数据以`名字/值对`的形式进行编码，并发送给`Web`服务器，然后由`Web`服务器传递给`CGI`程序。其格式如下：

``` python
name1=value1&name2=value2&name3=value3&name4=value4&...
```

其中名字是`Form`中定义的`INPUT`、`SELECT`或`TEXTAREA`等标置(`Tag`)名字，值是用户输入或选择的标置值。这种格式即为`URL`编码，程序中需要对其进行分析和解码。要分析这种数据流，`CGI`程序必须首先将数据流分解成一组组的名字/值对。这可以通过在输入流中查找下面的两个字符来完成。每当找到字符`=`，标志着一个`Form`变量名字的结束；每当找到字符`&`，标志着一个`Form`变量值的结束。请注意，输入数据的最后一个变量的值不以`&`结束。
&emsp;&emsp;一旦`名字/值对`分解后，还必须将输入中的一些特殊字符转换成相应的`ASCII`字符。这些特殊字符是：

- `+`：将`+`转换成空格符。
- `%xx`：用其十六进制`ASCII`码值表示的特殊字符，根据值`xx`将其转换成相应的`ASCII`字符。

对`Form`变量名和变量值都要进行这种转换。下面是一个对`Form`数据进行分析并将结果回送给`Web`服务器的`CGI`程序：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <strings.h>
　　
int htoi ( char * );
​
int main() {
    int i, n;
    char c;
    printf ( "Content-type:text/plain\n\n" );
    n = 0;
​
    if ( getenv ( "CONTENT_LENGTH" ) ) {
        n = atoi ( getenv ( "CONTENT_LENGTH" ) );
    }
​
    for ( i = 0; i < n; i++ ) {
        int is_eq = 0;
        c = getchar();
​
        switch ( c ) {
            case '&': c = '\n'; break;
            case '+': c = ' ';  break;​
            case '%': {
                    char s[3];
                    s[0] = getchar();
                    s[1] = getchar();
                    s[2] = 0;
                    c = htoi ( s );
                    i += 2;
                }
                break;
            case '=':
                c = ':';
                is_eq = 1;
                break;
        };
​
        putchar ( c );
​
        if ( is_eq ) {
            putchar ( ' ' );
        }　　
    }
​
    putchar ( '\n' );
    fflush ( stdout );
}
​
/* convert hex string to int */
int htoi ( char *s ) {
    char *digits = "0123456789ABCDEF";
​
    if ( islower ( s[0] ) ) {
        s[0] = toupper ( s[0] );
    }
​
    if ( islower ( s[1] ) ) {
        s[1] = toupper ( s[1] );
    }
​
    return 16 * ( strchr ( digits, s[0] ) - strchr ( digits, '0' ) ) + \
                ( strchr ( digits, s[1] ) - strchr ( digits, '0' ) );
}
```

上面的程序首先输出一个`MIME`头信息给`Web`服务器，检查输入中的字符数，并循环检查每一个字符。当发现字符为`&`时，意味着一个`名字/值对`的结束，程序输出一个空行；当发现字符为`+`时，将它转换成空格；当发现字符为`%`时，意味着一个两字符的十六进制值的开始，调用`htoi`函数将随后的两个字符转换为相应的`ASCII`字符；当发现字符为`=`时，意味着一个`名字/值对`的名字部分的结束，并将它转换成字符`:`。最后将转换后的字符输出给`Web`服务器。

### 产生HTML输出

&emsp;&emsp;`CGI`程序产生的输出由两部分组成：`MIME`头信息和实际的信息，两部分之间以一个空行分开。我们已经看到怎样使用`MIME`头信息`Content-type:text/plain\n\n`和`printf`、`putchar`等函数调用来输出纯`ASCII`文本给`Web`服务器。实际上，我们也可以使用`MIME`头信息`Content-type:text/html\n\n`来输出`HTML`源代码给`Web`服务器。请注意任何`MIME`头信息后必须有一个空行。一旦发送这个`MIME`头信息给`Web`服务器后，`Web`浏览器将认为随后的文本输出为`HTML`源代码，在`HTML`源代码中可以使用任何`HTML`结构，如超链、图像、`Form`以及对其他`CGI`程序的调用。也就是说，我们可以在`CGI`程序中动态产生`HTML`源代码输出。

``` cpp
#include <stdio.h>
#include <string.h>
​
int main() {
    printf ( "Contenttype:text/html\n\n" );
    printf ( "<html>\n" );
    printf ( "<head><title>An HTML Page From a CGI</title> </head>n" );
    printf ( "<body><br>\n" );
    printf ( "<h2> This is an HTML page generated from within a CGI program... </h2>\n" );
    printf ( "<hr><p>\n" );
    printf ( "<a href = \"../output.html#two\"><b> Go back to output.html page </b> </a>\n" );
    printf ( "</body>\n" );
    printf ( "</html>\n" );
    fflush ( stdout );
}
```

上面的`CGI`程序简单地用`printf`函数来产生`HTML`源代码。请注意在输出的字符串中如果有双引号，在其前面必须有一个后斜字符，这是因为整个`HTML`代码串已经在双引号内，所以`HTML`代码串中的双引号符必须用一个后斜字符来转义。