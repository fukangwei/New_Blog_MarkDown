---
title: Node.js基本知识
date: 2019-02-08 16:08:36
tags:
---
### Node.js基础

&emsp;&emsp;`Node.js`是一个事件驱动`I/O`服务端`JavaScript`环境，基于`Google`的`V8`引擎。可以使用以下命令来查看当前`Node.js`版本：

``` bash
$ node -v
v4.4.3
```

以下是第一个`Node.js`程序：

``` javascript
console.log("Hello World");
```

保存该文件，文件名为`helloworld.js`，并通过`node`命令来执行：

``` javascript
node helloworld.js
```

程序执行后，就会在终端输出`Hello World`。
&emsp;&emsp;也可以使用交互模式进行输入。打开终端，键入`node`进入命令交互模式：

``` bash
$ node
> console.log('Hello World!');
Hello World!
```

### Node.js创建第一个应用

&emsp;&emsp;使用`Node.js`时，我们不仅仅在实现一个`Web`应用，同时还实现了整个`HTTP`服务器。`Node.js`应用是由以下部分组成：

- 引入`required`模块：使用`require`指令来载入`Node.js`模块。
- 创建服务器：服务器可以监听客户端的请求，类似于`Apache`、`Nginx`等`HTTP`服务器。
- 接收请求与响应请求：服务器很容易创建，客户端可以使用浏览器或终端发送`HTTP`请求，服务器接收请求后返回响应数据。

#### 引入required模块

&emsp;&emsp;使用`require`指令来载入`http`模块，并将实例化的`HTTP`赋值给变量`http`：

``` javascript
var http = require("http");
```

#### 创建服务器

&emsp;&emsp;接下来使用`http.createServer`方法创建服务器，并使用`listen`方法绑定`8888`端口。函数通过`request`、`response`参数来接收和响应数据。在项目的根目录下创建`server.js`：

``` javascript
var http = require('http');
​
http.createServer(function(request, response) {
    // 发送HTTP头部，HTTP状态值是200(OK)，内容类型是“text/plain”
    response.writeHead(200, {
        'Content-Type': 'text/plain'
    });
    response.end('Hello World\n'); // 发送响应数据“Hello World”
}).listen(8888);
​
// 终端打印如下信息
console.log('Server running at http://127.0.0.1:8888/');
```

`http`模块提供了函数`createServer`，该函数会返回一个对象，其中有一个`listen`的方法，该方法有一个数值参数，指定这个`HTTP`服务器监听的端口号。以上代码完成了一个可以工作的`HTTP`服务器，使用`node`命令执行以上的代码：

``` bash
$ node server.js
Server running at http://127.0.0.1:8888/
```

接下来打开浏览器访问`http://127.0.0.1:8888/`，你会看到一个写着`Hello World`的网页。