---
title: xml模块
date: 2019-01-12 14:08:01
categories: Python语法
---
### 什么是XML？

&emsp;&emsp;`XML`指可扩展标记语言(`Extensible Markup Language`)，标准通用标记语言的子集，是一种用于标记电子文件使其具有结构性的标记语言。它被设计用于传输和存储数据。
&emsp;&emsp;`XML`是一套定义语义标记的规则，这些标记将文档分成许多部件，并对这些部件加以标识。它也是元标记语言，即定义了用于定义其他与特定领域有关的、语义的、结构化的标记语言的句法语言。

### python对XML的解析

&emsp;&emsp;常见的`XML`编程接口有`DOM`和`SAX`，这两种接口处理`XML`文件的方式不同，当然使用场合也不同。`python`有三种方法解析`XML`：`SAX`、`DOM`以及`ElementTree`：

- `SAX(Simple API for XML)`：`python`标准库包含`SAX`解析器，`SAX`用事件驱动模型，通过在解析`XML`的过程中触发一个个的事件，并调用用户定义的回调函数来处理`XML`文件。
- `DOM(Document Object Model)`：将`XML`数据在内存中解析成一个树，通过对树的操作来操作`XML`。

本章节使用到的`XML`实例文件`movies.xml`内容如下：

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

### python使用SAX解析xml

&emsp;&emsp;`SAX`是一种基于事件驱动的`API`。利用`SAX`解析`XML`文档牵涉到两个部分，即`解析器`和`事件处理器`。

- 解析器负责读取`XML`文档，并向事件处理器发送事件，例如元素开始和元素结束事件。
- 事件处理器则负责对事件作出相应，对传递的`XML`数据进行处理。

&emsp;&emsp;在`python`中使用`SAX`方式处理`XML`，要先引入`xml.sax`中的`parse`函数，还有`xml.sax.handler`中的`ContentHandler`。

#### ContentHandler类方法介绍

- `characters(content)`方法：调用时机是：从行开始，在遇到标签之前，如果存在字符，`content`的值为这些字符串；从一个标签开始，在遇到下一个标签之前，如果存在字符，`content`的值为这些字符串；从一个标签，在遇到行结束符之前，如果存在字符，`content`的值为这些字符串。标签可以是开始标签，也可以是结束标签。
- `startDocument`方法：文档启动的时候调用。
- `endDocument`方法：解析器到达文档结尾时调用。
- `startElement(name, attrs)`方法：遇到`XML`开始标签时调用，`name`是标签的名字，`attrs`是标签的属性值字典。
- `endElement(name)`方法：遇到`XML`结束标签时调用。

#### make_parser方法

&emsp;&emsp;以下方法创建一个新的解析器对象并返回：

``` python
xml.sax.make_parser([parser_list])
```

参数`parser_list`是可选参数，即解析器列表。

#### parser方法

&emsp;&emsp;以下方法创建一个`SAX`解析器，并解析`xml`文档：

``` python
xml.sax.parse(xmlfile, contenthandler[, errorhandler])
```

参数`xmlfile`是`xml`文件名；`contenthandler`必须是一个`ContentHandler`的对象；对于`errorhandler`，如果指定该参数，`errorhandler`必须是一个`SAX ErrorHandler`对象。

#### parseString方法

&emsp;&emsp;`parseString`方法创建一个`XML`解析器，并解析`xml`字符串：

``` python
xml.sax.parseString(xmlstring, contenthandler[, errorhandler])
```

参数`xmlstring`是`xml`字符串；`contenthandler`必须是一个`ContentHandler`的对象；对于`errorhandler`，如果指定该参数，`errorhandler`必须是一个`SAX ErrorHandler`对象。

### Python解析XML实例

&emsp;&emsp;代码如下：

``` python
import xml.sax
​
class MovieHandler(xml.sax.ContentHandler):
    def __init__(self):
        self.CurrentData = ""
        self.type = ""
        self.format = ""
        self.year = ""
        self.rating = ""
        self.stars = ""
        self.description = ""

    def startElement(self, tag, attributes):  # 元素开始调用
        self.CurrentData = tag

        if tag == "movie":
            print("*****Movie*****")
            title = attributes["title"]
            print("Title:", title)

    def endElement(self, tag):  # 元素结束调用
        if self.CurrentData == "type":
            print("Type:", self.type)
        elif self.CurrentData == "format":
            print("Format:", self.format)
        elif self.CurrentData == "year":
            print("Year:", self.year)
        elif self.CurrentData == "rating":
            print("Rating:", self.rating)
        elif self.CurrentData == "stars":
            print("Stars:", self.stars)
        elif self.CurrentData == "description":
            print("Description:", self.description)

        self.CurrentData = ""
​
    def characters(self, content):  # 读取字符时调用
        if self.CurrentData == "type":
            self.type = content
        elif self.CurrentData == "format":
            self.format = content
        elif self.CurrentData == "year":
            self.year = content
        elif self.CurrentData == "rating":
            self.rating = content
        elif self.CurrentData == "stars":
            self.stars = content
        elif self.CurrentData == "description":
            self.description = content
​
if __name__ == "__main__":
    parser = xml.sax.make_parser()  # 创建一个XMLReader
    # turn off namepsaces
    parser.setFeature(xml.sax.handler.feature_namespaces, 0)
    # 重写ContextHandler
    Handler = MovieHandler()
    parser.setContentHandler(Handler)
    parser.parse("movies.xml")
```

