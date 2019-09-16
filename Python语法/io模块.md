---
title: io模块
date: 2018-12-27 17:59:47
categories: Python语法
---
### StringIO

&emsp;&emsp;很多时候数据读写不一定在文件中，也可以在内存中。`StringIO`顾名思义就是在内存中读写`str`。要把`str`写入`StringIO`，需要先创建一个`StringIO`，然后像文件一样写入即可：

``` python
from io import StringIO
​
f = StringIO()
f.write('hello')
f.write(' ')
f.write('world!')
print(f.getvalue())  # 执行结果为“hello world!”
```

`getvalue`方法用于获得写入后的`str`。
&emsp;&emsp;要读取`StringIO`，可以用一个`str`初始化`StringIO`，然后像读文件一样读取：

``` python
from io import StringIO
​
f = StringIO('Hello!\nHi!\nGoodbye!')
while True:
    s = f.readline()

    if s == '':
        break

    print(s.strip())
```

执行结果：

``` bash
Hello!
Hi!
Goodbye!
```

### BytesIO

&emsp;&emsp;`StringIO`操作的只能是`str`，如果要操作二进制数据，就需要使用`BytesIO`。`BytesIO`实现了在内存中读写`bytes`，我们创建一个`BytesIO`，然后写入一些`bytes`：

``` python
from io import BytesIO
​
f = BytesIO()
f.write('中文'.encode('utf-8'))
print(f.getvalue())  # 执行结果为“b'\xe4\xb8\xad\xe6\x96\x87'”
```

注意，写入的不是`str`，而是经过`UTF-8`编码的`bytes`。
&emsp;&emsp;和`StringIO`类似，可以用一个`bytes`初始化`BytesIO`，然后像读文件一样读取：

``` python
from io import BytesIO
​
f = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')
print(f.read())  # 执行结果为“b'\xe4\xb8\xad\xe6\x96\x87'”
```

`StringIO`和`BytesIO`是在内存中操作`str`和`bytes`的方法，和读写文件具有一致的接口。