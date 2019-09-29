---
title: Linux基本文本处理
categories: Shell编程
abbrlink: 128720d6
date: 2019-01-22 08:41:35
---
### fold命令

&emsp;&emsp;`fold`指令会从指定的文件里读取内容，将超过限定列宽的列加入增列字符后，输出到标准输出设备。若不指定任何文件名称，或是所给予的文件名为`-`，则`fold`指令会从标准输入设备读取数据。

``` bash
fold [-bs] [-w <每行列数>] [--help] [--version] [文件...]
```

- `-b`或`--bytes`：以`Byte`为单位计算列宽，而非采用列数编号为单位。
- `-s`或`--spaces`：以空格字符作为换列点。
- `-w <每行列数>`或`--width <每行列数>`：设置每行的最大列数。
- `--help`：在线帮助。
- `--version`：显示版本信息。

&emsp;&emsp;将一个名为`testfile`的文件的行折叠成宽度为`30`，可使用如下命令：

``` bash
fold -w 30 testfile
```

为了对比，先将`testfile`文件输出如下：

``` bash
$ cat testfile  # 查看testfile中的内容
Linux networks are becoming more and more common, but
security is often an overlooked
issue. Unfortunately, in today’s environment all networks
are potential hacker targets,
from top-secret military research networks to small home LANs.
Linux Network Security focuses on securing Linux in a
networked environment, where the
security of the entire network needs to be considered
rather than just isolated machines.
It uses a mix of theory and practical techniques to
teach administrators how to install and
use security applications, as well as how the
applications work and why they are necessary.
```

然后使用`fold`命令折叠显示：

``` bash
$ fold -w 30 testfile  # 行折叠成宽度为30，显示testfile文件
Linux networks are becoming mo
re and more common, but securi
ty is often an overlooked issu
e. Unfortunately, in today’s
environment all networks are
potential hacker targets, from
top-secret military research
networks to small home LANs.
Linux Network Security focuses
on securing Linux in a networ
ked environment, where the sec
urity of the entire network ne
eds to be considered rather th
an just isolated machines. It
uses a mix of theory and pract
ical techniques to teach admin
istrators how to install and u
se security applications, as w
ell as how the applications wo
rk and why they are necessary
```

### fmt命令

&emsp;&emsp;`fmt`指令会从指定的文件里读取内容，将其依照指定格式重新编排后，输出到标准输出设备。若指定的文件名为`-`，则`fmt`指令会从标准输入设备读取数据。

``` bash
fmt [-cstu] [-w <每行字符数>] [--help] [--version] [文件...]
```

- `-c`或`--crown-margin`：每段前两列缩排。
- `-s`或`--split-only`：只拆开字数超出每列字符数的行，但不合并字数不足每列字符数的行。
- `-t`或`--tagged-paragraph`：每列前两列缩排，但第`1`列和第`2`列的缩排格式不同。
- `-u`或`--uniform-spacing`：每个字符之间都以一个空格字符间隔，每个句子之间则两个空格字符分隔。
- `-w <每行字符数>`：设置每行的最大字符数。
- `--help`：在线帮助。
- `--version`：显示版本信息。

&emsp;&emsp;先使用`cat`命令查看文件内容：

``` bash
$ cat testfile  # 查看testfile文件的内容
hello Linux!
Linux is a free Unix-type operating system.
This is a Linux testfile!
Linux
Linux
```

使用`fmt`命令重排之后，输出结果如下：

``` bash
$ fmt -w 85 testfile  # 指定重排宽度为85个字符
hello Linux! Linux is a free Unix-type operating system. This is a Linux testfile!
Linux Linux
```

### rev命令

&emsp;&emsp;`rev`命令将文件中的每行内容以字符为单位反序输出，即第一个字符最后输出，最后一个字符最先输出，依次类推。

``` bash
rev [参数]
```

参数为指定要反序显示内容的文件。

### pr命令

&emsp;&emsp;将文本文件内容转换成适合打印的格式：

``` bash
pr [选项] [文件]
```

