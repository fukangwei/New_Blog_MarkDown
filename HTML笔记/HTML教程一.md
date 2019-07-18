---
title: HTML教程一
date: 2019-07-15 21:57:22
tags:
---
### HTML教程(HTML5标准)

&emsp;&emsp;超文本标记语言(`HyperText Markup Language`，简称`HTML`)是一种用于创建网页的标准标记语言，您可以使用`HTML`来建立自己的`WEB`站点。`HTML`运行在浏览器上，由浏览器来解析。
&emsp;&emsp;`HTML`的实例如下，对于中文网页，需要使用`<meta charset="utf-8">`声明编码，否则会出现乱码。

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>菜鸟教程(runoob.com)</title>
    </head>
    <body>
        <h1>我的第一个标题</h1>
        <p>我的第一个段落。</p>
    </body>
</html>
```

`HTML`文档的后缀名为`.html`或`.htm`，这两种后缀名没有区别，都可以使用。

### HTML简介

&emsp;&emsp;实例解析如下：`<!DOCTYPE html>`声明为`HTML5`文档，`<html>`元素是`HTML`页面的根元素，`<head>`元素包含了文档的元数据，`<title>`元素描述了文档的标题，`<body>`元素包含了可见的页面内容，`<h1>`元素定义一个大标题，`<p>`元素定义一个段落。

#### 什么是HTML？

&emsp;&emsp;`HTML`是用来描述网页的一种语言，它不是一种编程语言，而是一种标记语言，标记语言是一套标记标签(`markup tag`)。`HTML`使用标记标签来描述网页，其文档包含了`HTML`标签及文本内容，也叫做`Web`页面。`HTML`标记标签通常被称为`HTML`标签(`HTML tag`)，标签是由尖括号包围的关键词(比如`<html>`)，标签通常是成对出现的(比如`<b>`和`</b>`)。标签对中的第一个标签是开始标签，第二个标签是结束标签，开始和结束标签也被称为`开放标签`和`闭合标签`：

``` xml
<标签>内容</标签>
```

#### HTML网页结构

&emsp;&emsp;下面是一个可视化的`HTML`页面结构：

<img src="./HTML教程一/1.png" width="80%">

只有`<body>`区域(白色部分)才会在浏览器中显示。

&emsp;&emsp;“<!DOCTYPE>”声明
&emsp;&emsp;`<!DOCTYPE>`声明有助于浏览器中正确显示网页。网络上有很多不同的文件，如果能够正确声明HTML的版本，浏览器就能正确显示网页内容。`doctype`声明是不区分大小写的，以下方式均可：

``` xml
<!DOCTYPE html>
<!DOCTYPE HTML>
<!doctype html>
<!Doctype Html>
```

#### HTML基础(4个实例)

&emsp;&emsp;`HTML`标题：`HTML`标题(`Heading`)是通过`<h1>`至`<h6>`标签来定义的：

``` xml
<h1>这是一个标题</h1>
<h2>这是一个标题</h2>
<h3>这是一个标题</h3>
```

&emsp;&emsp;`HTML`段落：`HTML`段落是通过标签`<p>`来定义的：

``` xml
<p>这是一个段落。</p>
<p>这是另外一个段落。</p>
```

&emsp;&emsp;`HTML`链接：`HTML`链接是通过标签`<a>`来定义的：

``` xml
<a href="http://www.runoob.com">这是一个链接</a>
```

注意在`href`属性中指定链接的地址。
&emsp;&emsp;`HTML`图像：`HTML`图像是通过标签`<img>`来定义的：

``` xml
<img src="/images/logo.png" width="258" height="39" />
```

注意，图像的名称和尺寸是以属性的形式提供的。

### HTML元素

&emsp;&emsp;`HTML`文档由`HTML`元素定义，`HTML`元素如下：

开始标签                  | 元素内容    | 结束标签
-------------------------|-------------|--------
`<p>`                    | 这是一个段落 | `</p>`
`<a href="default.htm">` | 这是一个链接 | `</a>`
`<br>`                   |

开始标签常被称为起始标签(`opening tag`)，结束标签常称为闭合标签(`closing tag`)。
&emsp;&emsp;`HTML`元素以开始标签起始，以结束标签终止，元素的内容是开始标签与结束标签之间的内容。某些`HTML`元素具有空内容(`empty content`)，空元素在开始标签中进行关闭(以开始标签的结束而结束)。大多数`HTML`元素可拥有属性。
&emsp;&emsp;`HTML`文档由嵌套的`HTML`元素构成：

``` xml
<!DOCTYPE html>
<html>
    <body>
        <p>这是第一个段落。</p>
    </body>
