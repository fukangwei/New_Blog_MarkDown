---
title: Qt添加资源文件
categories: Qt应用示例
date: 2019-03-16 16:51:49
---
&emsp;&emsp;编写`gui`时，可能需要一些额外的资源(比如贴图用的图片)，可以使用`Qt`的资源文件进行统一管理。<!--more-->
&emsp;&emsp;1. 右击项目文件夹，选择添加新文件，然后选择`Qt`，最后是`Qt resource file`。

<img src="./Qt添加资源文件/1.png" height="250" width="433">

&emsp;&emsp;2. 填写好`name`后点下一步，最终完成资源文件的创建。然后右击项目中生成的`.qrc`文件，点击`添加前缀`。

<img src="./Qt添加资源文件/2.png">

&emsp;&emsp;3. 添加好前缀之后就可以添加图片文件了。选择需要添加的文件，然后保存，这样就可以在资源浏览器中看到那些添加的图片资源，也就能在代码中引用了。

<img src="./Qt添加资源文件/3.png" height="260" width="284">