- `+首页[:末页]`、`--pages=首页[:末页]`：在指定的`首页/末页`处`开始/停止`打印。
- `-列数`、`--columns=列数`：输出指定的列数。如果指定了`-a`选项，则从上到下列印。程序会自动在每一页均衡每列占用的行数。
- `-a`、`--across`：设置每列从上到下输出，配合`-列数`选项一起使用。
- `-c`、`--show-control-chars`：使用头标`^G`和八进制反斜杠标记。
- `-d`、`--double-space`：加倍输出空白区域。
- `-D`、`--date-format=格式`：使用遵循指定格式的页眉日期。
- `-e[字符[宽度]]`、`--expand-tabs[=字符[宽度]]`：扩展输入的字符(`TAB`)到制表符宽度(`8`)。
- `-F`、`-f`、`--form-feed`：使用出纸页页标代替新行作为页面间的分隔符(使用`-F`选项时报头为`3`行，不使用时为`5`行)。
- `-h`、`--header=页眉`：在页眉中使用居中的指定字符代替文件名，`-h ""`输出一个空行，不要使用`-h""`。
- `-i[字符[宽度]]`、`--output-tabs[=字符[宽度]]`：使用指定字符(或`TAB`)代替空格不足到指定制表符宽度(默认`8`)。
- `-J`、`--join-lines`：合并整个行，关闭`-W`选项的行截断，不使用栏调整，使用`--sep-string[=字符串]`设置分隔符。
- `-l`、`--length=页长`：使用指定页长的行数(默认`66`)(默认文本行数为`56`，当启用`-F`时为`63`)。
- `-m`、`--merge`：在同一行显示所有文件，每个文件占用一栏，分割行，但是当使用`-J`时将行合并到完整长度。
- `-n[分隔符[位数]]`、`--number-lines[=分隔符[位数]]`：显示行号，使用指定(默认`5`)位数，后接分隔符(默认`TAB`)，默认从输入文件的第一行开始计数。
- `-N`、`--first-line-number=数字`：从首页的首行以指定数字开始计数。
- `-o`、`--indent=缩进量`：将每行缩进(默认`0`)个空格，不影响`-w`或`-W`参数，缩进量的值将被加入页面宽度。
- `-r`、`--no-file-warnings`：当文件无法打开时忽略警告。
- `-s[CHAR]`、`--separator[=CHAR]`：由单个字符分隔各列，未附加`-w`时默认为制表符，否则为空。另外除非`-w`选项被指定，否则`-s[CHAR]`会屏蔽三个列相关的截行选项(`-COLUMN|-a -COLUMN|-m`)。
- `-S字符串`、`--sep-string[=字符串]`：使用指定的字符串分栏，不使用`-S`则使用默认的制表符`TAB`作为分隔符，与`-J`和空格一起使用(等于`-S" "`)对分栏选项无影响。
- `-t`、`--omit-header`：忽略页眉和页脚。
- `-T`、`--omit-pagination`：按照输入文件中的设置忽略页眉和页脚并除去所有分页记号。
- `-v`、`--show-nonprinting`：使用八进制反斜杠标记。
- `-w`、`--width=页面宽度`：为多栏页面输出将设置为指定的字符数(默认`72`)，仅当`-s[char]`选项不启用时有效(即保持默认值`72`)。
- `-W`、`--page-width=页宽`：总是将页宽设置为指定的(默认`72`)字符数，除非`-J`选项启用总是截断行，此参数与`-S`或`-s`冲突。
- `--help`：显示此帮助信息并退出。
- `--version`：显示版本信息并退出。

如果页长小于等于`10`，则使用`-t`选项。如果`FILE`没有定义或者`FILE`是`-`，则从标准输入读入。

### sort命令

&emsp;&emsp;`sort`命令是依据不同的数据类型进行排序：

``` bash
sort [-bcfMnrtk] [源文件] [-o 输出文件]
```

`sort`可针对文本文件的内容以行为单位来排序。

- `-b`：忽略每行前面开始出的空格字符。
- `-c`：检查文件是否已经按照顺序排序。
- `-f`：排序时，忽略大小写字母。
- `-M`：将前面`3`个字母依照月份的缩写进行排序。
- `-n`：依照数值的大小排序。
- `-o <输出文件>`：将排序后的结果存入指定的文件。
- `-r`：以相反的顺序来排序。
- `-t <分隔字符>`：指定排序时所用的栏位分隔字符。
- `-k`：选择以哪个区间进行排序。

&emsp;&emsp;下面通过几个例子来讲述`sort`的使用：
&emsp;&emsp;1. `sort`将文件的每一行作为一个单位，相互比较，比较原则是从首字符向后，依次按`ASCII`码值进行比较，最后将它们按升序输出。

``` bash
$ cat seq.txt
banana
apple
pear
orange
$ sort seq.txt
apple
banana
orange
pear
```

用户可以保存排序后的文件内容，或把排序后的文件内容输出至打印机。下例中用户把排序后的文件内容保存到名为`result`的文件中：

``` bash
sort seq.txt > result
```