</html>
```

&emsp;&emsp;以上实例包含了三个`HTML`元素：

- `<p>`元素：这个`<p>`元素定义了`HTML`文档中的一个段落，这个元素拥有一个开始标签`<p>`以及一个结束标签`</p>`，元素内容是`这是第一个段落`。
- `<body>`元素：`<body>`元素定义了`HTML`文档的主体，这个元素拥有一个开始标签`<body>`以及一个结束标签`</body>`，元素内容是另一个`HTML`元素，即`<p>`元素。
- `<html>`元素：`<html>`元素定义了整个`HTML`文档，这个元素拥有一个开始标签`<html>`以及一个结束标签`</html>`，元素内容是另一个`HTML`元素，即`<body>`元素。

&emsp;&emsp;不要忘记结束标签，即使您忘记了使用结束标签，大多数浏览器也会正确地显示`HTML`，但不要依赖这种做法，忘记使用结束标签会产生不可预料的结果或错误。
&emsp;&emsp;没有内容的`HTML`元素被称为空元素，空元素是在开始标签中关闭的。`<br>`就是没有关闭标签的空元素，该标签定义换行。在`XHTML`、`XML`以及未来版本的`HTML`中，所有元素都必须被关闭。在开始标签中添加斜杠，比如`<br />`，是关闭空元素的正确方法，`HTML`、`XHTML`和`XML`都接受这种方式。即使`<br>`在所有浏览器中都是有效的，但使用`<br />`其实是更长远的保障。
&emsp;&emsp;`HTML`最好使用小写标签，实际上标签对大小写不敏感，例如`<P>`等同于`<p>`，许多网站都使用大写的`HTML`标签。本教程使用的是小写标签，因为万维网联盟(`W3C`)在`HTML4`中推荐使用小写，而在未来`(X)HTML`版本中强制使用小写。

### HTML属性

&emsp;&emsp;属性是`HTML`元素提供的附加信息。`HTML`元素可以设置属性，它可以在元素中添加附加信息，一般描述于开始标签，总是以`名称/值`对的形式出现，比如`name="value"`。
&emsp;&emsp;`HTML`链接由`<a>`标签定义，链接的地址在`href`属性中指定：

``` xml
<a href="http://www.runoob.com">这是一个链接</a>
```

属性值应该始终被包括在引号内，双引号是最常用的，不过使用单引号也没有问题。注意，在某些个别的情况下，比如属性值本身就含有双引号，那么您必须使用单引号，例如`name='John "ShotGun" Nelson'`。
&emsp;&emsp;下面列出了适用于大多数`HTML`元素的属性：

属性    | 描述
--------|------
`class` | 为`html`元素定义一个或多个类名(`classname`)(类名从样式文件引入)
`id`    | 定义元素的唯一`id`
`style` | 规定元素的行内样式(`inline style`)
`title` | 描述了元素的额外信息(作为工具条使用)

### HTML标题

&emsp;&emsp;在HTML文档中，标题很重要。标题(`Heading`)是通过`<h1>`至`<h6>`标签进行定义的，`<h1>`定义最大的标题，`<h6>`定义最小的标题。注意，浏览器会自动地在标题的前后添加空行。
&emsp;&emsp;请确保将`HTML`标题标签只用于标题，不要仅仅是为了生成粗体或大号的文本而使用标题。搜索引擎使用标题为您的网页的结构和内容编制索引，因为用户可以通过标题来快速浏览您的网页，所以用标题来呈现文档结构是很重要的。应该将`h1`用作主标题，其后是`h2`，再其次是`h3`，以此类推。
&emsp;&emsp;`<hr>`标签在`HTML`页面中创建水平线，该元素可用于分隔内容：

``` xml
<p>这是一个段落。</p>
<hr>
<p>这是一个段落。</p>
<hr>
<p>这是一个段落。</p>
```

&emsp;&emsp;可以将注释插入`HTML`代码中，这样可以提高其可读性，使代码更易被人理解。浏览器会忽略注释，也不会显示它们。注释写法如下：

``` xml
<!-- 这是一个注释 -->
```

开始括号之后(左边的括号)需要紧跟一个叹号，结束括号之前(右边的括号)不需要，合理地使用注释可以对未来的代码编辑工作产生帮助。
&emsp;&emsp;标题大小与字体大小的关系：`1`到`6`号标题与`1`到`6`号字体逆序对应，比如`1`号字体对应`6`号标题，`2`号字体对应`5`号标题：

``` xml
<h1>这是1号标题</h1>
<font size="6">这是6号字体文本</font>
<h2>这是2号标题</h2>
<font size="5">这是5号字体文本</font>

<h3>这是3号标题</h3>
<font size="4">这是4号字体文本</font>

<h4>这是4号标题</h4>
<font size="3">这是3号字体文本</font>

<h5>这是5号标题</h5>
<font size="2">这是2号字体文本</font>

