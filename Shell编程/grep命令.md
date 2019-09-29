---
title: grep命令
categories: Shell编程
abbrlink: 471198f0
date: 2019-01-21 19:02:28
---
### 基本用法

&emsp;&emsp;`grep`是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。
&emsp;&emsp;`Unix`的`grep`家族包括`grep`、`egrep`和`fgrep`。`egrep`和`fgrep`的命令只跟`grep`有很小不同，`egrep`是`grep`的扩展，支持更多的`re`元字符，`fgrep`就是`fixed grep`或`fast grep`，它们把所有的字母都看作单词，也就是说正则表达式中的元字符表示回其自身的字面意义，不再特殊。`linux`使用`GNU`版本的`grep`。它功能更强，可以通过`-G`、`-E`、`-F`命令行选项来使用`egrep`和`fgrep`的功能。
&emsp;&emsp;`grep`用法如下：

``` bash
grep [-acinv] [--color=auto] '搜寻字符串' filename
```

- `-a`：将`binary`文件以`text`文件的方式搜寻数据。
- `-c`：计算找到`搜寻字符串`的次数。
- `-i`：忽略大小写的不同，所以大小写视为相同。
- `-n`：输出行号。
- `-v`：反向选择，亦即显示出没有`搜寻字符串`内容的那一行。
- `--color=auto`：可以将找到的关键词部分加上颜色的显示。

&emsp;&emsp;将`/etc/passwd`中有`root`的行取出来：

``` bash
grep root /etc/passwd
# 或者
cat /etc/passwd | grep root
```

将`/etc/passwd`有`root`的行取出来，同时显示这些行在`/etc/passwd`的行号：

``` bash
$ grep -n root /etc/passwd
1:root:x:0:0:root:/root:/bin/bash
30:operator:x:11:0:operator:/root:/sbin/nologin
```

&emsp;&emsp;将`/etc/passwd`没有出现`root`和`nologin`的行取出来：

``` bash
grep -v root /etc/passwd | grep -v nologin
```

&emsp;&emsp;用`dmesg`列出核心信息，再以`grep`找出内含`eth`那行，在关键字所在行的前两行与后三行也一起捉出来显示：

``` bash
$ dmesg | grep -n -A3 -B2 --color=auto 'eth'
245-PCI: setting IRQ 10 as level-triggered
246-ACPI: PCI Interrupt 0000:00:0e.0[A] -> Link [LNKB] ...
247:eth0: RealTek RTL8139 at 0xee846000, 00:90:cc:a6:34:84, IRQ 10
248:eth0: Identified 8139 chip type 'RTL-8139C'
249-input: PC Speaker as /class/input/input2
250-ACPI: PCI Interrupt 0000:00:01.4[B] -> Link [LNKB] ...
251-hdb: ATAPI 48X DVD-ROM DVD-R-RAM CD-R/RW drive, 2048kB Cache, UDMA(66)
```

&emsp;&emsp;根据文件内容递归查找目录：

``` bash
# 在当前目录搜索带“energywise”行的文件
$ grep 'energywise' *
# 在当前目录及其子目录下搜索“energywise”行的文件
$ grep -r 'energywise' *  
# 在当前目录及其子目录下搜索“energywise”行的文件，但是不显示匹配的行，只显示匹配的文件
$ grep -l -r 'energywise' *
```

### grep与正规表达式

&emsp;&emsp;字符类的搜索：如果我想要搜寻`test`或`taste`这两个单词时，可以发现到它们有共同的`t?st`。此时可以这样来搜寻：

``` bash
$ grep -n 't[ae]st' regular_express.txt
8:I can't finish the test.
9:Oh! The soup taste good.
```

其实`[]`里面不论有几个字节，它都仅代表某一个字节，所以上面的例子说明了，我需要的字串是`tast`或`test`两个字串而已。
&emsp;&emsp;字符类的反向选择`[^]`：如果想要搜索到有`oo`的行，但不想要`oo`前面有`g`：

``` bash
$ grep -n '[^g]oo' regular_express.txt
2:apple is my favorite food.
3:Football game is not use feet only.
18:google is the best tools for search keyword.
```