&emsp;&emsp;2. `sort`的`-u`选项作用很简单，就是在输出中去除重复行。
&emsp;&emsp;3. `sort`默认的排序方式是升序，如果想改成降序，就加个`-r`就搞定了。
&emsp;&emsp;4. `sort`默认是把结果输出到标准输出，所以需要用重定向才能将结果写入文件，例如`sort filename > newfile`。但如果你想把排序结果输出到原文件中，用重定向可就不行了，而`-o`选项就是为了解决这一问题。
&emsp;&emsp;5. `-n`选项告诉`sort`要以数值来排序。
&emsp;&emsp;6. `sort`的`-t`和`-k`选项：如果有一个文件的内容是这样：

``` bash
banana:30:5.5
apple:10:2.5
pear:90:2.3
orange:20:3.4
```

这个文件有三列，列与列之间用冒号隔开。第一列表示水果类型，第二列表示水果数量，第三列表示水果价格。那么我想以水果数量来排序，也就是以第二列来排序，如何利用`sort`实现？幸好，`sort`提供了`-t`选项，后面可以设定间隔符。指定了间隔符之后，就可以用`-k`来指定列数了。

``` bash
$ sort -n -k 2,2 -t ':' file
apple:10:2.5
orange:20:3.4
banana:30:5.5
pear:90:2.3
```

&emsp;&emsp;7. 其他的`sort`常用选项：

- `-i`：仅仅比较可打印字符。
- `-R`：根据哈希值随机排序。
- `-m`：仅仅合并已经排序好的文件，不执行排序操作。

&emsp;&emsp;**补充说明**：
&emsp;&emsp;1. 在`sort`命令中，一个文本行最多包含`10`列。
&emsp;&emsp;2. `-k`选项的语法为`-k pos1[, pos2]`，`pos1`表示开始列，`pos2`表示结束列；`-k pos`表示从`pos`列开始一直到文本列的结束；`-k pos,pos`表示在第`pos`列。
&emsp;&emsp;3. `-r`选项是对所有的关键字进行降序排序，而`-k pos1(r)[,pos2(r)]`是对特定的关键字进行降序排序。`-n`和`-r`具有类似的属性。
&emsp;&emsp;4. `-k`选项能够使用多个修饰符，例如`-k pos1(nr)[,pos2(nr)]`。

### wc命令

&emsp;&emsp;利用`wc`命令我们可以计算文件的`Byte`数、字数、或是列数。若不指定文件名称、或是所给予的文件名为`-`，则`wc`命令会从标准输入设备读取数据。

``` bash
wc [-clw] [--help] [--version] [文件...]
```

- `-c`、`--bytes`或`--chars`：只显示`Bytes`数。
- `-l`或`--lines`：只显示列数。
- `-w`或`--words`：只显示字数。
- `--help`：在线帮助。
- `--version`：显示版本信息。

&emsp;&emsp;先查看`testfile`文件的内容：

``` bash
$ cat testfile
Linux networks are becoming more and more common, but scurity is often an overlooked
issue. Unfortunately, in today’s environment all networks are potential hacker targets,
fro0m tp-secret military research networks to small home LANs.
Linux Network Securty focuses on securing Linux in a networked environment, where the
security of the entire network needs to be considered rather than just isolated machines.
It uses a mix of theory and practicl techniques to teach administrators how to install and
use security applications, as well as how the applcations work and why they are necesary.
```

使用`wc`统计：

``` bash
$ wc testfile  # testfile文件的统计信息
3 92 598 testfile  # testfile文件的行数为3、单词数92、字节数598
```

### cut命令

&emsp;&emsp;`cut`是一个选取命令，就是将一段数据经过分析，取出我们想要的。一般来说，选取信息通常是针对`行`来进行分析的，并不是整篇信息分析的。

``` bash
cut [-bn] [file]
cut [-c] [file]
cut [-df] [file]
```

`cut`命令从文件的每一行剪切字节、字符和字段并将这些内容写至标准输出。如果不指定`File`参数，`cut`命令将读取标准输入。

- `-b`：以字节为单位进行分割。这些字节位置将忽略多字节字符边界，除非也指定了`-n`标志。
- `-c`：以字符为单位进行分割。
- `-d`：自定义分隔符，默认为制表符。
- `-f`：与`-d`一起使用，指定显示哪个区域。
- `-n`：取消分割多字节字符，仅和`-b`标志一起使用。
- `-s`：不输出不包含列分隔符的行。

&emsp;&emsp;`cut`命令主要是接受三个定位方法：字节(`bytes`)用选项`-b`，字符(`characters`)用选项`-c`，域(`fields`)用选项`-f`。