<h6>这是6号标题</h6>
<font size="1">这是1号字体文本</font>
```

<img src="./HTML教程一/2.png" width="30%">

### HTML段落

&emsp;&emsp;`HTML`段落：`HTML`可以将文档分割为若干段落，段落是通过`<p>`标签定义的：

``` xml
<p>这是一个段落</p>
<p>这是另一个段落</p>
```

注意，浏览器会自动地在段落的前后添加空行(`</p>`是块级元素)。
&emsp;&emsp;不要忘记结束标签，即使忘了使用结束标签，大多数浏览器也会正确地将`HTML`显示出来，但不要依赖这种做法，忘记使用结束标签会产生意想不到的结果和错误。在未来的`HTML`版本中，不允许省略结束标签。
&emsp;&emsp;`HTML`折行：如果您希望在不产生一个新段落的情况下进行换行(新行)，请使用`<br/>`标签：

``` xml
<p>这个<br>段落<br>演示了分行的效果</p>
```

<img src="./HTML教程一/3.png" width="30%">

`<br/>`元素是一个空的`HTML`元素。由于关闭标签没有任何意义，因此它没有结束标签。

### HTML文本格式化

&emsp;&emsp;`HTML`文本格式化效果如下：

<img src="./HTML教程一/4.png" width="20%">

&emsp;&emsp;`HTML`使用标签`<b>`与`<i>`对输出的文本进行格式，这些`HTML`标签被称为`格式化标签`。通常标签`<strong>`替换加粗标签`<b>`来使用，`<em>`替换`<i>`标签使用，然而这些标签的含义是不同的：`<b>`与`<i>`定义粗体或斜体文本，`<strong>`或者`<em>`意味着你要呈现的文本是重要的，所以要突出显示。
&emsp;&emsp;下例演示如何在一个`HTML`文件中对文本进行格式化：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <b>这个文本是加粗的</b>
        <br />
        <strong>这个文本是加粗的</strong>
        <br />
        <big>这个文本字体放大</big>
        <br />
        <em>这个文本是斜体的</em>
        <br />
        <i>这个文本是斜体的</i>
        <br />
        <small>这个文本是缩小的</small>
        <br />
        这个文本包含
        <sub>下标</sub>
        <br />
        这个文本包含
        <sup>上标</sup>
    </body>
</html>
```

<img src="./HTML教程一/5.png" width="20%">

&emsp;&emsp;下例演示如何使用`pre`标签对空行和空格进行控制：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <pre>
        此例演示如何使用 pre 标签
        对空行和    空格
        进行控制
        </pre>
    </body>
</html>
```

<img src="./HTML教程一/6.png" width="30%">

&emsp;&emsp;下例演示不同的`计算机输出`标签的显示效果：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <code>计算机输出</code>
        <br />
        <kbd>键盘输入</kbd>
        <br />
        <tt>打字机文本</tt>
        <br />
        <samp>计算机代码样本</samp>
        <br />
        <var>计算机变量</var>
        <br />
        <p>
            <b>注释：</b>这些标签常用于显示计算机/编程代码。
        </p>
    </body>
</html>
```

<img src="./HTML教程一/7.png" width="50%">

&emsp;&emsp;下例演示如何在`HTML`文件中写地址：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <address>
        Written by <a href="mailto:webmaster@example.com">Jon Doe</a>.<br>
        Visit us at:<br>
        Example.com<br>
        Box 564, Disneyland<br>
        USA
        </address>
    </body>
</html>
```

<img src="./HTML教程一/8.png" width="40%">

&emsp;&emsp;下例演示如何实现缩写或首字母缩写：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <abbr title="etcetera">etc.</abbr>
        <br />
        <acronym title="World Wide Web">WWW</acronym>
        <p>在某些浏览器中，当您把鼠标移至缩略词语上时，title 可用于展示表达的完整版本。</p>
        <p>仅对于 IE 5 中的 acronym 元素有效。</p>
        <p>对于 Netscape 6.2 中的 abbr 和 acronym 元素都有效。</p>
    </body>
</html>
```

<img src="./HTML教程一/9.png" width="100%">

&emsp;&emsp;下例演示如何改变文字的方向：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p>该段落文字从左到右显示。</p>
        <p><bdo dir="rtl">该段落文字从右到左显示。</bdo></p>
    </body>
</html>
```

<img src="./HTML教程一/10.png" width="40%">

&emsp;&emsp;下例演示如何实现长短不一的引用语：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p>WWF's goal is to:
        <q>Build a future where people live in harmony with nature.</q>
        We hope they succeed.</p>
    </body>
</html>
```

&emsp;&emsp;此例演示如何标记删除文本和插入文本：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p>My favorite color is <del>blue</del> <ins>red</ins>!</p>
    </body>
