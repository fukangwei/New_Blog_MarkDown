---
title: Buffer缓冲区
categories: Node.js笔记
abbrlink: bcf52270
date: 2019-02-08 16:36:39
---
&emsp;&emsp;`JavaScript`语言自身只有字符串数据类型，没有二进制数据类型。但在处理像`TCP`流或文件流时，必须使用到二进制数据。因此在`Node.js`中，定义了一个`Buffer`类，该类用来创建一个专门存放二进制数据的缓存区。一个`Buffer`类似于一个整数数组，但它对应于`V8`堆内存之外的一块原始内存。<!--more-->
&emsp;&emsp;在`v6.0`之前，创建`Buffer`对象直接使用`new Buffer`构造函数来创建对象实例，但是`Buffer`对内存的权限操作相比很大，可以直接捕获一些敏感信息，所以在`v6.0`以后，官方文档里面建议使用`Buffer.from`接口去创建`Buffer`对象。

### Buffer与字符编码

&emsp;&emsp;`Buffer`实例一般用于表示编码字符的序列，比如`UTF-8`、`UCS2`、`Base64`或十六进制编码的数据。通过使用显式的字符编码，就可以在`Buffer`实例与普通的`JavaScript`字符串之间进行相互转换。

``` javascript
const buf = Buffer.from('runoob', 'ascii');
console.log(buf.toString('hex')); // 输出“72756e6f6f62”
console.log(buf.toString('base64')); // 输出“cnVub29i”
```

`Node.js`目前支持的字符编码包括：

- `ascii`：仅支持`7`位`ASCII`数据，如果设置去掉高位的话，这种编码是非常快的。
- `utf8`：多字节编码的`Unicode`字符，许多网页和其他文档格式都使用`UTF-8`。
- `utf16le`：`2`或`4`个字节，小字节序编码的`Unicode`字符。
- `ucs2`：`utf16le`的别名。
- `base64`：`Base64`编码。
- `latin1`：一种把`Buffer`编码成一字节编码的字符串的方式。
- `binary`：`latin1`的别名。
- `hex`：将每个字节编码为两个十六进制字符。

### 创建Buffer类

&emsp;&emsp;`Buffer`提供了以下`API`来创建`Buffer`类：

- `Buffer.alloc(size[, fill[, encoding]])`：返回一个指定大小的`Buffer`实例，如果没有设置`fill`，则默认填满`0`。
- `Buffer.allocUnsafe(size)`：返回一个指定大小的`Buffer`实例，但是它不会被初始化，所以它可能包含敏感的数据。
- `Buffer.from(array)`：返回一个被`array`的值初始化的新的`Buffer`实例(传入的`array`的元素只能是数字，不然就会自动被`0`覆盖)。
- `Buffer.from(arrayBuffer[, byteOffset[, length]])`：返回一个新建的与给定的`ArrayBuffer`共享同一内存的`Buffer`。
- `Buffer.from(buffer)`：复制传入的`Buffer`实例的数据，并返回一个新的`Buffer`实例。
- `Buffer.from(string[, encoding])`：返回一个被`string`的值初始化的新的`Buffer`实例。

``` javascript
// 创建一个长度为10，且用0填充的Buffer
const buf1 = Buffer.alloc(10);
// 创建一个长度为10，且用0x1填充的Buffer
const buf2 = Buffer.alloc(10, 1);

// 创建一个长度为10，且未初始化的Buffer。这个方法比调用Buffer.alloc更快，
// 但返回的Buffer实例可能包含旧数据，因此需要使用fill或write重写
const buf3 = Buffer.allocUnsafe(10);
// 创建一个包含[0x1, 0x2, 0x3]的Buffer
const buf4 = Buffer.from([1, 2, 3]);
// 创建一个包含UTF-8字节[0x74, 0xc3, 0xa9, 0x73, 0x74]的Buffer
const buf5 = Buffer.from('tést');
// 创建一个包含Latin-1字节[0x74, 0xe9, 0x73, 0x74]的Buffer
const buf6 = Buffer.from('tést', 'latin1');
```

### 写入缓冲区

&emsp;&emsp;写入`Node.js`缓冲区的语法：

``` javascript
buf.write(string [, offset [, length]] [, encoding])
```

- `string`：写入缓冲区的字符串。
- `offset`：缓冲区开始写入的索引值，默认为`0`。
- `length`：写入的字节数，默认为`buffer.length`。
- `encoding`：使用的编码，默认为`utf8`。

根据`encoding`的字符编码写入`string`到`buf`中的`offset`位置。该函数返回实际写入的大小，如果`buffe`空间不足，则只会写入部分字符串。