执行结果：

``` python
*****Movie*****
Title: Enemy Behind
Type: War, Thriller
Format: DVD
Year: 2003
Rating: PG
Stars: 10
Description: Talk about a US-Japan war
*****Movie*****
Title: Transformers
Type: Anime, Science Fiction
Format: DVD
Year: 1989
Rating: R
Stars: 8
Description: A schientific fiction
*****Movie*****
Title: Trigun
Type: Anime, Action
Format: DVD
Rating: PG
Stars: 10
Description: Vash the Stampede!
*****Movie*****
Title: Ishtar
Type: Comedy
Format: VHS
Rating: PG
Stars: 2
Description: Viewable boredom
```

### 使用xml.dom解析xml

&emsp;&emsp;文件对象模型(`Document Object Model`，即`DOM`)是`W3C`组织推荐的处理可扩展置标语言的标准编程接口。
&emsp;&emsp;一个`DOM`的解析器在解析一个`XML`文档时，一次性读取整个文档，把文档中所有元素保存在内存中的一个树结构里，之后可以利用`DOM`提供的不同的函数来读取或修改文档的内容和结构，也可以把修改过的内容写入`xml`文件。
&emsp;&emsp;`python`中用`xml.dom.minidom`来解析`xml`文件：

``` python
from xml.dom.minidom import parse
import xml.dom.minidom
​
DOMTree = xml.dom.minidom.parse("movies.xml")  # 使用minidom解析器打开XML文档
collection = DOMTree.documentElement
​
if collection.hasAttribute("shelf"):
    print("Root element : %s" % collection.getAttribute("shelf"))
​
movies = collection.getElementsByTagName("movie")  # 在集合中获取所有电影
​
for movie in movies:  # 打印每部电影的详细信息
    print("*****Movie*****")

    if movie.hasAttribute("title"):
        print("Title: %s" % movie.getAttribute("title"))
​
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

---

### xml.etree.ElementTree -- The ElementTree XML API

&emsp;&emsp;The `xml.etree.ElementTree` module implements a simple and efficient `API` for parsing and creating `XML` data.
&emsp;&emsp;Changed in `version 3.3`: This module will use a fast implementation whenever available. The `xml.etree.cElementTree` module is deprecated.
&emsp;&emsp;**Warning**: The `xml.etree.ElementTree` module is not secure against maliciously constructed data. If you need to parse untrusted or unauthenticated data.

### Tutorial

&emsp;&emsp;This is a short tutorial for using `xml.etree.ElementTree` (`ET` in short). The goal is to demonstrate some of the building blocks and basic concepts of the module.

#### XML tree and elements

&emsp;&emsp;`XML` is an inherently hierarchical data format, and the most natural way to represent it is with a tree. `ET` has two classes for this purpose - `ElementTree` represents the whole `XML` document as a tree, and `Element` represents a single node in this tree. Interactions with the whole document (reading and writing to/from files) are usually done on the `ElementTree` level. Interactions with a single `XML` element and its `sub-elements` are done on the `Element` level.

#### Parsing XML

&emsp;&emsp;We'll be using the following `XML` document as the sample data for this section:

``` xml
<?xml version="1.0"?>
<data>
    <country name="Liechtenstein">
        <rank>1</rank>
        <year>2008</year>
        <gdppc>141100</gdppc>
        <neighbor name="Austria" direction="E"/>
        <neighbor name="Switzerland" direction="W"/>
    </country>
    <country name="Singapore">
        <rank>4</rank>
        <year>2011</year>
        <gdppc>59900</gdppc>
        <neighbor name="Malaysia" direction="N"/>
    </country>
    <country name="Panama">
        <rank>68</rank>
        <year>2011</year>
        <gdppc>13600</gdppc>
        <neighbor name="Costa Rica" direction="W"/>
        <neighbor name="Colombia" direction="E"/>
    </country>
</data>
```

We can import this data by reading from a file:

``` python
import xml.etree.ElementTree as ET