``` bash
cut -b 3  # 如果想提取每一行的第3个字节
cut -b 3-5,8  # 如果想提取第3、第4、第5和第8个字节，注意顺序不能颠倒
cut -b -3  # 表示从第一个字节到第三个字节
cut -b 3-  # 表示从第三个字节到行尾
```

当遇到多字节字符时，可以使用`-n`选项，`-n`用于告诉`cut`不要将多字节字符拆开。
&emsp;&emsp;为什么会有`域`的提取呢？因为刚才提到的`-b`和`-c`只能在固定格式的文档中提取信息，而对于非固定格式的信息则束手无策，这时候`域`就派上用场了。如果你观察过`/etc/passwd`文件就会发现，它并不像`who`的输出信息那样具有固定格式，而是比较零散的排放。但是冒号在这个文件的每一行中都起到了非常重要的作用，冒号用来隔开每一个项。
&emsp;&emsp;`cut`命令提供了这样的提取方式，具体的说就是设置`间隔符`，再设置`提取第几个域`就`OK`了。以`/etc/passwd`的前五行内容为例：

``` bash
$ cat /etc/passwd | head -n 5
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
$ cat /etc/passwd | head -n 5 | cut -d : -f 1
root
bin
daemon
adm
lp
```

用`-d`来设置间隔符为冒号，然后用`-f`来设置要取的是第一个域，于是所有的用户名就都列出来了！当然，在设定`-f`时也可以使用例如`3-5`或者`4-`类似的格式。

### paste命令

&emsp;&emsp;`paste`命令会把每个文件以列对列的方式，一列列地加以合并。

``` bash
paste [-s] [-d <间隔字符>] [--help] [--version] [文件]
```

- `-d <间隔字符>`或`--delimiters=<间隔字符>`：用指定的间隔字符取代跳格字符。
- `-s`或`--serial`：串列进行而非平行处理。
- `--help`：在线帮助。
- `--version`：显示帮助信息。

&emsp;&emsp;使用`paste`指令将文件`file`、`testfile`和`testfile1`进行合并。首先使用`cat`命令查看`3`个文件内容：

``` bash
$ cat file  # file文件的内容
xiongdan 200
lihaihui 233
lymlrl 231
$ cat testfile  # testfile文件的内容
liangyuanm  ss
$ cat testfile1  # testfile1文件的内容
huanggai 56
zhixi 73
```

当合并指令执行后，程序界面中将显示合并后的文件内容：

``` bash
xiongdan 200
lihaihui 233
lymlrl 231
liangyuanm  ss
huanggai 56
zhixi 73
```

若使用`paste`指令的参数`-s`，则可以将一个文件中的多行数据合并为一行进行显示。例如，将文件`file`中的`3`行数据合并为一行数据进行显示：

``` bash
paste -s file  # 合并指定文件的多行数据
```

上面的命令执行后，显示的数据内容如下：

``` bash
xiongdan 200 lihaihui 233 lymlrl 231
```

注意，参数`-s`只是将`testfile`文件的内容调整显示方式，并不会改变原文件的内容格式。

### join命令

&emsp;&emsp;`join`命令用于将两个文件中指定列位内容相同的行加以合并，再输出到标准输出设备：

``` bash
join [-i] [-a<1或2>] [-e <字符串>] [-o <格式>] [-t <字符>] [-v <1或2>] \
     [-1 <列位>] [-2 <列位>] [--help] [--version] [文件1] [文件2]
```

- `-a <1或2>`：除了显示原来的输出内容之外，还显示指令文件中没有相同栏位的行。
- `-e <字符串>`：若`文件1`与`文件2`中找不到指定的栏位，则在输出中填入选项中的字符串。
- `-i`或`--igore-case`：比较栏位内容时，忽略大小写的差异。
- `-o <格式>`：按照指定的格式来显示结果。
- `-t <字符>`：使用栏位的分隔字符。
- `-v <1或2>`：跟`-a`相同，但是只显示文件中没有相同栏位的行。
- `-1 <列位>`：连接`文件1`指定的栏位。
- `-2 <列位>`：连接`文件2`指定的栏位。
- `--help`：显示帮助。
- `--version`：显示版本信息。

&emsp;&emsp;首先查看`testfile_1`、`testfile_2`中的文件内容：

``` bash
$ cat testfile_1  # testfile_1文件中的内容
Hello 95  # 例如，本例中第一列为姓名，第二列为数额
Linux 85
test 30
$ cat testfile_2  # testfile_2文件中的内容
Hello 2005  # 例如，本例中第一列为姓名，第二列为年份
Linux 2009
test 2006
```

