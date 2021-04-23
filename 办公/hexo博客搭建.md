---
title: hexo博客搭建
date: 2021-02-21 16:26:23
categories: 办公
top: true
---
### 基础

&emsp;&emsp;搭建博客需要安装如下软件：<!--more-->

1. `git`
2. `NodeJs`

&emsp;&emsp;安装`Hexo`需要使用如下命令：

``` bash
npm install hexo-cli -g
```

&emsp;&emsp;新建一个文件夹作为博客的主目录，例如`My_Blog`，然后在该目录下使用如下命令：

``` bash
hexo init .
hexo server
```

然后在浏览器中输入地址`http://localhost:4000/`，即可查看`Hexo`博客。

### 配置文件

&emsp;&emsp;在`Hexo`中，有两份主要的配置文件，其名称都是`_config.yml`。

1. 一份位于站点根目录下，主要包含`Hexo`本身的站点配置。
2. 另一份位于主题目录下，这份配置由主题作者提供，主要用于配置主题相关的选项。

&emsp;&emsp;为了描述方便，在以下说明中，将前者称为`站点配置文件`，后者称为`主题配置文件`。

``` bash
hexo/_config.yml # 站点配置文件
hexo/themes/next/_config.yml # 主题配置文件
```

### 安装Next主题

&emsp;&emsp;进入命令行，输入如下命令即可下载`Next`主题：

``` bash
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

修改站点配置文件`_config.yml`，找到如下代码：

``` bash
## Themes: https://hexo.io/themes/
theme: landscape
```

将`landscape`修改为`next`即可。

### 修改语言

&emsp;&emsp;打开`站点配置文件`，搜索`language`，找到如下代码：

``` bash
author:
language:
timezone:
```

在`language`后面输入`zh-CN`，即可将博客切换为中文。

### 切换样式

&emsp;&emsp;`next`提供了`4`种样式。在`next/_config.yml`中查找`scheme`：

``` bash
# Schemes
scheme: Muse
#scheme: Mist
#scheme: Pisces
#scheme: Gemini
```

选择你喜欢的一种样式，去掉前面的`#`，并在其他样式前加上`#`即可。我比较中意`Gemini`。

### 本地搜索

&emsp;&emsp;安装插件`hexo-generator-searchdb`：

``` bash
npm install hexo-generator-searchdb --save
```

编辑`主题配置文件`，启用本地搜索功能，并且修改`url`和`root`：

``` bash
# URL
## If your site is put in a subdirectory, set url as 'http://example.com/child' and root as '/child/'
url: https://fukangwei.github.io/
root: //
permalink: :title/

# Local search
local_search:
    enable: true
```

### 文章添加分类

#### 添加分类页

&emsp;&emsp;新建一个页面，命名为`categories`：

``` bash
hexo new page categories
```

编辑新建的页面，将页面的类型设置为`categories`：

``` bash
---
title: 分类
date: 2014-12-22 12:39:04
type: "categories"
---
```

编辑主题的`_config.yml`，将`menu`中的`categories: /categories`注释去掉：

``` bash
menu:
    home: /
    categories: /categories
    archives: /archives
    tags: /tags
```

#### 添加文章分类关联

&emsp;&emsp;如果需要将文章进行分类，则在其中添加`categories`属性，例如下面是将文章分类为`前端`：

``` bash
---
title: Hexo World
date: 2016-03-16 08:12:43
categories: 前端
---
```

### 远程部署到GitHub

&emsp;&emsp;首先在`github`建立一个仓库。注意，仓库名一定要是你`github`的名字，例如`fukangwei.github.io`。
&emsp;&emsp;然后需要安装如下的插件：

``` bash
npm install hexo-deployer-git --save
```

&emsp;&emsp;在`站点配置文件`中，进行如下修改：

``` bash
deploy:
  type: git
  repo: https://github.com/fukangwei/fukangwei.github.io.git
  branch: master
```

&emsp;&emsp;最后使用如下命令即可部署到`GitHub`：

``` bash
hexo clean # 清除缓存
hexo g # 上传到仓库
hexo d # 部署
```

在浏览器中输入`https://fukangwei.github.io/`，即可看到部署的博客。

### Hexo博客中使用Latex

&emsp;&emsp;安装`Kramed`：

``` bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

&emsp;&emsp;打开`node_modules/hexo-renderer-kramed/lib/renderer.js`：

``` javascript
// Change inline math rule
function formatText(text) {
    // Fit kramed's rule: $$ + \1 + $$
    return text.replace(/`\$(.*?)\$`/g, '$$$$$1$$$$');
}
```

修改为：

``` javascript
// Change inline math rule
function formatText(text) {
    return text;
}
```

&emsp;&emsp;停用`hexo-math`，并安装`mathjax`：

``` bash
npm uninstall hexo-math --save
npm install hexo-renderer-mathjax --save
```

&emsp;&emsp;打开`node_modules/hexo-renderer-mathjax/mathjax.html`，将最后一行的`<script>`改为：

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML"></script>
```

&emsp;&emsp;因为`LaTeX`与`markdown`语法有语义冲突，所以`hexo`默认的转义规则会将一些字符进行转义，所以我们需要对默认的规则进行修改。打开`node_modules/kramed/lib/rules/inline.js`，进行如下修改：

``` javascript
var inline = {
    // escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/, /* 第一处修改 */
    escape: /^\\([`*\[\]()#$+\-.!_>])/,
    ...
    // em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/, /* 第二处修改 */
    em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
    ...
};
```

&emsp;&emsp;打开主题目录下的`_config.yml`文件，将如下的`enable`设置为`true`：

``` bash
mathjax:
    enable: false
```

&emsp;&emsp;如果需要在文章中增加公式，则要加上`mathjax: true`，例如：

``` bash
---
title: Python向信号中添加不同强度dB的噪声
date: 2019-10-23 14:05:37
mathjax: true
---
```

### 插入本地图片

&emsp;&emsp;在博客的根目录执行如下命令：

``` bash
npm install https://github.com/CodeFalling/hexo-asset-image --save
```

在`站点配置文件`中，将`post_asset_folder`设置为`true`：

``` bash
post_asset_folder: true
```

&emsp;&emsp;插入图片只要使用如下语法：

``` html
<img src="./hexo博客搭建/1.png" width="70%">
```

### 添加置顶功能

&emsp;&emsp;在`Hexo`博客的根目录下使用如下命令：

``` bash
npm uninstall hexo-generator-index --save
npm install hexo-generator-index-pin-top --save
```

然后在需要置顶的文章的`Front-matter`中加上`top: true`即可：

``` bash
---
title: hexo+GitHub博客搭建实战
date: 2017-09-08 12:00:25
categories: 博客搭建系列
top: true
---
```

### Hexo使用注意事项

&emsp;&emsp;1. 如果需要截断文章，则要使用`<!--more-->`。
&emsp;&emsp;2. 如果需要在<code>\`\`\`</code>的后面插入`---`，则两者之间要间隔`2`个空行。