tree = ET.parse('country_data.xml')
root = tree.getroot()
Or directly from a string:
root = ET.fromstring(country_data_as_string)
```

`fromstring()` parses `XML` from a string directly into an `Element`, which is the root element of the parsed tree. Other parsing functions may create an `ElementTree`.
&emsp;&emsp;As an `Element`, root has a tag and a dictionary of attributes:

``` python
>>> root.tag
'data'
>>> root.attrib
{}
```

It also has children nodes over which we can iterate:

``` python
>>> for child in root:
...     print(child.tag, child.attrib)
...
country {'name': 'Liechtenstein'}
country {'name': 'Singapore'}
country {'name': 'Panama'}
```

Children are nested, and we can access specific child nodes by `index`:

``` python
>>> root[0][1].text
'2008'
```

&emsp;&emsp;**Note**: Not all elements of the `XML` input will end up as elements of the parsed tree. Currently, this module skips over any `XML` comments, processing instructions, and document type declarations in the input. Nevertheless, trees built using this module's `API` rather than parsing from `XML` text can have comments and processing instructions in them; they will be included when generating `XML` output. A document type declaration may be accessed by passing a custom `TreeBuilder` instance to the `XMLParser` constructor.

#### Pull API for non-blocking parsing

&emsp;&emsp;Most parsing functions provided by this module require the whole document to be read at once before returning any result. It is possible to use an `XMLParser` and feed data into it incrementally, but it is a push `API` that calls methods on a callback target, which is too `low-level` and inconvenient for most needs. Sometimes what the user really wants is to be able to parse `XML` incrementally, without blocking operations, while enjoying the convenience of fully constructed `Element` objects.
&emsp;&emsp;The most powerful tool for doing this is `XMLPullParser`. It does not require a blocking read to obtain the `XML` data, and is instead fed with data incrementally with `XMLPullParser.feed()` calls. To get the parsed `XML` elements, call `XMLPullParser.read_events()`. Here is an example:

``` python
>>> parser = ET.XMLPullParser(['start', 'end'])
>>> parser.feed('<mytag>sometext')
>>> list(parser.read_events())
[('start', <Element 'mytag' at 0x7fa66db2be58>)]
>>> parser.feed(' more text</mytag>')
>>> for event, elem in parser.read_events():
...     print(event)
...     print(elem.tag, 'text=', elem.text)
...
end
```

The obvious use case is applications that operate in a `non-blocking` fashion where the `XML` data is being received from a socket or read incrementally from some storage device. In such cases, `blocking` reads are unacceptable.
&emsp;&emsp;Because it's so flexible, `XMLPullParser` can be inconvenient to use for simpler `use-cases`. If you don't mind your application `blocking` on reading `XML` data but would still like to have incremental parsing capabilities, take a look at `iterparse()`. It can be useful when you're reading a large `XML` document and don't want to hold it wholly in memory.

#### Finding interesting elements

&emsp;&emsp;`Element` has some useful methods that help iterate recursively over all the `sub-tree` below it (its children, their children, and so on). For example, `Element.iter()`:

``` python
>>> for neighbor in root.iter('neighbor'):
...     print(neighbor.attrib)
...
{'name': 'Austria', 'direction': 'E'}
{'name': 'Switzerland', 'direction': 'W'}
{'name': 'Malaysia', 'direction': 'N'}
{'name': 'Costa Rica', 'direction': 'W'}
{'name': 'Colombia', 'direction': 'E'}
```

`Element.findall()` finds only elements with a tag which are direct children of the current element. `Element.find()` finds the first child with a particular tag, and `Element.text` accesses the element's text content. `Element.get()` accesses the element's attributes:

``` python
>>> for country in root.findall('country'):
...     rank = country.find('rank').text
...     name = country.get('name')
...     print(name, rank)
...
Liechtenstein 1
Singapore 4
Panama 68
```

More sophisticated specification of which elements to look for is possible by using `XPath`.

#### Modifying an XML File

&emsp;&emsp;`ElementTree` provides a simple way to build `XML` documents and write them to files. The `ElementTree.write()` method serves this purpose.
&emsp;&emsp;Once created, an `Element` object may be manipulated by directly changing its fields (such as `Element.text`), adding and modifying attributes (`Element.set()` method), as well as adding new children (for example with `Element.append()`).
&emsp;&emsp;Let's say we want to add one to each country's rank, and add an updated attribute to the rank element:

``` python
>>> for rank in root.iter('rank'):
...     new_rank = int(rank.text) + 1
...     rank.text = str(new_rank)
...     rank.set('updated', 'yes')
>>> tree.write('output.xml')
```

Our `XML` now looks like this:

``` xml
<?xml version="1.0"?>
<data>
    <country name="Liechtenstein">
        <rank updated="yes">2</rank>
        <year>2008</year>
        <gdppc>141100</gdppc>
        <neighbor name="Austria" direction="E"/>
        <neighbor name="Switzerland" direction="W"/>
    </country>
    <country name="Singapore">
        <rank updated="yes">5</rank>
        <year>2011</year>
        <gdppc>59900</gdppc>
        <neighbor name="Malaysia" direction="N"/>
    </country>
    <country name="Panama">
        <rank updated="yes">69</rank>
        <year>2011</year>
        <gdppc>13600</gdppc>
        <neighbor name="Costa Rica" direction="W"/>
        <neighbor name="Colombia" direction="E"/>
    </country>
</data>
```

We can remove elements using `Element.remove()`. Let's say we want to remove all countries with a rank higher than `50`:

``` python
>>> for country in root.findall('country'):
...     rank = int(country.find('rank').text)
...     if rank > 50:
...         root.remove(country)
>>> tree.write('output.xml')
```

Our `XML` now looks like this:

``` xml
<?xml version="1.0"?>
<data>
    <country name="Liechtenstein">
        <rank updated="yes">2</rank>
        <year>2008</year>
        <gdppc>141100</gdppc>
        <neighbor name="Austria" direction="E"/>
        <neighbor name="Switzerland" direction="W"/>
    </country>
    <country name="Singapore">
        <rank updated="yes">5</rank>
        <year>2011</year>
        <gdppc>59900</gdppc>
        <neighbor name="Malaysia" direction="N"/>
    </country>