``` javascript
buf = Buffer.alloc(256);
len = buf.write("www.runoob.com");
console.log("写入字节数："+  len);
```

执行结果是`写入字节数：14`。

### 从缓冲区读取数据

&emsp;&emsp;读取`Node.js`缓冲区数据的语法：

``` javascript
buf.toString([encoding [, start [, end]]])
```

- `encoding`：使用的编码，默认为`utf8`。
- `start`：指定开始读取的索引位置，默认为`0`。
- `end`：结束位置，默认为缓冲区的末尾。

该函数解码缓冲区数据并使用指定的编码返回字符串。

``` javascript
buf = Buffer.alloc(26);
​
for (var i = 0; i < 26; i++) {
    buf[i] = i + 97;
}

console.log( buf.toString('ascii')); // 输出“abcdefghijklmnopqrstuvwxyz”
console.log( buf.toString('ascii', 0, 5)); // 输出“abcde”
console.log( buf.toString('utf8', 0, 5)); // 输出“abcde”
console.log( buf.toString(undefined, 0, 5)); // 使用utf8编码，并输出“abcde”
```

### 将Buffer转换为JSON对象

&emsp;&emsp;将`Node.js`的`Buffer`转换为`JSON`对象：

``` javascript
buf.toJSON()
```

当字符串化一个`Buffer`实例时，`JSON.stringify`会隐式地调用`toJSON`。

``` javascript
const buf = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5]);
const json = JSON.stringify(buf);

console.log(json); // 输出“{"type":"Buffer","data":[1,2,3,4,5]}”

const copy = JSON.parse(json, (key, value) => {
    return value && value.type === 'Buffer' ? Buffer.from(value.data) : value;
});
​
console.log(copy); // 输出“<Buffer 01 02 03 04 05>”
```

### 缓冲区合并

&emsp;&emsp;`Node.js`缓冲区合并：

``` javascript
Buffer.concat(list[, totalLength])
```

参数`list`是合并的`Buffer`对象数组列表，`totalLength`指定合并后`Buffer`对象的总长度。该函数返回一个多个成员合并的新`Buffer`对象。

``` javascript
var buffer1 = Buffer.from(('菜鸟教程'));
var buffer2 = Buffer.from(('www.runoob.com'));
var buffer3 = Buffer.concat([buffer1, buffer2]);
console.log("buffer3内容：" + buffer3.toString());
```

执行结果是`buffer3内容：菜鸟教程 www.runoob.com`。

### 缓冲区比较

&emsp;&emsp;`Node.js`的`Buffer`比较：

``` javascript
buf.compare(otherBuffer);
```

参数`otherBuffer`是与`buf`对象比较的另外一个`Buffer`对象。该函数返回一个数字，表示`buf`在`otherBuffer`之前、之后或相同。

``` javascript
var buffer1 = Buffer.from('ABC');
var buffer2 = Buffer.from('ABCD');
var result = buffer1.compare(buffer2);

if(result < 0) {
    console.log(buffer1 + "在" + buffer2 + "之前");
} else if(result == 0){
    console.log(buffer1 + "与" + buffer2 + "相同");
} else {
    console.log(buffer1 + "在" + buffer2 + "之后");
}
```

执行结果是`ABC在ABCD之前`。

### 拷贝缓冲区

&emsp;&emsp;`Node.js`缓冲区拷贝语法如下，该函数没有返回值：

``` javascript
buf.copy(targetBuffer [, targetStart [, sourceStart [, sourceEnd]]])
```

- `targetBuffer`：要拷贝的`Buffer`对象。
- `targetStart`：数字，默认是`0`。
- `sourceStart`：数字，默认是`0`。
- `sourceEnd`：数字，默认是`buffer.length`。

``` javascript
var buf1 = Buffer.from('abcdefghijkl');
var buf2 = Buffer.from('RUNOOB');

buf2.copy(buf1, 2); // 将buf2插入到buf1指定位置上
console.log(buf1.toString());
```

执行结果是`abRUNOOBijkl`。

### 缓冲区裁剪

&emsp;&emsp;`Node.js`缓冲区裁剪：

``` javascript
buf.slice([start [, end]])
```

参数`start`是数字，可选项，默认是`0`；参数`end`是数字，可选项，默认是`buffer.length`。该函数返回一个新的缓冲区，它和旧缓冲区指向同一块内存，但是从索引`start`到`end`的位置剪切。

``` javascript
var buffer1 = Buffer.from('runoob');
var buffer2 = buffer1.slice(0, 2); // 剪切缓冲区
console.log("buffer2 content: " + buffer2.toString());
```

