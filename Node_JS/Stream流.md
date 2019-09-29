---
title: Stream流
categories: Node.js笔记
abbrlink: 657e9de6
date: 2019-02-08 14:17:36
---
&emsp;&emsp;`Stream`是一个抽象接口，`Node.js`中有很多对象实现了这个接口。例如，对`http`服务器发起请求的`request`对象就是一个`Stream`，还有`stdout`(标准输出)。
&emsp;&emsp;对于`Node.js`，`Stream`有四种流类型：

- `Readable`：可读操作。
- `Writable`：可写操作。
- `Duplex`：可读可写操作。
- `Transform`：操作被写入数据，然后读出结果。

所有的`Stream`对象都是`EventEmitter`的实例，常用的事件有：

- `data`：当有数据可读时触发。
- `end`：没有更多的数据可读时触发。
- `error`：在接收和写入过程中发生错误时触发。
- `finish`：所有数据已被写入到底层系统时触发。

### 从流中读取数据

&emsp;&emsp;创建`input.txt`：

``` javascript
菜鸟教程官网地址：www.runoob.com
```

创建`main.js`：

``` javascript
var fs = require("fs");
var data = '';

var readerStream = fs.createReadStream('input.txt'); // 创建可读流
readerStream.setEncoding('UTF8'); // 设置编码为utf8
​
// 处理流事件 --> data, end, and error
readerStream.on('data', function(chunk) {
    data += chunk;
});
​
readerStream.on('end',function(){
    console.log(data);
});

readerStream.on('error', function(err){
    console.log(err.stack);
});
​
console.log("程序执行完毕");
```

执行结果：

``` javascript
程序执行完毕
菜鸟教程官网地址：www.runoob.com
```

### 写入流

&emsp;&emsp;创建`main.js`：

``` javascript
var fs = require("fs");
var data = '菜鸟教程官网地址：www.runoob.com';
​
// 创建一个可以写入的流，写入到文件output.txt中
var writerStream = fs.createWriteStream('output.txt');
writerStream.write(data,'UTF8'); // 使用utf8编码写入数据
writerStream.end(); // 标记文件末尾
​
// 处理流事件 --> data, end and error
writerStream.on('finish', function() {
    console.log("写入完成");
});
​
writerStream.on('error', function(err){
    console.log(err.stack);
});
​
console.log("程序执行完毕");
```

以上程序会将`data`变量的数据写入到`output.txt`。执行结果：

``` javascript
程序执行完毕
写入完成
```

查看`output.txt`文件的内容：

``` bash
$ cat output.txt
菜鸟教程官网地址：www.runoob.com
```

### 管道流

&emsp;&emsp;管道提供了一个输出流到输入流的机制，通常用于从一个流中获取数据，并将数据传递到另外一个流中。
&emsp;&emsp;我们把文件比作装水的桶，而水就是文件里的内容，用一根管子(`pipe`)连接两个桶，使得水从一个桶流入另一个桶，这样就慢慢地实现了大文件的复制过程。
&emsp;&emsp;创建`input.txt`：

``` javascript
菜鸟教程官网地址：www.runoob.com
```

创建`main.js`：

``` javascript
var fs = require("fs");
// 创建一个可读流
var readerStream = fs.createReadStream('input.txt');
// 创建一个可写流
var writerStream = fs.createWriteStream('output.txt');
// 管道读写操作，读取input.txt文件内容，并将内容写入到output.txt
readerStream.pipe(writerStream);
console.log("程序执行完毕");
```

### 链式流

&emsp;&emsp;链式是通过连接输出流到另外一个流，并创建多个流操作链的机制，一般用于管道操作。接下来用管道和链式来压缩和解压文件。
&emsp;&emsp;创建`compress.js`：

``` javascript
var fs = require("fs");
var zlib = require('zlib');
​
// 压缩input.txt文件为input.txt.gz
fs.createReadStream('input.txt')
    .pipe(zlib.createGzip())
    .pipe(fs.createWriteStream('input.txt.gz'));
​
console.log("文件压缩完成");
```

执行完以上操作后，可以看到当前目录下生成了`input.txt`的压缩文件`input.txt.gz`。
&emsp;&emsp;接下来创建`decompress.js`：

``` javascript
var fs = require("fs");
var zlib = require('zlib');
​
// 解压input.txt.gz文件为input.txt
fs.createReadStream('input.txt.gz')
    .pipe(zlib.createGunzip())
    .pipe(fs.createWriteStream('input.txt'));
​
console.log("文件解压完成");
```