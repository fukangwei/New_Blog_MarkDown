---
title: configparser模块
date: 2018-12-27 18:19:14
categories: Python语法
---
&emsp;&emsp;`configparser`用于配置文件解析，可以解析特定格式的配置文件，多数此类配置文件名格式为`XXX.ini`，例如`mysql`的配置文件。`ini`文件示例：

``` bash
[section1]
name = wang
age = 18
​
[section2]
name : python
age = 19
```

文件格式说明：

``` bash
[XXX]  # 代表节点
XX = XX 或者 XX : XX  # 代表参数
```

代码如下：

``` python
import configparser  # 导入模块
​
config = configparser.ConfigParser()  # 创建对象
# 读取配置文件，如果配置文件不存在，则进行创建
config.read("user.ini", encoding="utf-8")
secs = config.sections()  # 获取所有的节点名称
print(secs)  # 输出['section1', 'section2']
options = config.options('section1')  # 获取指定节点的所有key
print(options)  # 输出['name', 'age']
item_list = config.items('section1')  # 获取指定节点的键值对
print(item_list)  # 输出[('name', 'wang'), ('age', '18')]
val = config.get('section1', 'name')  # 获取指定节点的指定key的value
print(val)  # 输出“wang”
# 获取节点section1的age属性，属性需要是int型，否则ValueError
val = config.getint('section1', 'age')
print(val)  # 输出“18”
val = config.has_section('section1')  # 检查指定节点是否存在，返回True或False
print(val)  # 输出“True”
# 检查指定节点中是否存在某个key，返回True或False
val = config.has_option('section1', 'age')
print(val)  # 输出“True”
​
# 添加一个节点，节点名为node，此时添加的节点node尚未写入文件
config.add_section("node")
config.write(open('user.ini', "w"))  # 将添加的节点node写入配置文件
config.remove_section("node")  # 删除一个节点，节点名为node，删掉了内存中的节点node
config.write(open("user.ini", "w"))  # 将删除节点node后的文件内容回写到配置文件
# 在已存在的节点中添加一个键值对“k1 = v1”，如果该节点不存在则报错；如果key已经存在，则修改value
config.set("section1", "k1", "v1")
config.write(open("user.ini", "w"))
```

对于`getint`，还有对应的`getboolean`和`getfloat`函数。