</html>
```

&emsp;&emsp;`HTML`文本格式化标签如下：

标签       | 描述
-----------|-----
`<b>`      | 定义粗体文本
`<em>`     | 定义着重文字
`<i>`      | 定义斜体字
`<small>`  | 定义小号字
`<strong>` | 定义加重语气
`<sub>`    | 定义下标字
`<sup>`    | 定义上标字
`<ins>`    | 定义插入字
`<del>`    | 定义删除字

&emsp;&emsp;`HTML`的计算机输出标签如下：

标签     | 描述
---------|-----
`<code>` | 定义计算机代码
`<kbd>`  | 定义键盘码
`<samp>` | 定义计算机代码样本
`<var>`  | 定义变量
`<pre>`  | 定义预格式文本

&emsp;&emsp;`HTML`引文、引用以及标签定义如下：

标签           | 描述
---------------|-----
`<abbr>`       | 定义缩写
`<address>`    | 定义地址
`<bdo>`        | 定义文字方向
`<blockquote>` | 定义长的引用
`<q>`          | 定义短的引用语
`<cite>`       | 定义引用、引证
`<dfn>`        | 定义一个定义项目

### HTML链接

&emsp;&emsp;`HTML`使用超级链接与网络上的另一个文档相连。几乎可以在所有的网页中找到链接，点击链接可以从一张页面跳转到另一张页面：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p>
        <a href="/index.html">本文本</a> 是一个指向本网站中的一个页面的链接。</p>
        <p><a href="http://www.microsoft.com/">本文本</a> 是一个指向万维网上的页面的链接。</p>
    </body>
</html>
```

&emsp;&emsp;`HTML`使用标签`<a>`来设置超文本链接，超链接可以是一个字、一个词或者一组词，也可以是一幅图像，您可以点击这些内容来跳转到新的文档或者当前文档中的某个部分。当您把鼠标指针移动到网页中的某个链接上时，箭头会变为一只小手。在标签`<a>`中使用了`href`属性来描述链接的地址。
&emsp;&emsp;默认情况下，链接将以以下形式出现在浏览器中：一个未访问过的链接显示为蓝色字体并带有下划线；访问过的链接显示为紫色并带有下划线；点击链接时，链接显示为红色并带有下划线。注意，如果为这些超链接设置了`CSS`样式，展示样式会根据`CSS`的设定而显示。
&emsp;&emsp;链接的`HTML`代码很简单：

``` xml
<a href="url">链接文本</a>
```

`href`属性描述了链接的目标。

#### 链接的target属性

&emsp;&emsp;你可以定义被链接的文档在何处显示。下面的这行会在新窗口打开文档：

``` xml
<a href="http://www.runoob.com/" target="_blank">访问菜鸟教程!</a>
```

#### 链接的id属性

&emsp;&emsp;`id`属性可用于创建在一个`HTML`文档书签标记。书签是不以任何特殊的方式显示，在`HTML`文档中是不显示的，所以对于读者来说是隐藏的。在`HTML`文档中插入`ID`：

``` xml
<a id="tips">有用的提示部分</a>
```

在`HTML`文档中创建一个链接到`有用的提示部分(id="tips")`：

``` xml
<a href="#tips">访问有用的提示部分</a>
```

或者从另一个页面创建一个链接到`有用的提示部分(id="tips")`：

``` xml
<a href="http://www.runoob.com/html/html-links.html#tips">
访问有用的提示部分</a>
```

&emsp;&emsp;注意，请始终将正斜杠添加到子文件夹。假如这样书写链接`href="http://www.runoob.com/html"`，就会向服务器产生两次`HTTP`请求，这是因为服务器会添加正斜杠到这个地址，然后创建一个新的请求，就像这样`href="http://www.runoob.com/html/"`。
&emsp;&emsp;下例演示如何使用图片链接：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p>创建图片链接:
        <a href="http://www.runoob.com/html/html-tutorial.html">
        <img src="smiley.gif" alt="HTML 教程" width="32" height="32"></a></p>

        <p>无边框的图片链接:
        <a href="http://www.runoob.com/html/html-tutorial.html">
        <img border="0" src="smiley.gif" alt="HTML 教程" width="32" height="32"></a></p>
    </body>
</html>
```

&emsp;&emsp;下例演示如何使用书签：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p>
        <a href="#C4">查看章节 4</a>
        </p>
        <h2>章节 1</h2>
        <p>这边显示该章节的内容……</p>
        <h2>章节 2</h2>
        <p>这边显示该章节的内容……</p>
        <h2>章节 3</h2>
        <p>这边显示该章节的内容……</p>
        <h2><a id="C4">章节 4</a></h2>
        <p>这边显示该章节的内容……</p>
    </body>
</html>
```

&emsp;&emsp;下例演示如何跳出框架，假如你的页面被固定在框架之内：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p>跳出框架?</p>
        <a href="http://www.runoob.com/" target="_top">点击这里!</a>
    </body>
</html>
```

&emsp;&emsp;下例演示如何链接到一个邮件，本例在安装邮件客户端程序后才能工作：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p>
        这是一个电子邮件链接：
        <a href="mailto:someone@example.com?Subject=Hello%20again" target="_top">
        发送邮件</a>
        </p>
        <p>
        <b>注意:</b> 单词之间空格使用 %20 代替，以确保浏览器可以正常显示文本。
        </p>
    </body>
</html>
```