然后以默认的方式比较两个文件，将两个文件中指定字段的内容相同的行连接起来，在终端中输入命令：

``` bash
$ join testfile_1 testfile_2  # 连接testfile_1、testfile_2中的内容
Hello 95 2005  # 连接后显示的内容
Linux 85 2009
test 30 2006
```

文件`1`与文件`2`的位置对输出到标准输出的结果是有影响的。例如将命令中的两个文件互换：

``` bash
$ join testfile_2 testfile_1  # 改变文件顺序连接两个文件
Hello 2005 95  # 连接后显示的内容
Linux 2009 85
test 2006 30
```

### tr命令

&emsp;&emsp;通过使用`tr`，可以非常容易地实现`sed`许多最基本功能，可以将`tr`看作为`sed`的简化的变体：它可以用一个字符来替换另一个字符，或者可以完全除去一些字符，也可以用它来除去重复字符。
&emsp;&emsp;`tr`用来从标准输入中通过替换或删除操作进行字符转换，`tr`主要用于删除文件中控制字符或进行字符转换。使用`tr`时要转换两个字符串：字符串`1`用于查询，字符串`2`用于处理各种转换。`tr`刚执行时，字符串`1`中的字符被映射到字符串`2`中的字符，然后转换操作开始。

``` bash
tr -c -d -s ["string1_to_translate_from"] ["string2_to_translate_to"] < input-file
```

- `-c`：用字符串`1`中字符集的补集替换此字符集，要求字符集为`ASCII`。
- `-d`：删除字符串`1`中所有输入字符。
- `-s`：删除所有重复出现字符序列，只保留第一个；即将重复出现字符串压缩为一个字符串。
- `input-file`：它是转换文件名。

&emsp;&emsp;指定字符串`1`或字符串`2`的内容时，只能使用单字符或字符串范围或列表。

- `[a-z]`：`a`至`z`内的字符组成的字符串。
- `[A-Z]`：`A`至`Z`内的字符组成的字符串。
- `[0-9]`：数字串。
- `\octal`：一个三位的八进制数，对应有效的`ASCII`字符。
- `[O*n]`：表示字符`O`重复出现指定次数`n`，因此`[O*2]`匹配`OO`的字符串。

&emsp;&emsp;1. 将文件`file`中出现的`abc`替换为`xyz`：

``` bash
cat file | tr "abc" "xyz" > new_file
```

注意，这里凡是在`file`中出现的`a`都替换成`x`，`b`替换为`y`，`c`替换为`z`，而不是将字符串`abc`替换为字符串`xyz`。
&emsp;&emsp;2. 使用`tr`命令`统一`字母大小写：

``` bash
cat file | tr [a-z] [A-Z] > new_file  # 小写 -> 大写
cat file | tr [A-Z] [a-z] > new_file  # 大写 -> 小写
```

&emsp;&emsp;3. 把文件中的数字`0`至`9`替换为`a`至`j`：

``` bash
cat file | tr [0-9] [a-j] > new_file
```

&emsp;&emsp;4. 删除文件`file`中出现的`Snail`字符：

``` bash
cat file | tr -d "Snail" > new_file
```

注意，这里凡是在`file`文件中出现的`S`、`n`、`a`、`i`和`l`字符都会被删除，而不是仅仅删除出现的`Snail`字符串。
&emsp;&emsp;5. 删除文件`file`中出现的换行`\n`、制表`\t`字符：

``` bash
cat file | tr -d "\n\t" > new_file
```

不可见字符都得用转义字符来表示的，这个都是统一的。
&emsp;&emsp;6. 删除`连续的`重复字母，只保留第一个：

``` bash
cat file | tr -s [a-zA-Z] > new_file
```

&emsp;&emsp;7. 删除空行：

``` bash
cat file | tr -s "\n" > new_file
```

&emsp;&emsp;8. 删除`Windows`文件造成的`^M`字符：

``` bash
cat file | tr -d "\r" > new_file
# 或者
cat file | tr -s "\r" "\n" > new_file
```

注意，这里`-s`后面是两个参数`\r`和`\n`，用后者替换前者。
&emsp;&emsp;9. 用空格符`\040`替换制表符`\011`：

``` bash
cat file | tr -s "\011" "\040" > new_file
```

&emsp;&emsp;10. 把路径变量中的冒号`:`替换成换行符`\n`：

``` bash
echo $PATH | tr -s ":" "\n"
```