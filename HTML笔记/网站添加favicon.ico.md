---
title: 网站添加favicon.ico
categories: HTML笔记
date: 2019-03-08 10:49:06
---
&emsp;&emsp;`favicon.ico`图标是网站的缩略标志，可以显示在浏览器标签、地址栏左边和收藏夹。它是展示网站个性的缩略`logo`标志，也可以说是网站头像。<!--more-->
&emsp;&emsp;准备一张长宽一样的图片，然后打开在线工具`aTool`(`http://www.atool.org/ico.php`)生成`ico`图标。点击选择文件，可以浏览你刚刚生成的宽高相同的图片。选择生成的图片尺寸大小，建议采用`32 * 32`的大小。点击`生成favicon.ico图标`按钮，即可结束。
&emsp;&emsp;点击`保存`链接即可将图片另存到本地磁盘，然后将文件重命名为`favicon.ico`，放到网站的根目录，强烈建议放在根目录。然后在网站的每一个页面的`head`标签内加入如下两行代码。清空浏览器缓存，刷新网页网址即可看到网站的图标。

``` html
<head>
    <link rel="shortcut icon" href="/favicon.ico"/>
    <link rel="bookmark" href="/favicon.ico"/>
</head>
```

&emsp;&emsp;**补充说明**：在线转换之前一定要转换成长宽相等的图片，否则转换成`ico`之后，图片会被拉伸或者压缩。