### HTML头部

&emsp;&emsp;`<title>`定义了`HTML`文档的标题：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>我的 HTML 的第一页</title>
    </head>
    <body>
        <p>浏览器中包含body元素的内容。</p>
        <p>浏览器的标题包含title元素的内容</p>
    </body>
</html>
```

`<base>`定义页面中所有链接默认的链接目标地址：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>菜鸟教程(runoob.com)</title>
        <base href="http://www.runoob.com/images/" target="_blank">
    </head>

    <body>
        <img src="logo.png"> - 注意这里我们设置了图片的相对地址。能正常显示是因为我们在 head 部分设置了 base 标签，该标签指定了页面上所有链接的默认 URL，所以该图片的访问地址为 "http://www.runoob.com/images/logo.png"
        <br><br>
        <a href="http://www.runoob.com">菜鸟教程</a> - 注意这个链接会在新窗口打开，即便它没有 target="_blank" 属性。因为在 base 标签里我们已经设置了 target 属性的值为 "_blank"。
    </body>
</html>
```

`<meta>`元素来描述`HTML`文档的关键词、作者、字符集等：

``` xml
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>菜鸟教程(runoob.com)</title>
        <meta name="description" content="免费在线教程">
        <meta name="keywords" content="HTML,CSS,XML,JavaScript">
        <meta name="author" content="runoob">
        <meta charset="UTF-8">
    </head>

    <body>
        <p>所有 meta 标签显示在 head 部分...</p>
    </body>
</html>
```

&emsp;&emsp;HTML的<head>元素包含了所有的头部标签元素。在<head>元素中，你可以插入脚本(scripts)、样式文件(CSS)及各种meta信息。可以添加在头部区域的元素标签为：<title>、<style>、<meta>、<link>、<script>、<noscript>和<base>。
&emsp;&emsp;HTML的<title>元素定义了不同文档的标题，在“HTML/XHTML”文档中是必须的。<title>元素定义了浏览器工具栏的标题，当网页添加到收藏夹时显示在收藏夹中的标题以及显示在搜索引擎结果页面的标题。
&emsp;&emsp;HTML的<base>元素描述了基本的“链接地址/链接目标”，该标签作为HTML文档中所有的链接标签的默认链接：

<head>
    <base href="http://www.runoob.com/images/" target="_blank">
</head>
    HTML的<link>元素定义了文档与外部资源之间的关系，<link>标签通常用于链接到样式表：
<head>
    <link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
    HTML的<style>元素定义了HTML文档的样式文件引用地址，在<style>元素中你也可以直接添加样式来渲染HTML文档：
<head>
	<style type="text/css">
	body {background-color:yellow}
	p {color:blue}
	</style>
</head>
    HTML的<meta>元素meta标签描述了一些基本的元数据，它<meta>标签提供了元数据。元数据也不显示在页面上，但会被浏览器解析。META元素通常用于指定网页的描述、关键词，文件的最后修改时间、作者和其他元数据。
    元数据可以使用于浏览器(如何显示内容或重新加载页面)、搜索引擎(关键词)或其他Web服务，一般放置于<head>区域。使用实例如下所示：
<meta name="keywords" content="HTML, CSS, XML, XHTML, JavaScript"> #为搜索引擎定义关键词
<meta name="description" content="免费 Web & 编程 教程"> #为网页定义描述内容
<meta name="author" content="Runoob"> #定义网页作者
<meta http-equiv="refresh" content="30"> #每30秒中刷新当前页面
    HTML的<script>元素用于加载脚本文件，如JavaScript。

HTML样式之CSS
    下例演示如何使用添加到<head>部分的样式信息对HTML进行格式化：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>菜鸟教程(runoob.com)</title>
		<style type="text/css">
			h1 {color:red;}
			p {color:blue;}
		</style>
	</head>
	<body>
		<h1>这是一个标题</h1>
		<p>这是一个段落。</p>
	</body>
</html>

    下例演示如何使用样式属性做一个没有下划线的链接：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>菜鸟教程(runoob.com)</title>
	</head>
	<body>
		<a href="http://www.runoob.com/" style="text-decoration:none;">访问 runoob.com!</a>
	</body>
</html>

    下例演示如何将标签链接到一个外部样式表：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>菜鸟教程(runoob.com)</title>
		<link rel="stylesheet" type="text/css" href="styles.css">
	</head>
	<body>
		<h1>我使用了外部样式文件来格式化文本 </h1>
		<p>我也是!</p>
	</body>
</html>

    CSS是在HTML4开始使用的，是为了更好的渲染HTML元素而引入的。CSS可以通过以下方式添加到HTML中：
