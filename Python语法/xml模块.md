---
title: xml模块
categories: Python语法
date: 2019-01-12 14:08:01
---
### 关于XML

&emsp;&emsp;`XML`是一种标记电子文件，使其具有结构性的标记语言，经常用于传输和存储数据。<!--more-->
&emsp;&emsp;`movies.xml`如下：

``` xml
<collection shelf="New Arrivals">
    <movie title="Enemy Behind">
        <type>War, Thriller</type>
        <format>DVD</format>
        <year>2003</year>
        <rating>PG</rating>
        <stars>10</stars>
        <description>Talk about a US-Japan war</description>
    </movie>
    <movie title="Transformers">
        <type>Anime, Science Fiction</type>
        <format>DVD</format>
        <year>1989</year>
        <rating>R</rating>
        <stars>8</stars>
        <description>A schientific fiction</description>
    </movie>
    <movie title="Trigun">
        <type>Anime, Action</type>
        <format>DVD</format>
        <episodes>4</episodes>
        <rating>PG</rating>
        <stars>10</stars>
        <description>Vash the Stampede!</description>
    </movie>
    <movie title="Ishtar">
        <type>Comedy</type>
        <format>VHS</format>
        <rating>PG</rating>
        <stars>2</stars>
        <description>Viewable boredom</description>
    </movie>
</collection>
```

### 关于DOM

&emsp;&emsp;`DOM`是`W3C`组织推荐的处理`XML`的标准编程接口。`DOM`解析`XML`文档的方法如下：

``` python
from xml.dom.minidom import parse
import xml.dom.minidom

DOMTree = xml.dom.minidom.parse("movies.xml")  # 使用minidom解析器打开XML文档
collection = DOMTree.documentElement

if collection.hasAttribute("shelf"):
    print("Root element : %s" % collection.getAttribute("shelf"))

movies = collection.getElementsByTagName("movie")  # 在集合中获取所有电影

for movie in movies:  # 打印每部电影的详细信息
    print("*****Movie*****")

    if movie.hasAttribute("title"):
        print("Title: %s" % movie.getAttribute("title"))

    type = movie.getElementsByTagName('type')[0]
    print("Type: %s" % type.childNodes[0].data)
    format = movie.getElementsByTagName('format')[0]
    print("Format: %s" % format.childNodes[0].data)
    rating = movie.getElementsByTagName('rating')[0]
    print("Rating: %s" % rating.childNodes[0].data)
    description = movie.getElementsByTagName('description')[0]
    print("Description: %s" % description.childNodes[0].data)
```

执行结果：

``` python
Root element : New Arrivals
*****Movie*****
Title: Enemy Behind
Type: War, Thriller
Format: DVD
Rating: PG
Description: Talk about a US-Japan war
*****Movie*****
Title: Transformers
Type: Anime, Science Fiction
Format: DVD
Rating: R
Description: A schientific fiction
*****Movie*****
Title: Trigun
Type: Anime, Action
Format: DVD
Rating: PG
Description: Vash the Stampede!
*****Movie*****
Title: Ishtar
Type: Comedy
Format: VHS
Rating: PG
Description: Viewable boredom
```