</data>
```

#### Building XML documents

&emsp;&emsp;The `SubElement()` function also provides a convenient way to create new `sub-elements` for a given element:

``` python
>>> a = ET.Element('a')
>>> b = ET.SubElement(a, 'b')
>>> c = ET.SubElement(a, 'c')
>>> d = ET.SubElement(c, 'd')
>>> ET.dump(a)
<a><b /><c><d /></c></a>
```

#### Parsing XML with Namespaces

&emsp;&emsp;If the `XML` input has `namespaces`, `tags` and `attributes` with prefixes in the form `prefix:sometag` get expanded to `{uri}sometag` where the prefix is replaced by the full `URI`. Also, if there is a default namespace, that full `URI` gets prepended to all of the `non-prefixed` tags.
&emsp;&emsp;Here is an `XML` example that incorporates two namespaces, one with the prefix `fictional` and the other serving as the default namespace:

``` xml
<?xml version="1.0"?>
<actors xmlns:fictional="http://characters.example.com"
        xmlns="http://people.example.com">
    <actor>
        <name>John Cleese</name>
        <fictional:character>Lancelot</fictional:character>
        <fictional:character>Archie Leach</fictional:character>
    </actor>
    <actor>
        <name>Eric Idle</name>
        <fictional:character>Sir Robin</fictional:character>
        <fictional:character>Gunther</fictional:character>
        <fictional:character>Commander Clement</fictional:character>
    </actor>
</actors>
```

One way to search and explore this `XML` example is to manually add the `URI` to every tag or attribute in the xpath of a `find()` or `findall()`:

``` python
root = fromstring(xml_text)

for actor in root.findall('{http://people.example.com}actor'):
    name = actor.find('{http://people.example.com}name')
    print(name.text)

    for char in actor.findall('{http://characters.example.com}character'):
        print(' |-->', char.text)
```

A better way to search the namespaced `XML` example is to create a dictionary with your own prefixes and use those in the search functions:

``` python
ns = {'real_person': 'http://people.example.com', 'role': 'http://characters.example.com'}
​
for actor in root.findall('real_person:actor', ns):
    name = actor.find('real_person:name', ns)
    print(name.text)

    for char in actor.findall('role:character', ns):
        print(' |-->', char.text)
```

These two approaches both output:

``` python
John Cleese
 |--> Lancelot
 |--> Archie Leach
Eric Idle
 |--> Sir Robin
 |--> Gunther
 |--> Commander Clement
```

### XPath support

&emsp;&emsp;This module provides limited support for `XPath` expressions for locating elements in a tree. The goal is to support a small subset of the abbreviated syntax; a full `XPath` engine is outside the scope of the module.

#### Example

&emsp;&emsp;Here's an example that demonstrates some of the `XPath` capabilities of the module. We'll be using the countrydata `XML` document from the Parsing `XML` section:

``` python
import xml.etree.ElementTree as ET
​
root = ET.fromstring(countrydata)
root.findall(".")  # Top-level elements
# All 'neighbor' grand-children of 'country' children of the top-level elements
root.findall("./country/neighbor")
# Nodes with name='Singapore' that have a 'year' child
root.findall(".//year/..[@name='Singapore']")
# 'year' nodes that are children of nodes with name='Singapore'
root.findall(".//*[@name='Singapore']/year")
# All 'neighbor' nodes that are the second child of their parent
root.findall(".//neighbor[2]")
```

#### Supported XPath syntax

Syntax              | Meaning
--------------------|--------
`tag`               | Selects all child elements with the given `tag`. For example, `spam` selects all child elements named `spam`, and `spam/egg` selects all grandchildren named `egg` in all children named `spam`.
`*`                 | Selects all child elements. For example, `*/egg` selects all grandchildren named `egg`.
`.`                 | Selects the current node. This is mostly useful at the `beginning` of the path, to indicate that it's a relative path.
`//`                | Selects all subelements, on all levels beneath the current element. For example, `.//egg` selects all `egg` elements in the entire tree.
`..`                | Selects the parent element. Returns `None` if the path attempts to reach the ancestors of the `start` element (the element find was called on).
`[@attrib]`         | Selects all elements that have the given `attribute`.
`[@attrib='value']` | Selects all elements for which the given `attribute` has the given `value`. The value cannot contain quotes.
`[tag]`             | Selects all elements that have a child named `tag`. Only immediate children are supported.
`[tag='text']`      | Selects all elements that have a child named `tag` whose complete `text` content, including descendants, equals the given `text`.
`[position]`        | Selects all elements that are located at the given `position`. The `position` can be either an `integer` (`1` is the `first position`), the expression `last()` (for the `last position`), or a `position` relative to the `last position` (e.g. `last() - 1`).

Predicates (expressions within square brackets) must be preceded by a tag name, an asterisk, or another predicate. position predicates must be preceded by a tag name.

### Reference

#### Functions