内联样式 -- 在HTML元素中使用“style”属性。
内部样式表 -- 在HTML文档头部 <head>区域使用<style>元素来包含CSS。
外部引用 -- 使用外部CSS文件，这是最好的方式。

    1、内联样式
    当特殊的样式需要应用到个别元素时，就可以使用内联样式。使用内联样式的方法是在相关的标签中使用样式属性。样式属性可以包含任何CSS属性。以下实例显示出如何改变段落的颜色和左外边距：
<p style="color:blue;margin-left:20px;">This is a paragraph.</p>
    背景色属性(background-color)定义一个元素的背景颜色，实例如下所示：
<body style="background-color:yellow;">
	<h2 style="background-color:red;">这是一个标题</h2>
	<p style="background-color:green;">这是一个段落。</p>
</body>
早期背景色属性(background-color)是使用bgcolor属性定义。
    我们可以使用font-family(字体)、color(颜色)和font-size(字体大小)属性来定义字体的样式，实例如下所示：
<h1 style="font-family:verdana;">一个标题</h1>
<p style="font-family:arial;color:red;font-size:20px;">一个段落。</p>
    使用text-align(文字对齐)属性指定文本的水平与垂直对齐方式，实例如下所示：
<h1 style="text-align:center;">居中对齐的标题</h1>
<p>这是一个段落。</p>
文本对齐属性text-align取代了旧标签<center>。
    2、内部样式表
    当单个文件需要特别样式时，就可以使用内部样式表。你可以在<head>部分通过<style>标签定义内部样式表：
<head>
	<style type="text/css">
	body {background-color:yellow;}
	p {color:blue;}
	</style>
</head>
    3、外部样式表
    当样式需要被应用到很多页面的时候，外部样式表将是理想的选择。使用外部样式表，你就可以通过更改一个文件来改变整个站点的外观，如下所示：
<head>
	<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
    HTML样式标签如下所示：
标签       描述
---------------
<style>    定义文本样式
<link>     定义资源引用地址
    在HTML4，原来支持定义HTML元素样式的标签和属性已被弃用，这些标签将不支持新版本的HTML标签。不建议使用的标签有<font>、<center>和<strike>，不建议使用的属性color和bgcolor。

HTML图像
    下例演示如何在网页中显示图像：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<p>
		一个图像:
		<img src="smiley.gif" alt="Smiley face" width="32" height="32"></p>
		<p>
		一个动图:
		<img src="hackanm.gif" alt="Computer man" width="48" height="48"></p>
		<p>
		注意插入动图的语法和静态图的语法是一样的。
		</p>
	</body>
</html>

    下例演示如何将其他文件夹或服务器的图片显示到网页中：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<p>一个来自文件夹中的图像:</p>
		<img src="/images/chrome.gif" alt="Google Chrome" width="33" height="32"><p>一个来自菜鸟教程的图像:</p>
		<img src="http://www.runoob.com/images/logo.png" alt="runoob.com" width="336" height="69">
	</body>
</html>
    在HTML中，图像由<img>标签定义。<img>是空标签，意思是说它只包含属性，并且没有闭合标签。要在页面上显示图像，你需要使用源属性(src)。src是指“source”，源属性的值是图像的URL地址。定义图像的语法如下所示：
<img src="url" alt="some_text">
URL指存储图像的位置。如果名为“pulpit.jpg”的图像位于www.runoob.com的images目录中，那么其URL为“http://www.runoob.com/images/pulpit.jpg”。浏览器将图像显示在文档中图像标签出现的地方，如果你将图像标签置于两个段落之间，那么浏览器会首先显示第一个段落，然后显示图片，最后显示第二段。
    HTML图像的Alt属性用来为图像定义一串预备的可替换的文本，替换文本属性的值是用户定义的。
<img src="boat.gif" alt="Big Boat">
在浏览器无法载入图像时，替换文本属性告诉读者它们失去的信息，此时浏览器将显示这个替代性的文本而不是图像。为页面上的图像都加上替换文本属性是个好习惯，这样有助于更好的显示信息，并且对于那些使用纯文本浏览器的人来说是非常有用的。
    height(高度)与width(宽度)属性用于设置图像的高度与宽度，属性值默认单位为像素：
<img src="pulpit.jpg" alt="Pulpit rock" width="304" height="228">
提示，指定图像的高度和宽度的一个很好的习惯。如果图像指定了高度宽度，页面加载时就会保留指定的尺寸。如果没有指定图片的大小，加载页面时有可能会破坏HTML页面的整体布局。
    假如某个HTML文件包含十个图像，那么为了正确显示这个页面，需要加载11个文件。加载图片是需要时间的，所以我们的建议是“慎用图片”。加载页面时，要注意插入页面图像的路径，如果不能正确设置图像的位置，浏览器无法加载图片，图像标签就会显示一个破碎的图片。
    下例演示如何在文字中排列图像：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<h4>默认对齐的图像 (align="bottom"):</h4>
		<p>这是一些文本。 <img src="smiley.gif" alt="Smiley face" width="32" height="32"> 这是一些文本。</p>
		<h4>图片使用 align="middle":</h4>
		<p>这是一些文本。 <img src="smiley.gif" alt="Smiley face" align="middle" width="32" height="32">这是一些文本。</p>
		<h4>图片使用 align="top":</h4>
		<p>这是一些文本。 <img src="smiley.gif" alt="Smiley face" align="top" width="32" height="32">这是一些文本。</p>
		<p><b>注意:</b>在HTML 4中 align 属性已废弃，HTML5 已不支持该属性，可以使用 CSS 代替。</p>
	</body>
