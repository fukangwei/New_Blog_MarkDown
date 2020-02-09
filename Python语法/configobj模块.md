---
title: configobj模块
categories: Python语法
abbrlink: 3be3f75b
date: 2018-12-27 17:48:53
---
&emsp;&emsp;`configobj`模块用于读取或修改配置文件。例如配置文件`test.ini`的内容是：<!--more-->

``` bash
[server]
servername = 192.168.11.1
serverport = 8000

[client_srv]
# 这里是注释
server = localhost
port = 8000
```

&emsp;&emsp;解析文件：

``` python
from configobj import ConfigObj
​
conf_ini = "./test.ini"
config = ConfigObj(conf_ini, encoding='iso-8859-1')
print(config['server'])
print(config['server']['servername'])
```

执行结果：

``` bash
{'servername': '192.168.11.1', 'serverport': '8000'}
192.168.11.1
```

&emsp;&emsp;修改配置文件：

``` python
from configobj import ConfigObj
​
conf_ini = "./test.ini"
config = ConfigObj(conf_ini, encoding='iso-8859-1')
config['server']['servername'] = "127.0.0.1"
config.write()
```

&emsp;&emsp;添加新项：

``` python
from configobj import ConfigObj
​
conf_ini = "./test.ini"
config = ConfigObj(conf_ini, encoding='iso-8859-1')
config['new_items'] = {}
config['new_items']['Items1'] = "test items"
config.write()
```

&emsp;&emsp;删除项：

``` python
from configobj import ConfigObj
​
conf_ini = "./test.ini"
config = ConfigObj(conf_ini, encoding='iso-8859-1')
del config['client_srv']['port']
config.write()
```

&emsp;&emsp;将配置环境写入到不同的文件：

``` python
from configobj import ConfigObj
​
conf_ini = "./test.ini"
config = ConfigObj(conf_ini, encoding='iso-8859-1')
del config['client_srv']['port']
config.filename = "./test1.ini"
config.write()
```