&emsp;&emsp;`xml.etree.ElementTree.Comment(text=None)`: `Comment` `element` factory. This factory function creates a special `element` that will be serialized as an `XML` `comment` by the standard serializer. The `comment` string can be either a `bytestring` or a `Unicode string`. `text` is a string containing the `comment` string. Returns an `element` instance representing a `comment`.
&emsp;&emsp;Note that `XMLParser` skips over `comments` in the input instead of creating `comment` objects for them. An `ElementTree` will only contain `comment` nodes if they have been inserted into to the `tree` using one of the `Element` methods.
&emsp;&emsp;`xml.etree.ElementTree.dump(elem)`: Writes an `element` tree or `element` structure to `sys.stdout`. This function should be used for debugging only. The exact output format is implementation dependent. In this version, it's written as an ordinary `XML` file. `elem` is an `element` tree or an individual `element`.
&emsp;&emsp;`xml.etree.ElementTree.fromstring(text)`: Parses an `XML` section from a `string` constant. Same as `XML()`. `text` is a `string` containing `XML` data. Returns an `Element` instance.
&emsp;&emsp;`xml.etree.ElementTree.fromstringlist(sequence, parser=None)`: Parses an `XML` document from a `sequence` of `string` fragments. `sequence` is a list or other `sequence` containing `XML` data fragments. `parser` is an optional `parser` instance. If not given, the standard `XMLParser` `parser` is used. Returns an `Element` instance.
&emsp;&emsp;`xml.etree.ElementTree.iselement(element)`: Checks if an object appears to be a valid `element` object. `element` is an `element` instance. Returns a `true` value if this is an `element` object.
&emsp;&emsp;`xml.etree.ElementTree.iterparse(source, events=None, parser=None)`: Parses an `XML` section into an `element tree` incrementally, and reports what's going on to the user. `source` is a filename or file object containing `XML` data. `events` is a sequence of `events` to report back. The supported `events` are the strings `start`, `end`, `start-ns` and `end-ns` (the `ns` events are used to get detailed namespace information). If `events` is omitted, only `end events` are reported. `parser` is an optional `parser` instance. If not given, the standard `XMLParser` parser is used. `parser` must be a subclass of `XMLParser` and can only use the default `TreeBuilder` as a target. Returns an iterator providing `(event, elem)` pairs.
&emsp;&emsp;Note that while `iterparse()` builds the `tree` incrementally, it issues blocking reads on source (or the file it names). As such, it's unsuitable for applications where blocking reads can't be made. For fully `non-blocking` parsing, see `XMLPullParser`.
&emsp;&emsp;**Note**: `iterparse()` only guarantees that it has seen the `>` character of a starting tag when it emits a `start` event, so the attributes are defined, but the contents of the `text` and `tail` attributes are undefined at that point. The same applies to the `element` children; they may or may not be present. If you need a fully populated `element`, look for `end` events instead.
&emsp;&emsp;`xml.etree.ElementTree.parse(source, parser=None)`: Parses an `XML` section into an `element` tree. `source` is a filename or file object containing `XML` data. `parser` is an optional `parser` instance. If not given, the standard `XMLParser` parser is used. Returns an `ElementTree` instance.
&emsp;&emsp;`xml.etree.ElementTree.ProcessingInstruction(target, text=None)`: `PI` element factory. This factory function creates a special `element` that will be serialized as an `XML` processing instruction. `target` is a string containing the `PI` target. `text` is a string containing the `PI` contents, if given. Returns an `element` instance, representing a processing instruction.

&emsp;&emsp;Note that `XMLParser` skips over processing instructions in the input instead of creating comment objects for them. An `ElementTree` will only contain processing instruction nodes if they have been inserted into to the tree using one of the Element methods.
&emsp;&emsp;`xml.etree.ElementTree.register_namespace(prefix, uri)`: Registers a `namespace prefix`. The `registry` is global, and any existing mapping for either the given `prefix` or the `namespace URI` will be removed. `prefix` is a `namespace prefix`. `uri` is a `namespace uri`. Tags and attributes in this `namespace` will be serialized with the given `prefix`, if at all possible.
&emsp;&emsp;`xml.etree.ElementTree.SubElement(parent, tag, attrib={}, **extra)`: `Subelement` factory. This function creates an `element` instance, and appends it to an existing `element`.
&emsp;&emsp;The `element` name, attribute names, and attribute values can be either `bytestrings` or `Unicode strings`. `parent` is the `parent` element. `tag` is the subelement name. `attrib` is an optional dictionary, containing `element` attributes. `extra` contains additional attributes, given as keyword arguments. Returns an `element` instance.
&emsp;&emsp;`xml.etree.ElementTree.tostring(element, encoding="us-ascii", method="xml", *, short_empty_elements=True)`: Generates a `string` representation of an `XML` element, including all subelements. `element` is an `Element` instance. `encoding` is the output `encoding` (default is `US-ASCII`). Use `encoding="unicode"` to generate a `Unicode string` (otherwise, a `bytestring` is generated). `method` is either `xml`, `html` or `text` (default is `xml`). `short_empty_elements` has the same meaning as in `ElementTree.write()`. Returns an (optionally) `encoded string` containing the `XML` data.
&emsp;&emsp;`xml.etree.ElementTree.tostringlist(element, encoding="us-ascii", method="xml", *, short_empty_elements=True)`: Generates a `string` representation of an `XML` element, including all subelements. `element` is an `Element` instance. `encoding` is the output `encoding` (default is `US-ASCII`). Use `encoding="unicode"` to generate a `Unicode string` (otherwise, a `bytestring` is `generated`). `method` is either `xml`, `html` or `text` (default is `xml`). `short_empty_elements` has the same meaning as in `ElementTree.write()`. Returns a list of (optionally) `encoded strings` containing the `XML` data. It does not guarantee any specific sequence, except that `b"".join(tostringlist(element)) == tostring(element)`.
&emsp;&emsp;`xml.etree.ElementTree.XML(text, parser=None)`: Parses an `XML` section from a string constant. This function can be used to embed `XML literals` in Python code. `text` is a string containing `XML` data. `parser` is an optional `parser` instance. If not given, the standard `XMLParser` parser is used. Returns an `Element` instance.
&emsp;&emsp;`xml.etree.ElementTree.XMLID(text, parser=None)`: Parses an `XML` section from a string constant, and also returns a `dictionary` which maps from element `id:s` to elements. `text` is a string containing `XML` data. `parser` is an optional `parser` instance. If not given, the standard `XMLParser` parser is used. Returns a `tuple` containing an `Element` instance and a `dictionary`.