执行结果是`buffer2 content: ru`。

### 缓冲区长度

&emsp;&emsp;`Node.js`缓冲区长度计算：

``` javascript
buf.length;
```

该函数返回`Buffer`对象所占据的内存长度。

``` javascript
var buffer = Buffer.from('www.runoob.com');
// 缓冲区长度
console.log("buffer length: " + buffer.length);
```

执行结果是`buffer length: 14`。

### 方法参考手册

&emsp;&emsp;以下列出了`Node.js`的`Buffer`模块常用的方法，注意有些方法在旧版本是没有的。

- `new Buffer(size)`：分配一个新的`size`大小单位为`8`位字节的`buffer`。注意，`size`必须小于`kMaxLength`，否则将会抛出异常`RangeError`。该函数目前已废弃，使用`Buffer.alloc`或`Buffer.allocUnsafe`代替。
- `new Buffer(buffer)`：拷贝参数`buffer`的数据到`Buffer`实例，该函数目前已废弃，使用`Buffer.from(buffer)`代替。
- `new Buffer(str [, encoding])`：分配一个新的`buffer`，其中包含着传入的`str`字符串，`encoding`编码方式默认为`utf8`。该函数目前已废弃，使用`Buffer.from(string[, encoding])`代替。
- `buf.length`：返回这个`buffer`的`bytes`数，注意这未必是`buffer`里面内容的大小。`length`是`buffer`对象所分配的内存数，它不会随着这个`buffer`对象内容的改变而改变。
- `buf.write(string [, offset [, length]] [, encoding])`：根据参数`offset`偏移量和指定的`encoding`编码方式，将参数`string`数据写入`buffer`。`offset`偏移量默认值是`0`；`encoding`编码方式默认是`utf8`；`length`长度是将要写入的字符串的`bytes`大小，默认是`buffer.length - offset`。该函数返回`number`类型，表示写入了多少`8`位字节流。如果`buffer`没有足够的空间来放整个`string`，它将只会只写入部分字符串。这个方法不会出现写入部分字符。
- `buf.writeUIntLE(value, offset, byteLength [, noAssert])`：将`value`写入到`buffer`里，它由`offset`和`byteLength`决定，最高支持`48`位无符号整数，小端对齐。当`noAssert`值为`true`时，不再验证`value`和`offset`的有效性，默认是`false`。

``` javascript
const buf = Buffer.allocUnsafe(6);
buf.writeUIntLE(0x1234567890ab, 0, 6);
console.log(buf); // 输出“<Buffer ab 90 78 56 34 12>”
```

- `buf.writeUIntBE(value, offset, byteLength [, noAssert])`：将`value`写入到`buffer`里，它由`offset`和`byteLength`决定，最高支持`48`位无符号整数，大端对齐。

``` javascript
const buf = Buffer.allocUnsafe(6);
buf.writeUIntBE(0x1234567890ab, 0, 6);
console.log(buf); // 输出“<Buffer 12 34 56 78 90 ab>”
```

