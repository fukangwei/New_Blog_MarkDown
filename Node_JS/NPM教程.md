---
title: NPM教程
categories: Node.js笔记
abbrlink: 29097ff9
date: 2019-02-08 13:11:48
---
### NPM基础

&emsp;&emsp;`NPM`是随同`Node.js`一起安装的包管理工具，能够解决代码部署上的很多问题，常见的使用场景有以下几种：<!--more-->

- 允许用户从`NPM`服务器下载别人编写的第三方包到本地使用。
- 允许用户从`NPM`服务器下载并安装别人编写的命令行程序到本地使用。
- 允许用户将自己编写的包或命令行程序上传到`NPM`服务器供别人使用。

可以通过输入`npm -v`来测试是否成功安装：

``` bash
$ npm -v
2.3.0
```

&emsp;&emsp;`npm`安装`Node.js`模块语法格式如下：

``` bash
npm install <Module Name>
```

使用`npm`命令安装常用的`web`框架模块`express`：

``` bash
npm install express
```

安装好之后，`express`包就放在了工程目录下的`node_modules`目录中，在代码中只需要通过`require('express')`的方式就可以引用，无需指定第三方包路径：

``` javascript
var express = require('express');
```

### 全局安装与本地安装

&emsp;&emsp;`npm`的包安装分为本地安装(`local`)、全局安装(`global`)两种，差别只是有没有`-g`而已：

``` bash
npm install express     # 本地安装
npm install express -g  # 全局安装
```

- 本地安装：将安装包放在`./node_modules`下(运行`npm`命令时所在的目录)，如果没有`node_modules`目录，会在当前执行`npm`命令的目录下生成`node_modules`目录。可以通过`require`来引入本地安装的包。
- 全局安装：将安装包放在`/usr/local`下或者`node`的安装目录。可以直接在命令行里使用。

&emsp;&emsp;使用全局方式安装`express`：

``` bash
npm install express -g
```

安装过程输出如下内容，第一行输出了模块的版本号及安装位置：

``` bash
express@4.13.3 node_modules/express
├── escape-html@1.0.2
├── range-parser@1.0.2
├── merge-descriptors@1.0.0
...
```

使用以下命令来查看所有全局安装的模块：

``` bash
$ npm list -g
├─┬ cnpm@4.3.2
│ ├── auto-correct@1.0.0
│ ├── bagpipe@0.3.5
│ ├── colors@1.1.2
...
```

如果要查看某个模块的版本号，可以使用如下命令：

``` bash
$ npm list grunt
projectName@projectVersion /path/to/project/folder
└── grunt@0.4.1
```

&emsp;&emsp;可以使用以下命令来卸载`Node.js`模块：

``` bash
npm uninstall express
```

卸载后，可以到`./node_modules/`目录下查看包是否还存在，或者使用以下命令查看：

``` bash
npm ls
```

&emsp;&emsp;使用以下命令更新模块：

``` bash
npm update express
```

&emsp;&emsp;使用以下来命令搜索模块：

``` bash
npm search express
```