### Element Objects

#### class xml.etree.ElementTree.Element(tag, attrib={}, **extra)

&emsp;&emsp;Element `class`. This `class` defines the `Element` interface, and provides a reference implementation of this interface.
&emsp;&emsp;The `element name`, `attribute names`, and `attribute values` can be either `bytestrings` or `Unicode strings`. `tag` is the element name. `attrib` is an optional `dictionary`, containing `element` attributes. `extra` contains additional attributes, given as keyword arguments.
&emsp;&emsp;`tag`: A `string` identifying what kind of data this `element` represents (the `element` type, in other words).
&emsp;&emsp;`tail`: These attributes can be used to hold additional data associated with the `element`. Their values are usually strings but may be any `application-specific` object. If the `element` is created from an `XML` file, the text attribute holds either the text between the element's `start tag` and its first child or `end tag`, or `None`, and the `tail` attribute holds either the text between the element's `end tag` and the `next tag`, or `None`. For the `XML` data

``` xml
<a><b>1<c>2<d/>3</c></b>4</a>
```

the `a` element has `None` for both text and tail attributes, the `b` element has text `1` and tail `4`, the `c` element has text `2` and tail `None`, and the `d` element has text `None` and tail `3`.
&emsp;&emsp;To collect the inner text of an element, see `itertext()`, for example `"".join(element.itertext())`.
&emsp;&emsp;`attrib`: A `dictionary` containing the element's attributes. Note that while the `attrib` value is always a real mutable `Python dictionary`, an `ElementTree` implementation may choose to use another internal representation, and create the `dictionary` only if someone asks for it. To take advantage of such implementations, use the `dictionary` methods below whenever possible.
&emsp;&emsp;The following `dictionary-like` methods work on the element attributes.
&emsp;&emsp;`clear()`: Resets an `element`. This function removes all subelements, `clears` all attributes, and sets the text and tail attributes to `None`.
&emsp;&emsp;`get(key, default=None)`: Gets the `element` attribute named `key`. Returns the attribute value, or default if the attribute was not found.
&emsp;&emsp;`items()`: Returns the `element` attributes as a sequence of `(name, value)` pairs. The attributes are returned in an arbitrary order.
&emsp;&emsp;`keys()`: Returns the `elements` attribute names as a list. The names are returned in an arbitrary order.
&emsp;&emsp;`set(key, value)`: Set the attribute `key` on the element to `value`.
&emsp;&emsp;The following methods work on the element's children (`subelements`).
&emsp;&emsp;`append(subelement)`: Adds the element `subelement` to the end of this element's internal list of `subelements`. Raises `TypeError` if `subelement` is not an `Element`.
&emsp;&emsp;`extend(subelements)`: Appends `subelements` from a sequence object with zero or more `elements`. Raises `TypeError` if a `subelement` is not an `Element`.
&emsp;&emsp;`find(match, namespaces=None)`: Finds the first subelement matching `match`. `match` may be a tag name or a path. Returns an `element` instance or `None`. `namespaces` is an optional mapping from `namespace prefix` to full name.
&emsp;&emsp;`findall(match, namespaces=None)`: Finds all matching subelements, by tag name or path. Returns a list containing all matching elements in document order. `namespaces` is an optional mapping from `namespace prefix` to full name.
&emsp;&emsp;`findtext(match, default=None, namespaces=None)`: Finds text for the first subelement matching `match`. `match` may be a tag name or a path. Returns the `text` content of the first matching `element`, or `default` if no `element` was found. Note that if the matching `element` has no `text` content an empty string is returned. `namespaces` is an optional mapping from `namespace prefix` to full name.
&emsp;&emsp;`getchildren()`: Deprecated since version `3.2`: Use `list(elem)` or `iteration`.
&emsp;&emsp;`getiterator(tag=None)`: Deprecated since version `3.2`: Use method `Element.iter()` instead.
&emsp;&emsp;`insert(index, subelement)`: Inserts `subelement` at the given position in this `element`. Raises `TypeError` if `subelement` is not an `Element`.
&emsp;&emsp;`iter(tag=None)`: Creates a tree `iterator` with the current `element` as the `root`. The `iterator` iterates over this `element` and all `elements` below it, in document `(depth first)` order. If `tag` is not `None` or `*`, only `elements` whose `tag` equals `tag` are returned from the `iterator`. If the tree structure is modified during iteration, the result is undefined.
&emsp;&emsp;`iterfind(match, namespaces=None)`: Finds all matching subelements, by tag name or path. Returns an iterable yielding all matching `elements` in document order. `namespaces` is an optional mapping from `namespace prefix` to full name.
&emsp;&emsp;`itertext()`: Creates a text `iterator`. The `iterator` loops over this `element` and all subelements, in document order, and returns all inner `text`.
&emsp;&emsp;`makeelement(tag, attrib)`: Creates a new `element` object of the same type as this `element`. Do not call this method, use the `SubElement()` factory function instead.
&emsp;&emsp;`remove(subelement)`: Removes `subelement` from the `element`. Unlike the `find*` methods, this method compares elements based on the instance identity, not on tag value or contents.
&emsp;&emsp;Element objects also support the following sequence type methods for working with subelements: `__delitem__()`, `__getitem__()`, `__setitem__()`, `__len__()`.
&emsp;&emsp;**Caution**: Elements with no subelements will test as `False`. This behavior will change in future versions. Use specific `len(elem)` or elem is `None` test instead.