</html>

    下例演示如何使图片浮动至段落的左边或右边：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<p>
		<img src="smiley.gif" alt="Smiley face" style="float:left" width="32" height="32"> 一个带图片的段落，图片浮动在这个文本的左边。
		</p>
		<p>
		<img src="smiley.gif" alt="Smiley face" style="float:right" width="32" height="32"> 一个带图片的段落，图片浮动在这个文本的右边。
		</p>
		<p><b>注意:</b> 在这里我们使用了 CSS "float" 属性，在HTML 4中 align 属性已废弃，HTML5 已不支持该属性，可以使用 CSS 代替。</p>
	</body>
</html>

    下例演示如何将图像作为一个链接使用：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<p>创建图片链接:
		<a href="http://www.runoob.com/html/html-tutorial.html">
		<img src="smiley.gif" alt="HTML 教程" width="32" height="32"></a></p>
		<p>无边框的图片链接:
		<a href="http://www.runoob.com/html/html-tutorial.html">
		<img border="0" src="smiley.gif" alt="HTML 教程" width="32" height="32"></a></p>
	</body>
</html>

    下例显示如何创建带有可供点击区域的图像地图，其中的每个区域都是一个超级链接：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<p>点击太阳或其他行星，注意变化：</p>
		<img src="planets.gif" width="145" height="126" alt="Planets" usemap="#planetmap">
		<map name="planetmap">
			<area shape="rect" coords="0,0,82,126" alt="Sun" href="sun.htm">
			<area shape="circle" coords="90,58,3" alt="Mercury" href="mercur.htm">
			<area shape="circle" coords="124,58,8" alt="Venus" href="venus.htm">
		</map>
	</body>
</html>

    HTML图像标签如下所示：
标签      描述
--------------
<img>     定义图像
<map>     定义图像地图
<area>    定义图像地图中的可点击区域

HTML表格
    HTML表格实例如下所示：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<p>
		每个表格从一个 table 标签开始。
		每个表格行从 tr 标签开始。
		每个表格的数据从 td 标签开始。
		</p>

		<h4>一列:</h4>

		<table border="1">
			<tr>
			<td>100</td>
			</tr>
		</table>
		<h4>一行三列:</h4>
		<table border="1">
			<tr>
			<td>100</td>
			<td>200</td>
			<td>300</td>
			</tr>
		</table>
		<h4>两行三列:</h4>
		<table border="1">
			<tr>
			<td>100</td>
			<td>200</td>
			<td>300</td>
			</tr>
			<tr>
			<td>400</td>
			<td>500</td>
			<td>600</td>
			</tr>
		</table>
		<h4>两行三列:</h4>

		<table border="1">
			<tr>
			<td>100</td>
			<td>200</td>
			<td>300</td>
			</tr>
			<tr>
			<td>400</td>
			<td>500</td>
			<td>600</td>
			</tr>
		</table>
	</body>
</html>

    表格由<table>标签来定义，每个表格均有若干行(由<tr>标签定义)，每行被分割为若干单元格(由<td>标签定义)。字母td指表格数据(table data)，即数据单元格的内容。数据单元格可以包含文本、图片、列表、段落、表单、水平线、表格等等。
<table border="1">
    <tr>
        <td>row 1, cell 1</td>
        <td>row 1, cell 2</td>
    </tr>
    <tr>
        <td>row 2, cell 1</td>
        <td>row 2, cell 2</td>
    </tr>
</table>

    如果不定义边框属性，表格将不显示边框，但是大多数时候，我们希望显示边框。使用边框属性来显示一个带有边框的表格：
<table border="1">
    <tr>
        <td>Row 1, cell 1</td>
        <td>Row 1, cell 2</td>
    </tr>
</table>
    表格的表头使用<th>标签进行定义，大多数浏览器会把表头显示为粗体居中的文本：
<table border="1">
    <tr>
        <th>Header 1</th>
        <th>Header 2</th>
    </tr>
    <tr>
        <td>row 1, cell 1</td>
        <td>row 1, cell 2</td>
    </tr>
    <tr>
        <td>row 2, cell 1</td>
        <td>row 2, cell 2</td>
    </tr>
