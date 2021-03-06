---
title: json模块
categories: Python语法
date: 2018-12-28 09:16:28
---
&emsp;&emsp;可以使用`json`模块来对`JSON`数据进行编解码：<!--more-->

- `json.dumps`：对数据进行编码。
- `json.loads`：对数据进行解码。

&emsp;&emsp;将`Python`数据结构转换为`JSON`编码字符串：

``` python
import json

data = {'no': 1, 'name': 'Runoob', 'url': 'http://www.runoob.com'}
json_str = json.dumps(data)
print("Python原始数据：" + repr(data))
print("JSON对象：" + json_str)
```

执行结果：

``` bash
Python原始数据：{'no': 1, 'name': 'Runoob', 'url': 'http://www.runoob.com'}
JSON对象：{"no": 1, "name": "Runoob", "url": "http://www.runoob.com"}
```

&emsp;&emsp;将`JSON`编码字符串转换成`Python`数据结构：

``` python
import json

data1 = {'no': 1, 'name': 'Runoob', 'url': 'http://www.runoob.com'}
json_str = json.dumps(data1)
print("Python原始数据：" + repr(data1))
print("JSON对象：" + json_str)
data2 = json.loads(json_str)  # 将JSON对象转换为Python字典
print("data2['name']：" + data2['name'])
print("data2['url']：" + data2['url'])
```

执行结果：

``` bash
Python原始数据：{'no': 1, 'name': 'Runoob', 'url': 'http://www.runoob.com'}
JSON对象：{"no": 1, "name": "Runoob", "url": "http://www.runoob.com"}
data2['name']：Runoob
data2['url']：http://www.runoob.com
```

&emsp;&emsp;`json.loads`还可以对列表的字符串形式进行解析：

``` python
>>> import json
>>> str = "[1, 2, 3]"
>>> type(json.loads(str))
<class 'list'>
>>> print(json.loads(str))
[1, 2, 3]
```