``` python
element = root.find('foo')
​
if not element:  # careful!
    print("element not found, or element has no subelements")
​
if element is None:
    print("element not found")
```

### ElementTree Objects

#### class xml.etree.ElementTree.ElementTree(element=None, file=None)

&emsp;&emsp;`ElementTree` wrapper class. This class represents an entire `element` hierarchy, and adds some extra support for serialization to and from standard `XML`.
&emsp;&emsp;`element` is the `root element`. The `tree` is initialized with the contents of the `XML` file if given.
&emsp;&emsp;`_setroot(element)`: Replaces the `root element` for this tree. This discards the current contents of the tree, and replaces it with the given `element`. Use with care. `element` is an `element` instance.
&emsp;&emsp;`find(match, namespaces=None)`: Same as `Element.find()`, starting at the root of the tree.
&emsp;&emsp;`findall(match, namespaces=None)`: Same as `Element.findall()`, starting at the root of the tree.
&emsp;&emsp;`findtext(match, default=None, namespaces=None)`: Same as `Element.findtext()`, starting at the root of the tree.
&emsp;&emsp;`getiterator(tag=None)`: Deprecated since version `3.2`: Use method `ElementTree.iter()` instead.
&emsp;&emsp;`getroot()`: Returns the `root element` for this tree.
&emsp;&emsp;`iter(tag=None)`: Creates and returns a tree `iterator` for the `root element`. The `iterator` loops over all elements in this tree, in section order. `tag` is the `tag` to look for (default is to return all elements).
&emsp;&emsp;`iterfind(match, namespaces=None)`: Same as `Element.iterfind()`, starting at the root of the tree.
&emsp;&emsp;`parse(source, parser=None)`: Loads an external `XML` section into this `element` tree. `source` is a file name or file object. `parser` is an optional `parser` instance. If not given, the standard `XMLParser` parser is used. Returns the section `root element`.
&emsp;&emsp;`write(file, encoding="us-ascii", xml_declaration=None, default_namespace=None, method="xml", *, short_empty_elements=True)`: Writes the element tree to a file, as `XML`. `file` is a `file name`, or a `file object` opened for writing. `encoding` is the output `encoding` (default is `US-ASCII`). `xml_declaration` controls if an `XML` declaration should be added to the `file`. Use False for never, `True` for always, `None` for only if not `US-ASCII` or `UTF-8` or `Unicode` (default is `None`). `default_namespace` sets the default `XML` namespace (for `xmlns`). `method` is either `xml`, `html` or `text` (default is `xml`). The `keyword-only` `short_empty_elements` parameter controls the formatting of elements that contain no content. If `True` (the default), they are emitted as a single `self-closed` tag, otherwise they are emitted as a pair of start/end tags.
&emsp;&emsp;The output is either a `string` (`str`) or `binary` (`bytes`). This is controlled by the encoding argument. If encoding is `unicode`, the output is a `string`; otherwise, it’s `binary`. Note that this may conflict with the type of file if it’s an open file object; make sure you do not try to write a `string` to a `binary` stream and vice versa.
&emsp;&emsp;This is the `XML` file that is going to be manipulated:

``` xml
<html>
    <head>
        <title>Example page</title>
    </head>
    <body>
        <p>Moved to <a href="http://example.org/">example.org</a>
        or <a href="http://example.com/">example.com</a>.</p>
    </body>
</html>
```

Example of changing the attribute `target` of every link in first paragraph:

``` python
>>> from xml.etree.ElementTree import ElementTree
>>> tree = ElementTree()
>>> tree.parse("index.xhtml")
<Element 'html' at 0xb77e6fac>
>>> p = tree.find("body/p")  # Finds first occurrence of tag p in body
>>> p
<Element 'p' at 0xb77ec26c>
>>> links = list(p.iter("a"))  # Returns list of all links
>>> links
[<Element 'a' at 0xb77ec2ac>, <Element 'a' at 0xb77ec1cc>]
>>> for i in links:  # Iterates through all found links
...     i.attrib["target"] = "blank"
>>> tree.write("output.xhtml")
```

### QName Objects

#### class xml.etree.ElementTree.QName(text_or_uri, tag=None)

&emsp;&emsp;`QName` wrapper. This can be used to wrap a `QName` attribute value, in order to get proper namespace handling on output. `text_or_uri` is a string containing the `QName` value, in the form `{uri}local`, or, if the `tag` argument is given, the `URI` part of a `QName`. If `tag` is given, the first argument is interpreted as a `URI`, and this argument is interpreted as a local name. `QName` instances are opaque.

### TreeBuilder Objects

#### class xml.etree.ElementTree.TreeBuilder(element_factory=None)