</table>

    下例演示一个没有边框的表格：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<h4>这个表格没有边框:</h4>
		<table>
			<tr>
			<td>100</td>
			<td>200</td>
			<td>300</td>
			</tr>
			<tr>
			<td>400</td>
			<td>500</td>
			<td>600</td>
			</tr>
		</table>
		<h4>这个表格没有边框:</h4>

		<table border="0">
			<tr>
			<td>100</td>
			<td>200</td>
			<td>300</td>
			</tr>
			<tr>
			<td>400</td>
			<td>500</td>
			<td>600</td>
			</tr>
		</table>
	</body>
</html>

    下例演示如何显示表格表头：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<h4>水平标题:</h4>
		<table border="1">
			<tr>
			<th>Name</th>
			<th>Telephone</th>
			<th>Telephone</th>
			</tr>
			<tr>
			<td>Bill Gates</td>
			<td>555 77 854</td>
			<td>555 77 855</td>
			</tr>
		</table>
		<h4>垂直标题:</h4>
		<table border="1">
			<tr>
			<th>First Name:</th>
			<td>Bill Gates</td>
			</tr>
			<tr>
			<th>Telephone:</th>
			<td>555 77 854</td>
			</tr>
			<tr>
			<th>Telephone:</th>
			<td>555 77 855</td>
			</tr>
		</table>
	</body>
</html>

    下例演示一个带标题(caption)的表格：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<table border="1">
			<caption>Monthly savings</caption>
			<tr>
			<th>Month</th>
			<th>Savings</th>
			</tr>
			<tr>
			<td>January</td>
			<td>$100</td>
			</tr>
			<tr>
			<td>February</td>
			<td>$50</td>
			</tr>
		</table>
	</body>
</html>

    下例演示如何定义跨行或跨列的表格单元格：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<h4>单元格跨两格:</h4>
		<table border="1">
			<tr>
			<th>Name</th>
			<th colspan="2">Telephone</th>
			</tr>
			<tr>
			<td>Bill Gates</td>
			<td>555 77 854</td>
			<td>555 77 855</td>
			</tr>
		</table>
		<h4>单元格跨两列:</h4>
			<table border="1">
			<tr>
			<th>First Name:</th>
			<td>Bill Gates</td>
			</tr>
			<tr>
			<th rowspan="2">Telephone:</th>
			<td>555 77 854</td>
			</tr>
			<tr>
			<td>555 77 855</td>
			</tr>
		</table>
	</body>
</html>

    下例演示如何显示在不同的元素内显示元素：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<table border="1">
			<tr>
			<td>
			<p>这是一个段落</p>
			<p>这是另一个段落</p>
			</td>
			<td>这个单元格包含一个表格:
			<table border="1">
				<tr>
				<td>A</td>
				<td>B</td>
				</tr>
				<tr>
				<td>C</td>
				<td>D</td>
				</tr>
			</table>
			</td>
			</tr>
			<tr>
			<td>这个单元格包含一个列表
				<ul>
				<li>apples</li>
				<li>bananas</li>
				<li>pineapples</li>
				</ul>
			</td>
			<td>HELLO</td>
			</tr>
		</table>
	</body>
</html>

    下例演示如何使用“Cell padding”来创建单元格内容与其边框之间的空白：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<h4>没有单元格边距:</h4>
		<table border="1">
			<tr>
			<td>First</td>
			<td>Row</td>
			</tr>
			<tr>
			<td>Second</td>
			<td>Row</td>
			</tr>
		</table>
		<h4>有单元格边距:</h4>

		<table border="1"
			cellpadding="10">
			<tr>
			<td>First</td>
			<td>Row</td>
			</tr>
			<tr>
			<td>Second</td>
			<td>Row</td>
			</tr>
		</table>
	</body>
</html>

    下例演示如何使用“Cell spacing”增加单元格之间的距离：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
	</head>
	<body>
		<h4>没有单元格间距:</h4>
		<table border="1">
			<tr>
			<td>First</td>
			<td>Row</td>
			</tr>
			<tr>
			<td>Second</td>
			<td>Row</td>
			</tr>
		</table>

		<h4>单元格间距="0":</h4>
		<table border="1" cellspacing="0">
			<tr>
			<td>First</td>
			<td>Row</td>
			</tr>
			<tr>
			<td>Second</td>
			<td>Row</td>
			</tr>
		</table>
		<h4>单元格间距="10":</h4>

		<table border="1" cellspacing="10">
			<tr>
			<td>First</td>
			<td>Row</td>
			</tr>
			<tr>
			<td>Second</td>
			<td>Row</td>
			</tr>
		</table>
	</body>
</html>

    HTML表格标签如下所示：
标签          描述
------------------
<table>       定义表格
<th>          定义表格的表头
<tr>          定义表格的行
<td>          定义表格单元
<caption>     定义表格标题
<colgroup>    定义表格列的组
<col>         定义用于表格列的属性
<thead>       定义表格的页眉
<tbody>       定义表格的主体
<tfoot>       定义表格的页脚