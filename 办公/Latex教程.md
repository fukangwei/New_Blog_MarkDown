---
title: Latex教程
date: 2019-05-10 20:44:46
tags:
---
### Hello World

&emsp;&emsp;代码如下：

``` latex
\documentclass{article}
% 这里是导言区
\begin{document}
Hello, world!
\end{document}
```

此处的第一行`\documentclass{article}`中包含了一个控制序列(或称`命令/标记`)。所谓控制序列，是以反斜杠`\`开头，以第一个空格或非字母的字符结束的一串文字，它们并不被输出，但是它们会影响输出文档的效果。这里的控制序列是`documentclass`，它后面紧跟着的`{article}`代表这个控制序列有一个必要的参数，该参数的值为`article`。这个控制序列的作用，是调用名为`article`的文档类。
&emsp;&emsp;部分控制序列还有被方括号`[]`包括的可选参数。所谓`文档类`，即是`TeX`系统预设的(或是用户自定的)一些格式的集合。不同的文档类在输出效果上会有差别。
&emsp;&emsp;此处的第二行以`%`开头。在`TeX`风格的文档中，从`%`开始，到该行末尾的所有字符，都会被`TeX`系统无视，只作为供人类阅读的注释。除非在`%`前加上反斜杠来取消这一特性：

``` latex
\documentclass{article}
\begin{document}
Hello, \%world!
\end{document}
```

此处`%`被当做正常的百分号处理，其后的文字也将被正常输出。
&emsp;&emsp;其后出现了控制序列`begin`，这个控制序列总是与`end`成对出现。这两个控制序列以及它们中间的内容被称为`环境`，它们之后的第一个必要参数总是一致的，被称为`环境名`。只有在`document`环境中的内容，才会被正常输出到文档中去，或是作为控制序列对文档产生影响。因此，在`\end{document}`之后插入任何内容都是无效的。
&emsp;&emsp;`\begin{document}`与`\documentclass{article}`之间的部分被称为`导言区`。导言区中的控制序列，通常会影响到整个输出文档。

### 实现中英文混排

&emsp;&emsp;中英文混排可以使用`ctex`文档类：

``` latex
\documentclass[UTF8]{ctexart}
\begin{document}
你好，world!
\end{document}
```

### 作者、标题、日期

&emsp;&emsp;代码如下：

``` latex
\documentclass[UTF8]{ctexart}
\title{你好，world!}
\author{Liam}
\date{\today}
\begin{document}
    \maketitle
    你好，world!
