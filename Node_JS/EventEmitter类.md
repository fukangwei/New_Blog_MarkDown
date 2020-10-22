---
title: EventEmitter类
categories: Node.js笔记
date: 2019-02-08 16:21:34
---
&emsp;&emsp;`Node.js`所有的异步`I/O`操作在完成时，都会发送一个事件到事件队列。`Node.js`里面的许多对象都会分发事件：一个`net.Server`对象会在每次有新连接时触发一个事件，一个`fs.readStream`对象会在文件被打开的时候触发一个事件。所有这些产生事件的对象都是`events.EventEmitter`的实例。<!--more-->

### EventEmitter类

&emsp;&emsp;`events`模块只提供了一个对象：`events.EventEmitter`。`EventEmitter`的核心就是事件触发与事件监听器功能的封装。你可以通过`require("events");`来访问该模块：

``` javascript
var events = require('events'); /* 引入events模块 */
var eventEmitter = new events.EventEmitter(); /* 创建eventEmitter对象 */
```

`EventEmitter`对象如果在实例化时发生错误，会触发`error`事件。当添加新的监听器时，`newListener`事件会触发，当监听器被移除时，`removeListener`事件被触发。
&emsp;&emsp;下面用一个简单的例子说明`EventEmitter`的用法：

``` javascript
var EventEmitter = require('events').EventEmitter;
var event = new EventEmitter();

event.on('some_event', function() {
    console.log('some_event事件触发');
});

setTimeout(function() {
    event.emit('some_event');
}, 1000);
```

运行这段代码，`1`秒后控制台输出了`some_event事件触发`。其原理是`event`对象注册了事件`some_event`的一个监听器，然后我们通过`setTimeout`在`1000`毫秒以后向`event`对象发送事件`some_event`，此时会调用`some_event`的监听器。
&emsp;&emsp;`EventEmitter`的每个事件由一个事件名和若干个参数组成，事件名是一个字符串，通常表达一定的语义。对于每个事件，`EventEmitter`支持若干个事件监听器。当事件触发时，注册到这个事件的事件监听器被依次调用，事件参数作为回调函数参数传递。
&emsp;&emsp;使用下面的例子解释这个过程：

``` javascript
var events = require('events');
var emitter = new events.EventEmitter();

emitter.on('someEvent', function(arg1, arg2) {
    console.log('listener1', arg1, arg2);
});

emitter.on('someEvent', function(arg1, arg2) {
    console.log('listener2', arg1, arg2);
});

emitter.emit('someEvent', 'arg1参数', 'arg2参数');
```

执行结果：

``` javascript
listener1 arg1参数 arg2参数
listener2 arg1参数 arg2参数
```

以上例子中，`emitter`为事件`someEvent`注册了两个事件监听器，然后触发了`someEvent`事件。从运行结果可以看到，两个事件监听器回调函数被先后调用。这就是`EventEmitter`最简单的用法。

### EventEmitter的属性

&emsp;&emsp;`EventEmitter`提供了多个属性，例如`on`和`emit`。`on`函数用于绑定事件函数，`emit`属性用于触发一个事件。

- `addListener(event, listener)`：为指定事件添加一个监听器到监听器数组的尾部。`eventEmitter.on`与`eventEmitter.addListener`没有区别，且一个事件可以绑定多个回调函数。
- `on(event, listener)`：为指定事件注册一个监听器，接受一个字符串`event`和一个回调函数：

``` javascript
server.on('connection', function (stream) {
    console.log('someone connected!');
});
```

- `once(event, listener)`：为指定事件注册一个单次监听器，即监听器最多只会触发一次，触发后立刻解除该监听器：

``` javascript
server.once('connection', function (stream) {
    console.log('Ah, we have our first user!');
});
```

- `removeListener(event, listener)`：移除指定事件的某个监听器，监听器必须是该事件已经注册过的监听器。它接受两个参数，第一个是事件名称，第二个是回调函数名称。

``` javascript
var callback = function(stream) {
    console.log('someone connected!');
};

server.on('connection', callback);
/* ... */
server.removeListener('connection', callback);
```

- `removeAllListeners([event])`：移除所有事件的所有监听器，如果指定事件，则移除指定事件的所有监听器。
- `setMaxListeners(n)`：默认情况下，对于`EventEmitters`，如果你添加的监听器超过`10`个就会输出警告信息。`setMaxListeners`函数用于提高监听器的默认限制的数量。
- `listeners(event)`：返回指定事件的监听器数组。按参数的顺序执行每个监听器，如果事件有注册监听返回`true`，否则返回`false`。

``` javascript
emit(event [, arg1] [, arg2] [, ...])
```

- `listenerCount(emitter, event)`：返回指定事件的监听器数量。

### EventEmitter的应用

&emsp;&emsp;以下实例通过`connection`事件演示了`EventEmitter`类的应用：

``` javascript
var events = require('events');
var eventEmitter = new events.EventEmitter();

var listener1 = function listener1() { // 监听器#1
    console.log('监听器listener1执行');
}

var listener2 = function listener2() { // 监听器#2
    console.log('监听器listener2执行');
}

eventEmitter.addListener('connection', listener1); // 绑定connection事件，处理函数为listener1
eventEmitter.on('connection', listener2); // 绑定connection事件，处理函数为listener2

var eventListeners = require('events').EventEmitter.listenerCount(eventEmitter, 'connection');
console.log(eventListeners + "个监听器监听连接事件");

eventEmitter.emit('connection'); // 处理connection事件
eventEmitter.removeListener('connection', listener1); // 移除监绑定的listener1函数
console.log("listener1不再受监听");
eventEmitter.emit('connection'); // 触发连接事件

eventListeners = require('events').EventEmitter.listenerCount(eventEmitter, 'connection');
console.log(eventListeners + "个监听器监听连接事件");

console.log("程序执行完毕");
```

执行结果：

``` javascript
2个监听器监听连接事件
监听器listener1执行
监听器listener2执行
listener1不再受监听
监听器listener2执行
1个监听器监听连接事件
程序执行完毕
```

### error事件

&emsp;&emsp;`EventEmitter`定义了一个特殊的事件`error`，它包含了错误的语义，我们在遇到异常的时候通常会触发`error`事件。当`error`被触发时，`EventEmitter`规定如果没有响应的监听器，`Node.js`会把它当作异常，退出程序并输出错误信息。
&emsp;&emsp;一般要为可能会触发`error`事件的对象设置监听器，避免遇到错误后整个程序崩溃：

``` javascript
var events = require('events');
var emitter = new events.EventEmitter();
emitter.emit('error');
```

运行时会显示以下错误：

``` javascript
node.js:201
throw e; // process.nextTick error, or 'error' event on first tick
^
Error: Uncaught, unspecified 'error' event.
at EventEmitter.emit (events.js:50:15)
at Object.<anonymous> (/home/byvoid/error.js:5:9)
at Module._compile (module.js:441:26)
at Object..js (module.js:459:10)
at Module.load (module.js:348:31)
at Function._load (module.js:308:12)
at Array.0 (module.js:479:10)
at EventEmitter._tickCallback (node.js:192:40)
```

### 继承EventEmitter

&emsp;&emsp;大多数时候我们不会直接使用`EventEmitter`，而是在对象中继承它。包括`fs`、`net`、`http`，只要是支持事件响应的核心模块，都是`EventEmitter`的子类。