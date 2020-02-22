---
title: util模块
categories: Node.js笔记
date: 2019-02-08 15:33:19
---
&emsp;&emsp;`util`是一个`Node.js`核心模块，提供常用函数的集合，用于弥补核心`JavaScript`的功能不足。<!--more-->

### util.inherits

&emsp;&emsp;`util.inherits`是一个实现对象间原型继承的函数：

``` javascript
util.inherits(constructor, superConstructor)
```

`JavaScript`的面向对象特性是基于原型的，与常见的基于类的不同。`JavaScript`没有提供对象继承的语言级别特性，而是通过原型复制来实现的。
&emsp;&emsp;`util.inherits`用法示例如下：

``` javascript
var util = require('util');

function Base() {
    this.name = 'base';
    this.base = 1991;
    this.sayHello = function () {
        console.log('Hello ' + this.name);
    };
}

Base.prototype.showName = function () {
    console.log(this.name);
};

function Sub() {
    this.name = 'sub';
}

util.inherits(Sub, Base);
var objBase = new Base();
objBase.showName(); // 输出“base”
objBase.sayHello(); // 输出“Hello base”
console.log(objBase); // 输出“Base { name: 'base', base: 1991, sayHello: [Function] }”
var objSub = new Sub();
objSub.showName(); // 输出“sub”
console.log(objSub); // 输出“Sub { name: 'sub' }”
```

我们定义了一个基础对象`Base`和一个继承自`Base`的`Sub`，`Base`有三个在构造函数内定义的属性和一个在原型中定义的函数。
&emsp;&emsp;注意，`Sub`仅仅继承了`Base`在原型中定义的函数，而构造函数内部创造的`base`属性和`sayHello`函数都没有被`Sub`继承。同时，在原型中定义的属性不会被`console.log`作为对象的属性输出。

### util.inspect

&emsp;&emsp;`util.inspect`是一个将任意对象转换为字符串的方法，通常用于调试和错误输出。它至少接受一个参数`object`，即要转换的对象：

``` javascript
util.inspect(object [, showHidden] [, depth] [, colors])
```

- `showHidden`：一个可选参数，如果值为`true`，将会输出更多隐藏信息。
- `depth`：表示最大递归的层数，如果对象很复杂，你可以指定层数以控制输出信息的多少。如果不指定`depth`，默认会递归`2`层；指定为`null`表示将不限递归层数完整遍历对象。
- `color`：如果`color`值为`true`，输出格式将会以`ANSI`颜色编码，通常用于在终端显示更漂亮的效果。

特别要指出的是，`util.inspect`并不会简单地直接把对象转换为字符串，即使该对象定义了`toString`方法，也不会调用。

``` javascript
var util = require('util');

function Person() {
    this.name = 'byvoid';
    this.toString = function () {
        return this.name;
    };
}

var obj = new Person();
console.log(util.inspect(obj));
console.log(util.inspect(obj, true));
```

执行结果：

``` javascript
Person { name: 'byvoid', toString: [Function] }
Person {
  name: 'byvoid',
  toString:
   { [Function]
     [length]: 0,
     [name]: '',
     [arguments]: null,
     [caller]: null,
     [prototype]: { [constructor]: [Circular] } } }
```

### util.isArray(object)

&emsp;&emsp;如果给定的参数`object`是一个数组，则返回`true`，否则返回`false`：

``` javascript
var util = require('util');

console.log(util.isArray([])) // 输出“true”
console.log(util.isArray(new Array)) // 输出“true”
console.log(util.isArray({})) // 输出“false”
```

### util.isRegExp(object)

&emsp;&emsp;如果给定的参数`object`是一个正则表达式，则返回`true`，否则返回`false`：

``` javascript
var util = require('util');

console.log(util.isRegExp(/some regexp/)) // 输出“true”
console.log(util.isRegExp(new RegExp('another regexp'))) // 输出“true”
console.log(util.isRegExp({})) // 输出“false”
```

### util.isDate(object)

&emsp;&emsp;如果给定的参数`object`是一个日期，则返回`true`，否则返回`false`：

``` javascript
var util = require('util');

console.log(util.isDate(new Date())) // 输出“true”
console.log(util.isDate(Date())) // 输出“false”(without 'new', returns a String)
console.log(util.isDate({})) // 输出“false”
```

### util.isError(object)

&emsp;&emsp;如果给定的参数`object`是一个错误对象，则返回`true`，否则返回`false`：

``` javascript
var util = require('util');

console.log(util.isError(new Error())) // 输出“true”
console.log(util.isError(new TypeError())) // 输出“true”
console.log( // 输出“false”
    util.isError({
        name: 'Error',
        message: 'an error occurred'
    })
)
```