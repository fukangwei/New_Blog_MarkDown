---
title: url异常处理
categories: Python语法
abbrlink: 41dc25d
date: 2018-12-27 19:09:30
---
&emsp;&emsp;`URL`异常处理分为`URLError`和`HTTPError`，其中`HTTPError`是`URLError`的子类。在终端输入：

``` python
import urllib.request
import urllib.error
​
req = urllib.request.Request("http://www.ooxx-fishc.com")
​
try:
    urllib.request.urlopen(req)
except urllib.error.URLError as e:
    print(e.reason)
```

执行结果：

``` bash
[Errno 11004] getaddrinfo failed
```

&emsp;&emsp;在终端输入：

``` python
import urllib.request
import urllib.error
​
req = urllib.request.Request("http://www.fishC.com/ooxx.html")
​
try:
    urllib.request.urlopen(req)
except urllib.error.HTTPError as e:
    print(e.code)
    print(e.read().decode("utf-8"))
```

结果为`404`和一个网页代码。
&emsp;&emsp;处理异常的第一种方法：

``` python
from urllib.request import Request, urlopen
from urllib.error import URLError, HTTPError
​
req = Request("http://www.ooxx-fishc.com/")
​
try:
    response = urlopen(req)
except HTTPError as e:
    print("The server could\'t fulfill the request.")
    print("Error code: ", e.code)
except URLError as e:
    print("We failed to reach a server.")
    print("Reason: ", e.reason)
else:
    pass  # everything is fine
```

执行结果：

``` bash
We failed to reach a server.
Reason: [Errno 11004] getaddrinfo failed
```

&emsp;&emsp;处理异常的第二种写法：

``` python
from urllib.request import Request, urlopen
from urllib.error import URLError
​
req = Request("http://www.fishC.com/ooxx.html")
​
try:
    response = urlopen(req)
except URLError as e:
    if hasattr(e, "reason"):
        print("We failed to reach a server.")
        print("Reason: ", e.reason)
    elif hasattr(e, "code"):
        print("The server could\'t fulfill the request.")
        print("Error code: ", e.code)
else:
    pass  # everything is fine
```

执行结果：

``` bash
We failed to reach a server.
Reason: Not Found
```