\end{document}
```

在`document`环境中，除了原本的`你好，world!`，还多了一个控制序列`maketitle`。这个控制序列能将在导言区中定义的标题、作者、日期按照预定的格式展现出来。

### 章节和段落

&emsp;&emsp;代码如下：

``` latex
\documentclass[UTF8]{ctexart}
\title{你好，world!}
\author{Liam}
\date{\today}
\begin{document}
    \maketitle
    \section{你好中国}
    中国在 East Asia.
    \subsection{Hello Beijing}
    北京是 capital of China.
    \subsubsection{Hello Dongcheng District}
    \paragraph{Tian'anmen Square}
    is in the center of Beijing
    \subparagraph{Chairman Mao}
    is in the center of 天安门广场。
    \subsection{Hello 山东}
    \paragraph{山东大学} is one of the best university in 山东。
\end{document}
```

在文档类`article`或`ctexart`中，定义了五个控制序列来调整行文组织结构：

- `\section{}`
- `\subsection{}`
- `\subsubsection{}`
- `\paragraph{}`
- `\subparagraph{}`

### 插入目录

&emsp;&emsp;在上一节的文档中，找到`\maketitle`，在它的下面插入控制序列`\tableofcontents`，保存并使用`LaTeX`编译两次：

``` latex
\documentclass[UTF8]{ctexart}
\title{你好，world!}
\author{Liam}
\date{\today}
\begin{document}
    \maketitle
    \tableofcontents
    \section{你好中国}
    中国在 East Asia.
    \subsection{Hello Beijing}
    北京是 capital of China.
    \subsubsection{Hello Dongcheng District}
    \paragraph{Tian'anmen Square}
    is in the center of Beijing
    \subparagraph{Chairman Mao}
    is in the center of 天安门广场。
    \subsection{Hello 山东}
    \paragraph{山东大学} is one of the best university in 山东。
\end{document}
```

### 表格

&emsp;&emsp;`tabular`环境提供了最简单的表格功能。它用`\hline`命令表示横线，`|`表示竖线；用`&`来分列，用`\\`来换行；每列可以采用居中、居左、居右等横向对齐方式，分别用`l`、`c`、`r`来表示：

``` latex
\documentclass[UTF8]{ctexart}
\begin{document}
\begin{tabular}{|l|c|r|}
    \hline
    操作系统 & 发行版 & 编辑器\\
    \hline
    Windows & MikTeX & TexMakerX \\
    \hline
    Unix/Linux & teTeX & Kile \\
    \hline
    Mac OS & MacTeX & TeXShop \\
    \hline
    通用 & TeX Live & TeXworks \\
    \hline
\end{tabular}
\end{document}
```

&emsp;&emsp;`latex`还可以对表格进行合并，例如合并行(`\multirow`)和合并列(`\multicolumn`)。命令格式如下：

``` latex
\multirow{nrows}[bigstructs]{width}[fixup]{text}
```

- `nrows`：设定所占用的行数。
- `bigstructs`：如果使用了`bigstruct`宏包，那么可以使用此项。
- `width`：设定该栏文本的宽度。如果想让`LaTeX`自行决定文本的宽度，则使用`*`。
- `fixup`：此为可选项，主要用来调整文本的垂直位置。
- `text`：所要排版的文本，可用`\\`来强迫换行。

``` latex
\documentclass{article}
\usepackage{multirow}
\begin{document}
    \begin{tabular}{|l|l|l|l|}
        \hline  
        \multirow{4}{2cm}{This is a demo table}  
        & C2a &
        \multirow{4}{2cm}{This is another one} & C4a\\
        & C2b &  & C4b\\
        & C2c &  & C4c\\
        & C2d & & C4d\\
        \hline
    \end{tabular}
\end{document}
```

如果需要给表格增加说明，可以使用`table`：

``` latex
\documentclass[UTF8]{ctexart}
\begin{document}
    \begin{table}[h]
        \centering  % 显示位置为中间
        \caption{standard table}  % 表格标题
        \label{table1}  % 用于索引表格的标签
        % 字母的个数对应列数，“|”代表分割线，“l”代表左对齐，“c”代表居中，“r”代表右对齐
        \begin{tabular}{|c|c|c|c|}  
            \hline  % 表格的横线
            1 & 2 & 3 & 4 \\  % 表格中的内容，用“&”分开，“\\”表示下一行
            \hline
            0.1 & 0.2 & 0.3 & 0.4 \\
            \hline
        \end{tabular}
    \end{table}
\end{document}
```

使用`latex`制作表格是一件很复杂的事，可以使用`http://www.tablesgenerator.com/`在线生成。

### 页边距

&emsp;&emsp;设置页边距，推荐使用`geometry`宏包。比如我希望将纸张的长度设置为`20cm`，宽度设置为`15cm`，左边距`1cm`，右边距`2cm`，上边距`3cm`，下边距`4cm`，可以在导言区加上如下内容：

``` latex
\usepackage{geometry}
\geometry{papersize={20cm, 15cm}}
\geometry{left=1cm, right=2cm, top=3cm, bottom=4cm}
```

### 页眉页脚

&emsp;&emsp;设置页眉页脚，推荐使用`fancyhdr`宏包。比如我希望在页眉左边写上我的名字，中间写上今天的日期，右边写上我的电话；页脚的正中写上页码；页眉和正文直接有一道宽为`0.4pt`的横线分割，可以在导言区加上如下内容：

``` latex
\usepackage{fancyhdr}
\pagestyle{fancy}
\lhead{little fool}
\chead{\today}
\rhead{152xxxxxxxx}
\lfoot{}
\cfoot{\thepage}
\rfoot{}
\renewcommand{\headrulewidth}{0.4pt}
\renewcommand{\footrulewidth}{0pt}
```

### 段间距

&emsp;&emsp;可以通过修改计数器`\parskip`的值来调整段间距，例如在原有的基础上增加段间距`0.4em`：

``` latex
\documentclass[UTF8]{ctexart}
\addtolength{\parskip}{.4em}
\begin{document}
TeX是高德纳教授做出来的排版引擎，同时也是该引擎使用的标记语言的名称。

而LaTeX则是L.Lamport授开发的基于TeX的排版系统。
\end{document}
```

注意段与段之间需要一个空行。如果需要减小段间距，只需将该数值改为负值即可。

### 插入图片

&emsp;&emsp;可以使用`graphicx`宏包提供的`\includegraphics`命令来插入图片。比如在`TeX`源文件同目录下有名为`test.jpg`的图片，可以用这样的方式将它插入到输出文档中：

``` latex
\documentclass{article}
\usepackage{graphicx}
\begin{document}
\includegraphics{test.jpg}
\end{document}
```

图片可能很大，超过了输出文件的纸张大小。这时你可以使用`\includegraphics`控制序列的可选参数：

``` latex
\includegraphics[width = .8\textwidth]{a.jpg}
```

这样图片的宽度会被缩放至页面宽度的百分之八十，图片的总高度会按比例缩放。

### 分栏显示

&emsp;&emsp;可以使用`twocolumn`来设置双栏显示，栏间距使用`\columnsep`进行设置：

``` latex
\documentclass[UTF8, twocolumn]{ctexart}
\setlength{\columnsep}{0.24in}
\begin{document}
    % 内容
\end{document}
```

### Latex字体尺寸

&emsp;&emsp;单位如下：

pt  1pt = 1/72.27英寸
bp  1bp = 1/72英寸
mm  毫米
cm  厘米
in  英寸
ex  当前字体中x的高度

字号如下：

size            | 10pt (default) | 11pt option | 12pt option
----------------|----------------|-------------|------------
`\tiny`         | 5pt            | 6pt         | 6pt
`\scriptsize`   | 7pt            | 8pt         | 8pt
`\footnotesize` | 8pt            | 9pt         | 10pt
`\small`        | 9pt            | 10pt        | 11pt
`\normalsize`   | 10pt           | 11pt        | 12pt
`\large`        | 12pt           | 12pt        | 14pt
`\Large`        | 14pt           | 14pt        | 17pt
`\LARGE`        | 17pt           | 17pt        | 20pt
`\huge`         | 20pt           | 20pt        | 25pt
`\Huge`         | 25pt           | 25pt        | 25pt

中英文字号对照表如下：

初号  小初  一号     小一  二号    小二   三号    小三
42pt  36pt  26pt    24pt  22pt   18pt   16pt   15pt
四号  小四  五号     小五  六号    小六   七号    八号
14pt  12pt  10.5pt  9pt   7.5pt  6.5pt  5.5pt   5pt

Latex字体处理

显示直立文本：“\textup{文本}”
意大利斜体：“\textit{文本}”
slanted斜体：“\textsl{文本}”
显示小体大写文本：“\textsc{文本}”
中等权重：“\textmd{文本}”
加粗命令：“\textbf{文本}”
默认值：“\textnormal{文本}”
下划线：“\underline{文本}”
文字居中：
\begin{center}
第一行 \\
第二行 \\
\end{center}

### 摘要和关键字

&emsp;&emsp;代码如下：

``` latex
\documentclass[conference, letterpaper]{IEEEtran}
​
\begin{document}
    \begin{abstract}
        These instructions give you the basic guidelines for preparing
        papers for icma2015/IEEE conference proceedings. To get more information,
        please visit our website: http://www.ieee-ICMA.org
        \\
    \end{abstract}
​
    \begin{keywords}
        List key index terms here. No more than 5.
    \end{keywords}
\end{document}
```

### 列表

&emsp;&emsp;有序列表如下：

``` latex
\documentclass{article}
\begin{document}
    \begin{enumerate}
    \item This is the first item
    \item This is the second item
    \item This is the third item
    \end{enumerate}
\end{document}
```

无序列表如下：

``` latex
\documentclass{article}
\begin{document}
    \begin{itemize}
    \item This is the first item
    \item This is the second item
    \item This is the third item
    \end{itemize}
\end{document}
```

### Latex浮动体

&emsp;&emsp;图片或表格通常都占有较大的一块区域，直接放在文档中常常会造成分页的困难：在文档中常常会造成分页的困难，即一页放不下，放在后一页又会造成很大的留白。除了图表，其他类型的大块内容也有可能出现类似的问题，如程序算法、大型公式等。
&emsp;&emsp;`LaTeX`中的浮动体便可以解决这个问题。浮动体是一个活动的盒子，它可以把内容放在距离浮动体代码前后不远的地方，通常就是浮动体代码所在地，但也可以放在页面开头、末尾或是单独的一页中。使用浮动体，就可以在不太费力仔细调整内容的情况下，避免大块图表把整齐的页面弄糟。此外，浮动体还可以给图表添加标题，这在论文写作中非常重要。
&emsp;&emsp;`LaTeX`中两种浮动体环境，即`figure`和`table`，通常分别用于图和表的排版。`figure`环境的语法格式如下：

``` latex
\begin{figure}[允许位置]
内容
\end{figure}
```

`table`环境与之类似。可选参数允许位置用来设定浮动环境可以出现在页面中的位置，即`h`、`t`、`b`、`p`四个选项的组合：

- `h`：此处(`here`)，浮动体的内容被放在代码所在的上下文位置。
- `t`：页顶(`top`)，浮动体被放在一页的顶部，这可以是代码所在环境的页面或之后的页面，注意当页排版的浮动体可能出现在实际代码之前。
- `b`：页底(`bottom`)，浮动体被放在一页的底部。
- `p`：独立一页(`page`)，一个或多个浮动体被放在单独的页面中，这个页面被称为`浮动页`。

浮动体最常见的应用就是直接在`table`环境中放置`tabular`，或者是在`figure`环境中放置`\includegraphics`命令插入的图形。经常还在前面使用`\centering`命令让图表居中放置。

``` latex
\documentclass[UTF8]{ctexart}
\usepackage{graphicx}
\begin{document}
    \begin{figure}[htbp]
        \centering
        \includegraphics[width = .8\textwidth]{test.jpg}
    \end{figure}

    \centering
    \begin{tabular}{|l|c|r|}
        \hline
        操作系统 & 发行版 & 编辑器\\
        \hline
        Windows & MikTeX & TexMakerX \\
        \hline
        Unix/Linux & teTeX & Kile \\
        \hline
        Mac OS & MacTeX & TeXShop \\
        \hline
        通用 & TeX Live & TeXworks \\
        \hline
    \end{tabular}
\end{document}
```

### 插入公式

&emsp;&emsp;代码如下：

``` latex
\documentclass{article}
\begin{document}
    \begin{equation}
    a + b = c
    \end{equation}
\end{document}
```

注意，每一个公式后面都有一个序号。