第`2`和`3`行没有疑问，因为`foo`与`Foo`均可被接受；但是第`18`行却有`google`的`goo`，因为该行后面出现了`tool`的`too`，所以该行也被列出来。也就是说，第`18`行里面虽然出现了我们所不要的项目(`goo`)，但是由于有需要的项目(`too`)，因此是符合字串搜寻。
&emsp;&emsp;字符类的连续：假设在`oo`前面不想要有小写字节，可以这样写`[^abcd....z]oo`，但是这样似乎不怎么方便，由于小写字节的`ASCII`上编码的顺序是连续的，因此可以将之简化为如下：

``` bash
grep -n '[^a-z]oo' regular_express.txt
```

也就是说，当我们在一组集合字节中，如果该字节组是连续的，例如大写英文/小写英文/数字等等，就可以使用`[a-z]`、`[A-Z]`、`[0-9]`等方式来书写。那么如果我们的要求字串是数字与英文，那就将它们全部写在一起，变成`[a-zA-Z0-9]`。我们要取得有数字的那一行：

``` bash
grep -n '[0-9]' regular_express.txt
```

&emsp;&emsp;行首字符：如果我想要让`the`只在行首列出呢？这个时候就得要使用定位字节了！可以这样做：

``` bash
grep -n '^the' regular_express.txt
```

&emsp;&emsp;如果我想要开头是小写字节的那一行列出呢？可以这样做：

``` bash
grep -n '^[a-z]' regular_express.txt
```

&emsp;&emsp;如果我不想要开头是英文字母，则可以是这样：

``` bash
grep -n '^[^a-zA-Z]' regular_express.txt
```

`^`在`[]`内代表`反向选择`，在`[]`之外则代表定位在行首的意义。
&emsp;&emsp;那如果我想要找出来行尾结束为小数点`.`的那一行：

``` bash
grep -n '\.$' regular_express.txt
```

特别注意到，因为小数点具有其他意义，所以必须要使用转义字符`\`来加以解除其特殊意义。
&emsp;&emsp;找出空白行：

``` bash
grep -n '^$' regular_express.txt
```

因为只有行首跟行尾`^$`，所以这样就可以找出空白行了。
&emsp;&emsp;任意一个字节`.`与重复字节`*`这两个符号在正则表达式的意义如下：

- `.` (小数点)：代表`一定有一个任意字节`的意思。
- `*` (星号)：代表`重复前一个字符，0到无穷多次`的意思，为组合形态。

&emsp;&emsp;假设需要找出`g??d`的字串，亦即共有四个字节，起头是`g`而结束是`d`，可以这样做：

``` bash
grep -n 'g..d' regular_express.txt
```

&emsp;&emsp;如果想要列出有`oo`、`ooo`、`oooo`等等的数据，也就是说至少要有两个`o`，该怎么办？因为`*`代表的是`重复0个或多个前面的RE字符`的意义，因此`o*`代表的是`拥有空字节或一个“o”以上的字节`，因此`grep -n 'o*' regular_express.txt`将会把所有的数据都列印出来终端上。当我们需要`至少两个“o”以上的字串`时：

``` bash
grep -n 'ooo*' regular_express.txt
```

&emsp;&emsp;如果想要字串开头与结尾都是`g`，但是两个`g`之间能存在至少一个`o`，亦即是`gog`、`goog`、`gooog...`等：

``` bash
grep -n 'goo*g' regular_express.txt
```

&emsp;&emsp;如果想要找出`g`开头与`g`结尾的行，当中的字符可有可无：

``` bash
grep -n 'g.*g' regular_express.txt
```

这个`.*`的`RE`表示任意字符。
&emsp;&emsp;我们可以利用`.`与`RE`字符及`*`来配置`0`个到无限多个重复字节，那如果想要限制一个范围区间内的重复字节数呢？这时候就得要使用到限定范围的字符`{}`了。 但因为`{`与`}`的符号在`shell`是有特殊意义的，因此必须要使用字符`\`来让它失去特殊意义才行。
&emsp;&emsp;假设要找到两个`o`的字串：

``` bash
grep -n 'o\{2\}' regular_express.txt
```

&emsp;&emsp;假设要找出`g`后面接`2`到`5`个`o`，然后再接一个`g`的字串，可以是这样：

``` bash
grep -n 'go\{2,5\}g' regular_express.txt
```

&emsp;&emsp;如果想要的是`2`个`o`以上的`goooo....g`呢？除了可以是`gooo*g`，也可以是：

``` bash
grep -n 'go\{2,\}g' regular_express.txt
```