&emsp;&emsp;Generic `element` structure `builder`. This builder converts a sequence of start, data, and end method calls to a `well-formed` element structure. You can use this class to build an `element` structure using a custom `XML` parser, or a parser for some other `XML-like` format. `element_factory`, when given, must be a callable accepting two positional arguments: a `tag` and a `dict` of attributes. It is expected to return a new `element` instance.
&emsp;&emsp;`close()`: Flushes the builder buffers, and returns the toplevel document `element`. Returns an `Element` instance.
&emsp;&emsp;`data(data)`: Adds text to the current `element`. `data` is a string. This should be either a `bytestring`, or a `Unicode` string.
&emsp;&emsp;`end(tag)`: Closes the current `element`. `tag` is the `element` name. Returns the closed element.
&emsp;&emsp;`start(tag, attrs)`: Opens a new `element`. `tag` is the `element` name. `attrs` is a dictionary containing `element` attributes. Returns the opened `element`.
&emsp;&emsp;In addition, a custom `TreeBuilder` object can provide the following method:
&emsp;&emsp;`doctype(name, pubid, system)`: Handles a `doctype` declaration. `name` is the `doctype` name. `pubid` is the `public` identifier. `system` is the `system` identifier. This method does not exist on the default `TreeBuilder` class.

### XMLParser Objects

#### class xml.etree.ElementTree.XMLParser(html=0, target=None, encoding=None)

&emsp;&emsp;This class is the `low-level` building block of the module. It uses `xml.parsers.expat` for efficient, `event-based` parsing of `XML`. It can be fed `XML` data incrementally with the `feed()` method, and parsing events are translated to a push `API` - by invoking callbacks on the `target` object. If `target` is omitted, the standard `TreeBuilder` is used. The html argument was historically used for backwards compatibility and is now deprecated. If `encoding` is given, the value overrides the `encoding` specified in the `XML` file.
&emsp;&emsp;Deprecated since version `3.4`: The html argument. The remaining arguments should be passed via keyword to prepare for the removal of the html argument.
&emsp;&emsp;`close()`: Finishes feeding data to the `parser`. Returns the result of calling the `close()` method of the `target` passed during construction; by default, this is the toplevel document element.
&emsp;&emsp;`doctype(name, pubid, system)`: Deprecated since version `3.2`: Define the `TreeBuilder.doctype()` method on a custom `TreeBuilder` target.
&emsp;&emsp;`feed(data)`: Feeds `data` to the `parser`. `data` is encoded `data`.
&emsp;&emsp;`XMLParser.feed()` calls target's `start(tag, attrs_dict)` method for each opening tag, its `end(tag)` method for each closing tag, and data is processed by method `data(data)`. `XMLParser.close()` calls target's method `close()`. `XMLParser` can be used not only for building a tree structure. This is an example of counting the maximum depth of an `XML` file:

``` python
>>> from xml.etree.ElementTree import XMLParser
>>> class MaxDepth:  # The target object of the parser
...     maxDepth = 0
...     depth = 0
...     def start(self, tag, attrib):  # Called for each opening tag.
...         self.depth += 1
...         if self.depth > self.maxDepth:
...             self.maxDepth = self.depth
...     def end(self, tag):  # Called for each closing tag.
...         self.depth -= 1
...     def data(self, data):
...         pass  # We do not need to do anything with data.
...     def close(self):  # Called when all data has been parsed.
...         return self.maxDepth
...
>>> target = MaxDepth()
>>> parser = XMLParser(target=target)
>>> exampleXml = """
... <a>
...   <b>
...   </b>
...   <b>
...     <c>
...       <d>
...       </d>
...     </c>
...   </b>
... </a>"""
>>> parser.feed(exampleXml)
>>> parser.close()
4
```

### XMLPullParser Objects

#### class xml.etree.ElementTree.XMLPullParser(events=None)

&emsp;&emsp;A `pull parser` suitable for `non-blocking` applications. Its `input-side API` is similar to that of `XMLParser`, but instead of pushing calls to a callback target, `XMLPullParser` collects an internal list of parsing `events` and lets the user read from it. `events` is a sequence of `events` to report back. The supported `events` are the strings `start`, `end`, `start-ns` and `end-ns` (the `ns` events are used to get detailed namespace information). If `events` is omitted, only `end` events are reported.
&emsp;&emsp;`feed(data)`: Feed the given `bytes data` to the `parser`.
&emsp;&emsp;`close()`: Signal the `parser` that the data stream is terminated. Unlike `XMLParser.close()`, this method always returns `None`. Any events not yet retrieved when the `parser` is closed can still be read with `read_events()`.
&emsp;&emsp;`read_events()`: Return an iterator over the `events` which have been encountered in the data fed to the `parser`. The iterator yields `(event, elem)` pairs, where `event` is a string representing the type of `event` (e.g. `end`) and `elem` is the encountered `Element` object.
&emsp;&emsp;`Events` provided in a previous call to `read_events()` will not be yielded again. `Events` are consumed from the internal queue only when they are retrieved from the iterator, so multiple readers iterating in parallel over iterators obtained from `read_events()` will have unpredictable results.
&emsp;&emsp;**Note**: `XMLPullParser` only guarantees that it has seen the `>` character of a starting tag when it emits a `start` event, so the attributes are defined, but the contents of the text and tail attributes are undefined at that point. The same applies to the `element` children; they may or may not be present. If you need a fully populated `element`, look for `end` events instead.

### Exceptions

#### class xml.etree.ElementTree.ParseError

&emsp;&emsp;`XML` parse error, raised by the various parsing methods in this module when parsing fails. The string representation of an instance of this exception will contain a `user-friendly` error message. In addition, it will have the following `attributes` available:
&emsp;&emsp;`code`: A numeric error `code` from the expat `parser`. See the documentation of `xml.parsers.expat` for the list of error codes and their meanings.
&emsp;&emsp;`position`: A tuple of line, column numbers, specifying where the error occurred.