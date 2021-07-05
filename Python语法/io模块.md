---
title: io模块
categories: Python语法
date: 2018-12-27 17:59:47
---
### StringIO

&emsp;&emsp;很多时候，数据读写不一定在文件中进行，也可以在内存中，`StringIO`就是在内存中读写`str`。<!--more-->
&emsp;&emsp;向内存中写`str`：

``` python
from io import StringIO

f = StringIO()
f.write('hello')
f.write(' ')
f.write('world!')
print(f.getvalue())  # 执行结果为“hello world!”
```

&emsp;&emsp;从内存中读`str`：

``` python
from io import StringIO

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

&emsp;&emsp;`StringIO`操作的只能是`str`，如果要操作二进制数据，就需要使用`BytesIO`。
&emsp;&emsp;向内存中写`Byte`：

``` python
from io import BytesIO

f = BytesIO()
f.write('中文'.encode('utf-8'))
print(f.getvalue())  # 执行结果为“b'\xe4\xb8\xad\xe6\x96\x87'”
```

&emsp;&emsp;从内存中读`Byte`：

``` python
from io import BytesIO

f = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')
print(f.read())  # 执行结果为“b'\xe4\xb8\xad\xe6\x96\x87'”
```