- `buf.writeIntLE(value, offset, byteLength [, noAssert])`：将`value`写入到`buffer`里，它由`offset`和`byteLength`决定，最高支持`48`位有符号整数，小端对齐。
- `buf.writeIntBE(value, offset, byteLength [, noAssert])`：将`value`写入到`buffer`里，它由`offset`和`byteLength`决定，最高支持`48`位有符号整数，大端对齐。
- `buf.readUIntLE(offset, byteLength [, noAssert])`：支持读取`48`位以下的无符号数字，小端对齐。
- `buf.readUIntBE(offset, byteLength [, noAssert])`：支持读取`48`位以下的无符号数字，大端对齐。
- `buf.readIntLE(offset, byteLength [, noAssert])`：支持读取`48`位以下的有符号数字，小端对齐。
- `buf.readIntBE(offset, byteLength [, noAssert])`：支持读取`48`位以下的有符号数字，大端对齐。
- `buf.toString([encoding [, start [, end]]])`：根据`encoding`参数(默认是`utf8`)返回一个解码过的`string`类型。还会根据传入的参数`start`(默认是`0`)和`end`(默认是`buffer.length`)作为取值范围。
- `buf.toJSON()`：将`Buffer`实例转换为`JSON`对象。
- `buf[index]`：获取或设置指定的字节。返回值代表一个字节，所以返回值的合法范围是十六进制`0x00`到`0xFF`，或者十进制`0`至`255`。
- `buf.equals(otherBuffer)`：比较两个缓冲区是否相等，如果是返回`true`，否则返回`false`。
- `buf.compare(otherBuffer)`：比较两个`Buffer`对象，返回一个数字，表示`buf`在`otherBuffer`之前、之后或相同。
- `buf.copy(targetBuffer [, targetStart [, sourceStart [, sourceEnd]]])`：`buffer`拷贝，源和目标可以相同。`targetStart`目标开始偏移和`sourceStart`源开始偏移默认都是`0`。`sourceEnd`源结束位置偏移默认是源的长度`buffer.length`。
- `buf.slice([start [, end]])`：剪切`Buffer`对象，根据`start`(默认是`0`)和`end`(默认是`buffer.length`)裁剪了索引。负的索引是从`buffer`尾部开始计算的。
- `buf.readUInt8(offset [, noAssert])`：根据指定的偏移量，读取一个无符号`8`位整数。
- `buf.readUInt16LE(offset [, noAssert])`：根据指定的偏移量，使用特殊的`endian`字节序格式读取一个无符号`16`位整数。
- `buf.readUInt16BE(offset [, noAssert])`：根据指定的偏移量，使用特殊的`endian`字节序格式读取一个无符号`16`位整数，大端对齐。
- `buf.readUInt32LE(offset[, noAssert])`：根据指定的偏移量，使用指定的`endian`字节序格式读取一个无符号`32`位整数，小端对齐。
- `buf.readUInt32BE(offset[, noAssert])`：根据指定的偏移量，使用指定的`endian`字节序格式读取一个无符号`32`位整数，大端对齐。
- `buf.readInt8(offset [, noAssert])`：根据指定的偏移量，读取一个有符号`8`位整数。
- `buf.readInt16LE(offset [, noAssert])`：根据指定的偏移量，使用特殊的`endian`格式读取一个有符号`16`位整数，小端对齐。
- `buf.readInt16BE(offset [, noAssert])`：根据指定的偏移量，使用特殊的`endian`格式读取一个有符号`16`位整数，大端对齐。
- `buf.readInt32LE(offset [, noAssert])`：根据指定的偏移量，使用指定的`endian`字节序格式读取一个有符号`32`位整数，小端对齐。
- `buf.readInt32BE(offset [, noAssert])`：根据指定的偏移量，使用指定的`endian`字节序格式读取一个有符号`32`位整数，大端对齐。
- `buf.readFloatLE(offset [, noAssert])`：根据指定的偏移量，使用指定的`endian`字节序格式读取一个`32`位双浮点数，小端对齐。
- `buf.readFloatBE(offset [, noAssert])`：根据指定的偏移量，使用指定的`endian`字节序格式读取一个`32`位双浮点数，大端对齐。
- `buf.readDoubleLE(offset [, noAssert])`：根据指定的偏移量，使用指定的`endian`字节序格式读取一个`64`位双精度数，小端对齐。
- `buf.readDoubleBE(offset [, noAssert])`：根据指定的偏移量，使用指定的`endian`字节序格式读取一个`64`位双精度数，大端对齐。
- `buf.writeUInt8(value, offset [, noAssert])`：根据传入的`offset`偏移量将`value`写入`buffer`。注意，`value`必须是一个合法的无符号`8`位整数。
- `buf.writeUInt16LE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个合法的无符号`16`位整数，小端对齐。
- `buf.writeUInt16BE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个合法的无符号`16`位整数，大端对齐。
- `buf.writeUInt32LE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个合法的无符号`32`位整数，小端对齐。
- `buf.writeUInt32BE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个合法的有符号`32`位整数。
- `buf.writeInt16LE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个合法的有符号`16`位整数。
- `buf.writeInt16BE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个合法的有符号`16`位整数。
- `buf.writeInt32LE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个合法的有符号`32`位整数。
- `buf.writeInt32BE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个合法的有符号`32`位整数。
- `buf.writeFloatLE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，当`value`不是一个`32`位浮点数类型的值时，结果将是不确定的。
- `buf.writeFloatBE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，当`value`不是一个`32`位浮点数类型的值时，结果将是不确定的。
- `buf.writeDoubleLE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个有效的`64`位`double`类型的值。
- `buf.writeDoubleBE(value, offset [, noAssert])`：根据传入的`offset`偏移量和指定的`endian`格式将`value`写入`buffer`。注意，`value`必须是一个有效的`64`位`double`类型的值。
- `buf.fill(value [, offset] [, end])`：使用指定的`value`来填充这个`buffer`。如果没有指定`offset`(默认是`0`)和`end`(默认是`buffer.length`)，将会填充整个`buffer`。