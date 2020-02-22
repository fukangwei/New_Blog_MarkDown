---
title: content-type和MIME TYPE
categories: HTML笔记
date: 2019-03-07 19:35:57
---
&emsp;&emsp;首先，我们要了解浏览器是如何处理内容的。在浏览器中显示的内容有`HTML`、`XML`、`GIF`等，那么浏览器是如何区分它们，决定以什么形式来显示呢？答案是`MIME Type`，也就是该资源的媒体类型。<!--more-->
&emsp;&emsp;媒体类型通常是通过`HTTP`协议，由`Web`服务器告知浏览器的，更准确地说，是通过`Content-Type`来表示的。例如`Content-Type: text/HTML`表示内容是`text/HTML`类型，也就是超文本文件。
&emsp;&emsp;通常只有一些在互联网上获得广泛应用的格式才会获得一个`MIME Type`，如果是某个客户端自己定义的格式，一般只能以`application/x-`开头。`XHTML`正是一个获得广泛应用的格式。因此在`RFC 3236`中，说明了`XHTML`格式文件的`MIME Type`应该是`application/xHTML+XML`。
&emsp;&emsp;在把输出结果传送到浏览器上时，浏览器必须启动适当的应用程序来处理这个输出文档。这可以通过多种类型`MIME`(多功能网际邮件扩充协议)来完成。在`HTTP`中，`MIME`类型被定义在`Content-Type header`中。
&emsp;&emsp;假如你要传送一个`Microsoft Excel`文件到客户端，那么这时的`MIME`类型就是`application/vnd.ms-excel`。在大多数实际情况中，这个文件然后将传送给`Execl`来处理(假设我们设定`Execl`为处理特殊`MIME`类型的应用程序)。

### 多媒体文件格式MIME

&emsp;&emsp;最早的`HTTP`协议并没有附加的数据类型信息，所有传送的数据都被客户程序解释为超文本标记语言`HTML`文档。为了支持多媒体数据类型，`HTTP`协议中就使用了附加在文档之前的`MIME`数据类型信息来标识数据类型。
&emsp;&emsp;`MIME`设计的最初目的是为了在发送电子邮件时附加多媒体数据，让邮件客户程序能根据其类型进行处理。当它被`HTTP`协议支持之后，它的意义就更为显著了，它使得`HTTP`传输的不仅是普通的文本。
&emsp;&emsp;每个`MIME`类型由两部分组成，前面是数据的大类别，例如声音`audio`、图像`image`等，后面定义具体的种类。常见的`MIME`类型如下：

类型               | 后缀名            | MIME
-------------------|------------------|-------
超文本标记语言文本   | `.html`或`.html` | `text/html`
普通文本            | `.txt`           | `text/plain`
`RTF`文本           | `.rtf`           | `application/rtf`
`GIF`图形           | `.gif`           | `image/gif`
`JPEG`图形          | `.jpeg`或`.jpg`  | `image/jpeg`
`au`声音文件        | `.au`            | `audio/basic`
`MIDI`音乐文件      | `.mid`和`.midi`  | `audio/midi`和`audio/x-midi`
`RealAudio`音乐文件 | `.ra`和`.ram`    | `audio/x-pn-realaudio`
`MPEG`文件          | `.mpg`和`.mpeg`  | `video/mpeg`
`AVI`文件           | `.avi`           | `video/x-msvideo`
`GZIP`文件          | `.gz`            | `application/x-gzip`
`TAR`文件           | `.tar`           | `application/x-tar`

&emsp;&emsp;`Internet`中有一个专门组织`IANA`来确认标准的`MIME`类型，但`Internet`发展得太快，很多应用程序等不及`IANA`来确认它们使用的`MIME`类型为标准类型。因此它们使用在类别中以`x-`开头的方法标识这个类别还没有成为标准，例如`x-gzip`、`x-tar`等。事实上这些类型运用的很广泛，已经成为了事实标准。只要客户机和服务器共同承认这个`MIME`类型，即使它是不标准的类型也没有关系，客户程序就能根据`MIME`类型，采用具体的处理手段来处理数据。而`Web`服务器和浏览器(包括操作系统)中，缺省都设置了标准的和常见的`MIME`类型，只有对于不常见的`MIME`类型，才需要同时设置服务器和客户浏览器，以进行识别。
&emsp;&emsp;由于`MIME`类型与文档的后缀相关，因此服务器使用文档的后缀来区分不同文件的`MIME`类型，服务器中必须定义文档后缀和`MIME`类型之间的对应关系。而客户程序从服务器上接收数据的时候，它只是从服务器接受数据流，并不了解文档的名字，因此服务器必须使用附加信息来告诉客户程序数据的`MIME`类型。服务器在发送真正的数据之前，就要先发送标志数据的`MIME`类型的信息，这个信息使用`Content-type`关键字进行定义。例如对于`HTML`文档，服务器将首先发送以下两行`MIME`标识信息，这个标识并不是真正的数据文件的一部分。

``` html
Content-type: text/html
(空行)
```

注意第二行为一个空行，这是必须的。使用这个空行的目的是将`MIME`信息与真正的数据内容分隔开。