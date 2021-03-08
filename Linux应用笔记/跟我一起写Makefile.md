---
title: 跟我一起写Makefile
date: 2020-03-04 22:36:55
categories: Linux应用笔记
---
### 概述

&emsp;&emsp;什么是`makefile`？或许很多`Windows`的程序员都不知道这个东西，因为那些`Windows`的`IDE`都为你做了这个工作。但是，在`Unix`下进行软件编译，你就不能不自己写`makefile`了。会不会写`makefile`，从一个侧面说明了一个人是否具备完成大型工程的能力，因为`makefile`关系到了整个工程的编译规则。一个工程中的源文件不计数，其按类型、功能、模块分别放在若干个目录中，`makefile`定义了一系列的规则来指定哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作，因为`makefile`就像`Shell`脚本一样，其中也可以执行操作系统的命令。<!--more-->
&emsp;&emsp;`makefile`带来的好处就是`自动化编译`，只需要一个`make`命令，整个工程完全自动编译，极大地提高了软件开发的效率。`make`是一个命令工具，是一个解释`makefile`中指令的命令工具。一般来说，大多数的`IDE`都有这个命令，比如`Visual C++`的`nmake`以及`Linux`的`GNU make`，这里我仅对`GNU make`进行讲述。

### 关于程序的编译和链接

&emsp;&emsp;关于程序编译的一些规范和方法：一般来说，无论是`C`语言，还是`C++`，首先要把源文件编译成中间代码文件。在`Windows`下是`.obj`文件，在`UNIX`下是`.o`文件，即`Object File`，这个动作叫做编译(`compile`)。然后，再把大量的`Object File`合成执行文件，这个动作叫作链接(`link`)。
&emsp;&emsp;在编译时，编译器需要的是语法的正确，函数与变量的声明的正确。对于后者，通常需要告诉编译器头文件的所在位置(头文件中应该只是声明，而定义应该放在`C/C++`文件中)，只要所有的语法正确，编译器就可以编译出中间目标文件。一般来说，每个源文件都应该对应于一个中间目标文件(`.o`文件或是`.obj`文件)。
&emsp;&emsp;在链接时，主要是链接函数和全局变量，所以我们可以使用这些中间目标文件来链接我们的应用程序。链接器并不管函数所在的源文件，只管函数的中间目标文件。在大多数时候，由于源文件太多，导致编译生成的中间目标文件太多，而在链接时需要明显地指出中间目标文件名，这对于编译很不方便，所以我们要给中间目标文件打个包。在`Windows`下，这种包叫`库文件`(`Library File`)，也就是`.lib`文件；在`UNIX`下，叫做`Archive File`，也就是`.a`文件。
&emsp;&emsp;总结一下，源文件首先会生成中间目标文件，再由中间目标文件生成执行文件。在编译时，编译器只检测程序语法，以及函数、变量是否被声明。如果函数未被声明，编译器会给出一个警告，但可以生成`Object File`。而在链接程序时，链接器会在所有的`Object File`中找寻函数的实现。如果找不到，那到就会报链接错误码(`Linker Error`)：在`VC`下，这种错误一般是`Link 2001`错误，意思说是链接器未能找到函数的实现。

### Makefile介绍

&emsp;&emsp;`make`命令在执行时，需要一个`Makefile`文件，用来告诉`make`命令需要怎样去编译和链接程序。首先用一个示例来说明`Makefile`的书写规则，以便给大家一个感性的认识。在这个示例中，我们的工程有`8`个`.c`文件和`3`个`.h`文件，需要写一个`Makefile`来告诉`make`命令如何编译和链接这几个文件。规则如下：

1. 如果这个工程没有编译过，那么我们的所有`.c`文件都要编译并被链接。
2. 如果这个工程的某几个`.c`文件被修改，那么我们只编译被修改的`.c`文件，并链接目标程序。
3. 如果这个工程的`.h`文件被改变了，那么我们需要编译引用了这几个`.h`文件的`.c`文件，并链接目标程序。

&emsp;&emsp;只要`Makefile`写得够好，对于所有这一切，只用一个`make`命令就可以完成。`make`命令会智能地根据当前的文件修改的情况，来确定哪些文件需要重编译，从而自己编译所需要的文件和链接目标程序。

#### Makefile的规则

&emsp;&emsp;在讲述这个`Makefile`之前，先来粗略地看一看`Makefile`的规则：

``` makefile
target : prerequisites
    command
```

- `target`：目标文件，可以是`Object File`，也可以是执行文件，甚至是一个标签`Label`。
- `prerequisites`：生成`target`所需要的文件或者目标。
- `command`：`make`需要执行的命令，它可以是任意的`Shell`命令。

&emsp;&emsp;这是一个文件的依赖关系，也就是说`target`这一个或多个的目标文件依赖于`prerequisites`中的文件，其生成规则定义在`command`中。在`prerequisites`中，如果有一个以上的文件比`target`文件要新的话，`command`所定义的命令就会被执行。这就是`Makefile`的规则，也就是`Makefile`中最核心的内容。

#### 一个示例

&emsp;&emsp;正如前面所说的，如果一个工程有`3`个头文件和`8`个`C`文件，我们为了完成前面所述的那三个规则，我们的`Makefile`应该是下面的这个样子的：

``` makefile
edit : main.o kbd.o command.o display.o insert.o search.o files.o utils.o
    cc -o edit main.o kbd.o command.o display.o insert.o search.o files.o utils.o

main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit main.o kbd.o command.o display.o insert.o search.o files.o utils.o
```

我们可以把这个内容保存在文件名为`Makefile`或`makefile`的文件中，然后在该目录下直接输入命令`make`就可以生成执行文件`edit`。如果要删除执行文件和所有的中间目标文件，只要执行一下`make clean`就可以了。
&emsp;&emsp;在这个`makefile`中，目标文件(`target`)包含执行文件`edit`和中间目标文件(`*.o`)。依赖文件(`prerequisites`)就是冒号后面的那些`.c`文件和`.h`文件。每一个`.o`文件都有一组依赖文件，而这些`.o`文件又是执行文件`edit`的依赖文件。依赖关系的实质上就是说明了目标文件是由哪些文件生成的，换言之目标文件是由哪些文件更新的。
&emsp;&emsp;在定义好依赖关系后，后续的那一行定义了如何生成目标文件的操作系统命令，一定要以一个`Tab`作为开头。记住，`make`并不管命令是怎么工作的，它只管执行所定义的命令。`make`会比较`target`文件和`prerequisites`文件的修改日期，如果`prerequisites`文件的日期要比`target`文件的要新，或者`target`不存在的话，那么`make`就会执行后续定义的命令。
&emsp;&emsp;这里要说明一点的是，`clean`不是一个文件，它只不过是一个动作名字，其冒号后什么也没有，那么`make`就不会自动去找文件的依赖性，也就不会自动执行其后所定义的命令。要执行其后的命令，就要在`make`命令后指出这个`lable`的名字。这样的方法非常有用，我们可以在一个`makefile`中定义和编译无关的命令，比如程序的打包命令等。

#### make是如何工作的

&emsp;&emsp;在只输入`make`命令的情况下，`make`会进行如下动作：

1. `make`会在当前目录下寻找名字是`Makefile`或`makefile`的文件。
2. 如果找到，它会接着查找该文件中的第一个目标文件(`target`)。在上面的例子中，它会查找到`edit`，并把它作为最终的目标文件。
3. 如果`edit`文件不存在，或是`edit`所依赖的后面的`.o`文件的文件修改时间要比`edit`新，那么它就会执行后面所定义的命令来生成`edit`这个文件。
4. 如果`edit`所依赖的`.o`文件也不存在，那么`make`会在当前文件中找目标为`.o`文件的依赖性。如果找到，则再根据那一个规则生成`.o`文件(这有点像一个堆栈的过程)。
5. 当然，你的`.c`文件和`.h`文件是存在的，于是`make`会生成`.o`文件。然后，再用`.o`文件生成`make`的终极目标文件，也就是执行文件`edit`。

&emsp;&emsp;这就是`make`的依赖性，`make`会一层又一层地去找文件的依赖关系，直到最终编译出第一个目标文件。在找寻的过程中，如果出现错误(比如最后被依赖的文件找不到)，那么`make`就会直接退出并且报错。而对于所定义的命令的错误或是编译不成功，`make`根本不理会。
&emsp;&emsp;通过上述分析，可以得知：`clean`没有被第一个目标文件直接或间接关联，那么它后面所定义的命令将不会被自动执行。但是，我们可以显式地让`make`执行该命令，即`make clean`，以清除所有的目标文件。
&emsp;&emsp;如果这个工程已经被编译过了，当我们修改其中一个源文件(例如`file.c`)，那么根据依赖性，目标文件`file.o`会被重新编译(也就是在这个依性关系后面所定义的命令)。于是`file.o`的文件修改时间被更新，而它的文件修改时间要比`edit`要新，所以`edit`也会被重新链接了。
&emsp;&emsp;如果我们改变了`command.h`，那么`kdb.o`、`command.o`和`files.o`都会被重编译，并且`edit`会被重新链接。

#### makefile中使用变量

&emsp;&emsp;先看看`edit`的规则：

``` makefile
edit : main.o kbd.o command.o display.o insert.o search.o files.o utils.o
    cc -o edit main.o kbd.o command.o display.o insert.o search.o files.o utils.o
```

可以看到`.o`文件的字符串重复了两次，如果我们的工程需要加入一个新的`.o`文件，那么需要在两个地方加(应该是三个地方，还有一个地方在`clean`中)。如果`makefile`很复杂，那么就有可能会忘掉一个需要加入的地方，而导致编译失败。所以为了更好地维护`makefile`，可以在`makefile`使用变量。
&emsp;&emsp;`makefile`的变量也就是一个字符串，理解成`C`语言中的`宏`可能会更好。例如，声明一个变量叫做`objects`，在`makefile`一开始就这样定义：

``` makefile
objects = main.o kbd.o command.o display.o insert.o search.o files.o utils.o
```

于是，就可以很方便地在`makefile`中以`$(objects)`的方式来使用这个变量了。改良版`makefile`如下：

``` makefile
objects = main.o kbd.o command.o display.o insert.o search.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)
main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit $(objects)
```

如果有新的`.o`文件加入，只需简单地修改一下`objects`变量即可。

#### 让make自动推导

&emsp;&emsp;`GNU`的`make`很强大，它可以自动推导文件以及文件依赖关系后面的命令，就没必要去在每一个`.o`文件后都写上类似的命令。只要`make`看到一个`.o`文件，它就会自动地把`.c`文件加在依赖关系中。例如如果`make`找到一个`whatever.o`，那么`make`就会认为`whatever.c`就会是`whatever.o`的依赖文件，使用的编译命令是`cc -c whatever.c`。于是`makefile`再也不用写得这么复杂了：

``` makefile
objects = main.o kbd.o command.o display.o insert.o search.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)

main.o : defs.h
kbd.o : defs.h command.h
command.o : defs.h command.h
display.o : defs.h buffer.h
insert.o : defs.h buffer.h
search.o : defs.h buffer.h
files.o : defs.h buffer.h command.h
utils.o : defs.h

.PHONY : clean
clean :
    rm edit $(objects)
```

注意，`.PHONY`表示`clean`是个伪目标文件，因此不需要推导`clean`的依赖关系。

#### 另类风格的makefile

&emsp;&emsp;既然`make`可以自动推导命令，那么重复的`.h`能不能收拢起来？答案是可以的，最新的`makefile`如下：

``` makefile
objects = main.o kbd.o command.o display.o insert.o search.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)

$(objects) : defs.h

kbd.o command.o files.o : command.h
display.o insert.o search.o files.o : buffer.h

.PHONY : clean
clean :
    rm edit $(objects)
```

这种风格让`makefile`变得很简单，但文件依赖关系就显得有点凌乱了。

#### 清空目标文件的规则

&emsp;&emsp;每个`Makefile`中都应该定义一个清空目标文件(`.o`文件和执行文件)的规则，这不仅便于重新编译，也很利于保持文件的清洁。一般的风格如下：

``` makefile
clean:
    rm edit $(objects)
```

更为稳健的做法如下：

``` makefile
.PHONY : clean
clean :
    -rm edit $(objects)
```

前面说过，`.PHONY`表示`clean`是一个`伪目标`。而在`rm`命令前面加了`-`的意思是：也许某些文件出现问题(例如不存在)，但不要管，继续执行`rm`命令。
&emsp;&emsp;当然，`clean`的规则不要放在文件的开头，不然就会变成`make`的默认目标。有一个不成文的规矩：`clean`从来都是放在文件的最后。上面就是一个`makefile`的概貌，也是`makefile`的基础。

---

### Makefile里有什么？

&emsp;&emsp;`Makefile`里主要包含如下五个东西：

- **显式规则**：显式规则说明了如何生成一个或多个目标文件，这是由`Makefile`的书写者明显指出的。
- **隐晦规则**：由于`make`有自动推导的功能，所以隐晦的规则可以使我们比较粗糙地简略地书写`Makefile`。
- **变量定义**：变量一般都是字符串，类似于`C`语言中的`宏`。当`Makefile`被执行时，其中的变量都会被扩展到相应的引用位置上。
- **文件指示**：其中包括了三个部分：

1. 在一个`Makefile`中引用另一个`Makefile`，就`C`语言中的`include`一样。
2. 根据某些情况指定`Makefile`中的有效部分，就像`C`语言中的预编译`#if`一样。
3. 定义一个多行的命令。

- **注释**：`Makefile`中只有`行注释`，和`UNIX`的`Shell`脚本一样，其注释使用字符`#`。如果你要在`Makefile`中使用`#`字符，可以用反斜框进行转义，如`\#`。

### Makefile的文件名

&emsp;&emsp;默认的情况下，`make`命令会在当前目录下寻找文件名为`makefile`、`Makefile`的文件。最好使用`Makefile`这个文件名，因为第一个字符为大写，有一种显目的感觉。
&emsp;&emsp;当然，你可以使用别的文件名来书写`Makefile`，比如`Make.Linux`和`Make.Solaris`。如果要指定特定的`Makefile`，你可以使用`make`的`-f`和`--file`参数：

``` bash
make -f Make.Linux
# 或者
make --file Make.AIX
```

### 引用其它的Makefile

&emsp;&emsp;在`Makefile`中，使用关键字`include`可以把别的`Makefile`包含进来，这很像`C`语言的`#include`：

``` makefile
include <filename>
```

例如有如下`Makefile`文件：`a.mk`、`b.mk`、`c.mk`，同时还有一个文件叫`foo.make`以及一个变量`$(bar)`，并且`$(bar)`包含了`e.mk`和`f.mk`。因此下面的语句：

``` makefile
include foo.make *.mk $(bar)
```

等价于：

``` makefile
include foo.make a.mk b.mk c.mk e.mk f.mk
```

&emsp;&emsp;`make`命令开始执行时，先去寻找`include`所指出的其它`Makefile`，并把其内容安置在当前的位置。如果文件没有指定`绝对路径`或`相对路径`，`make`会先在当前目录下寻找；如果当前目录下没有找到，那么`make`还会在下面的几个目录下寻找：

- 如果在`make`执行时，有参数`-I`或`--include-dir`，那么`make`就会在这个参数所指定的目录下去寻找。
- 如果目录`<prefix>/include`(一般是`/usr/local/bin`或`/usr/include`)存在的话，`make`也会去寻找。

&emsp;&emsp;如果有文件没有找到的话，`make`会生成一条警告信息，但不会马上出现致命错误，它会继续载入其它的文件。一旦完成`makefile`的读取，`make`会再重试这些没有找到或是不能读取的文件。如果还是不行，`make`才会出现一条致命信息。如果你想让`make`不理那些无法读取的文件而是继续执行，可以在`include`前加一个减号`-`：

``` makefile
-include <filename>
```

### 环境变量MAKEFILES

&emsp;&emsp;在这里，我还是建议不要使用环境变量`MAKEFILES`。因为只要这个变量被定义，那么当你使用`make`时，所有的`Makefile`都会受到它的影响，这绝不是你想看到的。有时候，你的`Makefile`可能出现了怪事，那么你可以看看当前环境中有没有定义这个变量。

---

### 书写规则

&emsp;&emsp;规则包含两个部分，一个是依赖关系，一个是生成目标的方法。在`Makefile`中，规则的顺序是很重要的，因为`Makefile`中应该只有一个最终目标，其它的标都是被这个目标所带出来的，所以一定要让`make`知道你的最终目标是什么。
&emsp;&emsp;一般来说，定义在`Makefile`中的目标可能会有很多，但是第一条规则中的目标将被确立为最终的目标。如果第一条规则中的目标有很多个，那么第一个目标会成为最终的目标，`make`所完成的也就是这个目标。

#### 规则的语法

&emsp;&emsp;`makefile`规则的语法如下：

``` makefile
targets : prerequisites
    command
# 或者
targets : prerequisites ; command_1
    command_2
```

- `targets`：目标文件名，以空格分开，可以使用通配符。一般来说，我们的目标基本上是一个文件，但也有可能是多个文件。
- `command`：命令行，如果不与`target:prerequisites`在一行，那么必须以`Tab`键开头。如果和`prerequisites`在一行，那么可以用`;`做为分隔。
- `prerequisites`：目标所依赖的文件(或依赖目标)。如果其中的某个文件要比目标文件要新，那么目标就被认为是`过时的`，需要重新生成。如果命令太长，可以使用反斜杠`\`作为换行符。

#### 在规则中使用通配符

&emsp;&emsp;如果我们想定义一系列比较类似的文件，很自然地就想起了使用通配符。`make`支持三个通配符：`*`、`?`和`[...]`。
&emsp;&emsp;波浪号`~`在文件名中也有比较特殊的用途，如果是`~/test`，这就表示当前用户的`$HOME`目录下的`test`文件或目录。而在`Windows`下，用户没有宿主目录，那么波浪号所指的目录则根据环境变量`HOME`而定。
&emsp;&emsp;通配符代替了一系列的文件，例如`*.c`表示所有后缀为`c`的文件。需要注意，如果我们的文件名中有通配符，那么可以使用转义字符`\`，例如`\*`来表示真实的`*`字符，而不是任意长度的字符串。

``` makefile
# 目标print依赖于当前目录下的所有“.c”文件
print: *.c
    touch print
```

通配符同样可以用在变量中：

``` makefile
objects = *.o
```

#### 文件搜寻

&emsp;&emsp;在一些大的工程中有大量的源文件，通常的做法是把这许多的源文件分类，并存放在不同的目录中。当`make`需要去找寻文件的依赖关系时，可以在文件前加上路径。但最好的方法是把一个路径告诉`make`，让`make`自动去找。
&emsp;&emsp;`Makefile`文件中的特殊变量`VPATH`就是完成这个功能的，如果没有指明这个变量，`make`只会在当前的目录中去找寻依赖文件和目标文件。如果定义了这个变量，那么`make`就会在当前目录找不到的情况下，到所指定的目录中去找寻文件了。

``` makefile
VPATH = src:../headers
```

上面的的定义指定两个目录，即`src`和`../headers`，目录由`:`分隔。`make`会按照这个顺序进行搜索，当然`当前目录`永远是最高优先搜索的地方。
&emsp;&emsp;另一个设置文件搜索路径的方法是使用`make`的关键字`vpath`。它和变量`VPATH`很类似，但是更为灵活，可以指定在不同的搜索目录中搜索不同的文件。使用方法有如下`3`种：

``` makefile
vpath <pattern>; <directories>  # 为符合模式<pattern>的文件指定搜索目录<directories>
vpath <pattern>;  # 清除符合模式<pattern>的文件的搜索目录
vpath  # 清除所有已被设置好了的文件搜索目录
```

- `<pattern>`：指定了要搜索的文件集，需要包含字符`%`，意思是匹配零或若干字符，例如`%.h`表示所有以`.h`结尾的文件。
- `<directories>`：指定了文件集`<pattern>`的搜索目录。

``` makefile
vpath %.h ../headers
```

该语句要求`make`在`../headers`目录下搜索所有以`.h`结尾的文件。
&emsp;&emsp;我们可以连续地使用`vpath`语句，以指定不同搜索策略。如果连续的`vpath`语句中出现了相同的`<pattern>`，那么`make`会按照`vpath`语句的先后顺序来执行搜索：

``` makefile
vpath %.c foo
vpath %.c blish
vpath %.c bar
```

上面的语句表示先在`foo`目录下查找`.c`结尾的文件，然后是`blish`目录，最后是`bar`目录。

``` makefile
vpath %.c foo:bar
vpath %.c blish
```

上面的语句则表示先在`foo`目录下查找`.c`结尾的文件，然后是`bar`目录，最后才是`blish`目录。

#### 伪目标

&emsp;&emsp;我们之前提到过`伪目标`的概念：

``` makefile
clean:  # clean是一个伪目标
    rm *.o temp
```

`伪目标`并不是一个文件，只是一个标签。由于`伪目标`不是文件，所以`make`无法生成它的依赖关系，并且无法决定它是否要执行。只有通过显示地指明这个`目标`，才能让其生效。
&emsp;&emsp;`伪目标`的取名不能和文件名重名，不然其就失去了`伪目标`的意义了。如果当前文件夹中没有名为`clean`的文件，当输入`make clean`时，`rm`命令会被执行；一旦文件夹中出现了名为`clean`的文件，当输入`make clean`时，由于这个规则没有任何依赖文件，所以目标被认为是最新的，而不去执行规则所定义的命令，因此`rm`命令不会被执行。
&emsp;&emsp;为了避免这种情况，可以使用一个特殊的标记`.PHONY`，来显式地指明一个目标是`伪目标`。它向`make`说明，不管是否有这个文件，这个目标就是`伪目标`：

``` makefile
.PHONY: clean
clean:
    rm *.o temp
```

&emsp;&emsp;伪目标一般没有依赖的文件，但是也可以为伪目标指定所依赖的文件。伪目标同样可以作为`默认目标`，只要将其放在第一个`target`位置即可。
&emsp;&emsp;如果你的`Makefile`需要一次性生成若干个可执行文件，那么可以使用`伪目标`的特性：

``` makefile
all : prog1 prog2 prog3
.PHONY : all
prog1 : prog1.o utils.o
    cc -o prog1 prog1.o utils.o
prog2 : prog2.o
    cc -o prog2 prog2.o
prog3 : prog3.o sort.o utils.o
    cc -o prog3 prog3.o sort.o utils.o
```

&emsp;&emsp;从上面的例子可以看出，目标也可以成为依赖，所以伪目标同样也可以：

``` makefile
.PHONY: cleanall cleanobj cleandiff
cleanall : cleanobj cleandiff
    rm program
cleanobj :
    rm *.o
cleandiff :
    rm *.diff
```

`make cleanall`将清除所有要被清除的文件，`cleanobj`和`cleandiff`这两个伪目标有点像`子程序`。我们可以输入`make cleanall`、`make cleanobj`或`make cleandiff`命令来达到清除不同种类文件的目的。

#### 多目标

&emsp;&emsp;`Makefile`规则中的目标可以不止一个，它支持多目标的特性。在实际编程中，多个目标有可能同时依赖于一个文件，并且其生成的命令极为类似，于是就可以把它们合并起来。
&emsp;&emsp;当然，多个目标如果使用同一个执行命令，这可能会带来麻烦。好在可以使用一个自动化变量`$@`，该变量表示当前规则中所有目标的集合：

``` makefile
bigoutput littleoutput : text.g
    generate text.g -$(subst output, , $@) >; $@
```

上述规则等价于：

``` makefile
bigoutput : text.g
    generate text.g -big >; bigoutput
littleoutput : text.g
    generate text.g -little >; littleoutput
```

其中，`-$(subst output, , $@)`中的`$`表示执行一个`Makefile`的函数，函数名为`subst`，后面的为其参数，该函数的作用是截取字符串。`$@`表示目标的集合，类似于一个数组，它会依次取出目标，并放置在命令中。

#### 静态模式

&emsp;&emsp;静态模式可以更加容易地定义多目标的规则：

``` makefile
<targets ...> : <target-pattern> : <prereq-patterns ...>
    <commands>
```

- `targets`：定义了一系列的目标文件，可以有通配符，它是目标的一个集合。
- `target-parrtern`：指明了`targets`的模式，也就是的目标集模式。
- `prereq-parrterns`：目标的依赖模式，它对`target-parrtern`形成的模式再进行一次依赖目标的定义。

&emsp;&emsp;举例来说，如果`<target-parrtern>`定义成`%.o`，意思是`<target>`集合中都是以`.o`结尾的，而如果我们的`<prereq-parrterns>`定义成`%.c`，意思是对`<target-parrtern>`所形成的目标集进行二次定义。
&emsp;&emsp;其计算方法是：取`<target-parrtern>`模式中的`%`(也就是去掉了`.o`这个结尾)，并为其加上`.c`这个结尾形成的新依赖集合。所以`target-parrtern`或`prereq-parrterns`中都应该有`%`这个字符。如果文件名中有`%`，那么可以使用反斜杠`\`进行转义。

``` makefile
objects = foo.o bar.o
all: $(objects)

$(objects): %.o: %.c
    $(CC) -c $(CFLAGS) $< -o $@
```

上面的规则展开后等价于下面的规则：

``` makefile
objects = foo.o bar.o
all: $(objects)

foo.o : foo.c
    $(CC) -c $(CFLAGS) foo.c -o foo.o
bar.o : bar.c
    $(CC) -c $(CFLAGS) bar.c -o bar.o
```

命令中的`$<`也是自动化变量，表示所有的依赖目标集(也就是`foo.c bar.c`)。
&emsp;&emsp;试想一下，如果`%.o`有几百个，那么只要用这种很简单的`静态模式规则`就可以写完一堆规则，实在是太有效率了。
&emsp;&emsp;再看一个例子：

``` makefile
files = foo.elc bar.o lose.o

$(filter %.o, $(files)): %.o: %.c
    $(CC) -c $(CFLAGS) $< -o $@
$(filter %.elc, $(files)): %.elc: %.el
    emacs -f batch-byte-compile $<
```

`$(filter %.o, $(files))`表示调用`Makefile`的`filter`函数，只要其中模式为`%.o`的内容(也就是`bar.o lose.o`)。

---

### 书写命令

#### 显示命令

&emsp;&emsp;通常情况下，`make`会把其要执行的命令行在命令执行前输出到屏幕上。当使用`@`字符在命令行前，那么这个命令将不被`make`显示出来。
&emsp;&emsp;最具代表性的例子是使用这个功能来让屏幕显示一些信息：

``` makefile
@echo 正在编译XXX模块......
```

当`make`执行时，会输出`正在编译XXX模块......`字串，但不会输出命令。如果没有`@`，那么`make`将输出：

``` bash
echo 正在编译XXX模块......
正在编译XXX模块......
```

&emsp;&emsp;如果`make`执行时带入参数`-n`或`--just-print`，那么只是显示命令，但不会执行命令。这个功能很有利于调试`Makefile`，观察命令的执行过程。而`make`参数`-s`或`--slient`则是全面禁止命令的显示。

#### 命令执行

&emsp;&emsp;如果需要让上一条命令的结果应用在下一条命令中，应该使用`;`分隔这两条命令：
&emsp;&emsp;示例一中的`pwd`会打印出当前的`Makefile`目录，`cd`的执行结果对`pwd`没有产生影响：

``` makefile
exec:  # 示例一
    cd /home/hchen
    pwd
```

&emsp;&emsp;示例二中的`pwd`会打印出`/home/hchen`：

``` makefile
exec:  # 示例二
    cd /home/hchen; pwd
```

#### 命令出错

&emsp;&emsp;每当命令运行完后，`make`会检测每个命令的返回码。如果命令返回成功，那么`make`会执行下一条命令。当规则中所有的命令成功返回后，这个规则就算是成功完成了。如果一个规则中的某个命令出错了(命令退出码非零)，那么`make`就会终止执行当前规则，这将有可能终止所有规则的执行。
&emsp;&emsp;有时候命令的出错并不表示就是错误的。例如使用`mkdir`命令建立一个目录，如果目录不存在，那么`mkdir`就成功执行；如果目录存在，那么就出错了。我们之所以使用`mkdir`，其意思就是一定要有这样的一个目录，不希望`mkdir`出错而终止规则的运行。为了做到这一点，需要忽略命令的出错。可以在`Makefile`的命令行前加一个减号`-`，即不管命令出不出错都认为是成功的。

``` makefile
clean:
    -rm -f *.o
```

&emsp;&emsp;还有一个全局的办法，就是给`make`加上`-i`或者`--ignore-errors`参数，那么`Makefile`中的所有命令都会忽略错误。需要提一下`make`的参数`-k`或者`--keep-going`，这个参数的意思是：如果某条规则中的命令出错了，那么就终止该规则的执行，但继续执行其它规则。

#### 嵌套执行make

&emsp;&emsp;在一些大的工程中，我们会把不同模块或是不同功能的源文件放在不同的目录中。可以在每个目录中都写一个该目录的`Makefile`，这可以让`Makefile`变得更加得简洁，而不至于把所有的东西全部写在一个`Makefile`中。
&emsp;&emsp;例如有一个子目录`subdir`，这个目录下有个`Makefile`文件，它用来指明了这个目录下文件的编译规则，那么总控的`Makefile`可以这样书写：

``` makefile
subsystem:
    cd subdir && $(MAKE)
```

其等价于：

``` makefile
subsystem:
    $(MAKE) -C subdir
```

`make`定义了很多默认变量，例如`CC`、`CFLAGS`。`$(MAKE)`就是预设的`make`这个命令的名称或者路径。`make -p`可以查看所有预定义变量的当前值。
&emsp;&emsp;这两个例子的意思都是先进入`subdir`目录，然后执行`make`命令。总控`Makefile`的变量可以传递到下级的`Makefile`中(如果你是显式的声明)，但是不会覆盖下层的`Makefile`中所定义的变量，除非指定了`-e`参数。
&emsp;&emsp;如果你要传递变量到下级`Makefile`中，那么可以使用这样的声明：

``` makefile
export <variable ...>
```

如果你不想让某些变量传递到下级`Makefile`中，那么可以这样声明：

``` makefile
unexport <variable ...>
```

&emsp;&emsp;示例一如下：

``` makefile
export variable = value
# 等价于
variable = value
export variable
# 等价于
export variable := value
# 等价于
variable := value
export variable
```

&emsp;&emsp;示例二如下：

``` makefile
export variable += value
# 等价于
variable += value
export variable
```

&emsp;&emsp;如果你要传递所有的变量，那么只要一个`export`就行了，后面什么也不用跟。
&emsp;&emsp;需要注意两个变量：`SHELL`和`MAKEFLAGS`。这两个变量不管你是否有`export`，总是要传递到下层`Makefile`中。特别是`MAKEFILES`变量，其中包含了`make`的参数信息。如果我们执行总控`Makefile`时，有`make`参数；或是在上层`Makefile`中定义了这个变量，那么`MAKEFILES`变量将会是这些参数，并会传递到下层`Makefile`中。
&emsp;&emsp;但是，`make`命令中的有几个参数并不往下传递，它们是`-C`、`-f`、`-h`、`-o`和`-W`。
&emsp;&emsp;如果你不想往下层传递参数，那么可以这样写：

``` makefile
subsystem:
    cd subdir && $(MAKE) MAKEFLAGS=
```

如果你定义了环境变量`MAKEFLAGS`，那么你得确信其中的选项是大家都会用到的。如果其中有`-t`、`-n`和`-q`参数，那么将会有让你意想不到的结果，或许会让你异常地恐慌。
&emsp;&emsp;还有一个在`嵌套执行`中比较有用的参数：`-w`或`--print-directory`。它们会在make的过程中输出当前工作目录的信息。如果我们的下级`make`目录是`/home/hchen/gnu/make`，使用`make -w`来执行，那么当进入该目录时就会看到：

``` bash
make: Entering directory `/home/hchen/gnu/make'
```

而在完成下层`make`后离开目录时就会看到：

``` bash
make: Leaving directory `/home/hchen/gnu/make'
```

当你使用`-C`参数来指定`make`下层`Makefile`时，`-w`会被自动打开的。如果参数中有`-s`、`--slient`或是`--no-print-directory`，那么`-w`总是失效的。

### 定义命令包

&emsp;&emsp;如果`Makefile`中出现一些相同命令序列，那么我们可以为这些相同的命令序列定义一个变量。定义这种命令序列的语法以`define`开始，以`endef`结束：

``` makefile
define run-yacc
yacc $(firstword $^)
mv y.tab.c $@
endef
```

其中`run-yacc`是这个命令包的名字，不要和`Makefile`中的变量重名。使用示例如下：

``` makefile
foo.c : foo.y
    $(run-yacc)
```

`make`在执行命令包时，命令包中的每个命令会被依次独立执行。

---

### 使用变量

#### 变量的基础

&emsp;&emsp;变量在声明时，需要给予`初值`；而在使用时，需要给在变量名前加上`$`符号，但最好使用`()`或`{}`把变量给包括起来。如果你要使用真实的`$`字符，那么需要用`$$`来表示。
&emsp;&emsp;先看一个例子：

``` makefile
objects = program.o foo.o utils.o
program : $(objects)
    cc -o program $(objects)
$(objects) : defs.h
```

变量会在使用它的地方精确地展开：

``` makefile
objects = program.o foo.o utils.o
program : program.o foo.o utils.o
    cc -o program program.o foo.o utils.o
program.o foo.o utils.o : defs.h
```

#### 变量的值

&emsp;&emsp;在定义变量的值时，可以使用其它变量来构造变量的值，`Makefile`中有`2`种方式来用变量定义变量的值。
&emsp;&emsp;先看第一种方式，也就是简单的使用`=`号，左侧是变量，右侧是变量的值。而右侧变量的值可以定义在文件的任何一处：

``` makefile
foo = $(bar)
bar = $(ugh)
ugh = Huh?
all:
    echo $(foo)
```

执行`make all`将会打印出变量`$(foo)`的值是`Huh?`。
&emsp;&emsp;这个功能有好处还有坏处。好处是可以把变量的真实值推到后面来定义：

``` makefile
CFLAGS = $(include_dirs) -O
include_dirs = -Ifoo -Ibar
```

当`CFLAGS`在命令中被展开时，将会是`-Ifoo -Ibar -O`。但这种形式的坏处就是递归定义：

``` makefile
CFLAGS = $(CFLAGS) -O
# 或者
A = $(B)
B = $(A)
```

这会让`make`陷入无限的变量展开过程中去，当然`make`是有能力检测这样的定义，并会报错。
&emsp;&emsp;为了避免上面的这种情况，我们可以使用`make`中的另一种定义变量的方法。这种方法使用的是`:=`操作符：

``` makefile
x := foo
y := $(x) bar
x := later
```

其等价于：

``` makefile
y := foo bar
x := later
```

值得一提的是，这种方法让前面的变量不能使用后面的变量，只能使用前面已定义好了的变量。如果是下面这样：

``` makefile
y := $(x) bar
x := foo
```

那么`y`的值是`bar`，而不是`foo bar`。
&emsp;&emsp;下面是一个复杂的例子，其中包括了`make`的函数、条件表达式和系统变量`MAKELEVEL`的使用：

``` makefile
ifeq (0, ${MAKELEVEL})
    cur-dir   := $(shell pwd)
    whoami    := $(shell whoami)
    host-type := $(shell arch)
    MAKE := ${MAKE} host-type=${host-type} whoami=${whoami}
endif
```

系统变量`MAKELEVEL`的意思是，如果我们的`make`有一个嵌套执行的动作，那么这个变量会记录了当前`Makefile`的调用层数。
&emsp;&emsp;如果需要定义一个值为空格的变量，那么可以这样写：

``` makefile
nullstring :=
space := $(nullstring) # end of the line
```

`nullstring`是一个`Empty`变量，其中什么也没有，而`space`的值是一个空格。因为在操作符的右边是很难描述一个空格，这里采用的技术很管用，先用一个`Empty`变量来标明变量的值开始了，而后面采用`#`注释符来表示变量定义的终止，这样就可以定义出其值是一个空格的变量。
&emsp;&emsp;请注意这里关于`#`的使用，如果这样定义一个变量：

``` makefile
dir := /foo/bar  # directory to put the frobs in
```

那么`dir`的值将会是`/foo/bar(空格)(空格)`。如果我们这样使用这样变量来指定别的目录，例如`$(dir)/file`，那就完蛋了。
&emsp;&emsp;还有一个比较有用的操作符是`?=`，先看示例：

``` makefile
FOO ?= bar
```

其含义是，如果`FOO`没有被定义过，那么变量`FOO`的值就是`bar`；如果`FOO`先前被定义过，那么这条语将什么也不做，其等价于：

``` makefile
ifeq ($(origin FOO), undefined)
    FOO = bar
endif
```

#### 变量高级用法

&emsp;&emsp;这里介绍两种变量的高级使用方法，第一种是变量值的替换。
&emsp;&emsp;我们可以替换变量中的共有的部分，其格式是`$(var:a=b)`或是`${var:a=b}`：

``` makefile
foo := a.o b.o c.o
bar := $(foo:.o=.c)
```

执行完成后，`$(bar)`的值就是`a.c b.c c.c`。
&emsp;&emsp;另外一种变量替换的技术是以`静态模式`定义的：

``` makefile
foo := a.o b.o c.o
bar := $(foo:%.o=%.c)
```

&emsp;&emsp;第二种高级用法是`把变量的值再当成变量`，先看一个例子：

``` makefile
x = y
y = z
a := $($(x))
```

在这个例子中，`$(x)`的值是`y`，所以`$($(x))`就是`$(y)`，于是`$(a)`的值就是`z`。

#### 追加变量值

&emsp;&emsp;我们可以使用`+=`操作符给变量追加值：

``` makefile
objects = main.o foo.o bar.o utils.o
objects += another.o
```

于是`$(objects)`值变成了`main.o foo.o bar.o utils.o another.o`。
&emsp;&emsp;如果变量之前没有定义过，那么`+=`会自动变成`=`；如果前面有变量定义，那么`+=`会继承前一次操作的赋值符。
&emsp;&emsp;如果前一次操作的赋值符是`:=`，那么`+=`会以`:=`作为其赋值符：

``` makefile
variable := value
variable += more
```

等价于：

``` makefile
variable := value
variable := $(variable) more
```

&emsp;&emsp;但如果是下面这种情况，`make`则不会用`=`来赋值，这样是为了避免发生变量的递归定义，因此我们不需要去担心这个问题。

``` makefile
variable = value
variable += more
```

#### 环境变量

&emsp;&emsp;系统环境变量可以在`make`开始运行时被载入到`Makefile`文件中，但是如果`Makefile`中已定义了这个变量或是这个变量由`make`命令行带入，那么系统的环境变量的值将被覆盖(如果`make`指定了`-e`参数，那么系统环境变量将覆盖`Makefile`中定义的变量)。
&emsp;&emsp;因此，如果在环境变量中设置了`CFLAGS`环境变量，那么就可以在所有的`Makefile`中使用这个变量了，这对于使用统一的编译参数有比较大的好处。如果`Makefile`中定义了`CFLAGS`，那么则会使用`Makefile`中的这个变量；如果没有定义，则使用系统环境变量的值，很像`全局变量`和`局部变量`的特性。

#### 目标变量

&emsp;&emsp;在`Makefile`中定义的变量都是`全局变量`，在整个文件都可以访问这些变量。当然`自动化变量`除外，例如`$<`等这种自动化变量就属于`规则型变量`，这种变量的值依赖于规则的目标和依赖目标的定义。
&emsp;&emsp;当然同样可以为某个目标设置局部变量，这种变量被称为`Target-specific Variable`，它可以和`全局变量`同名。因为它的作用范围只在这条规则以及连带规则中，所以其值也只在作用范围内有效，而不会影响规则以外的全局变量的值。

``` makefile
<target ...> : <variable-assignment>  # 语法1
```

`<variable-assignment>`可以是前面讲过的各种赋值表达式，如`=`、`:=`、`+=`或是`?=`。

``` makefile
prog : CFLAGS = -g
prog : prog.o foo.o bar.o
    $(CC) $(CFLAGS) prog.o foo.o bar.o
prog.o : prog.c
    $(CC) $(CFLAGS) prog.c
foo.o : foo.c
    $(CC) $(CFLAGS) foo.c
bar.o : bar.c
    $(CC) $(CFLAGS) bar.c
```

在这个示例中，不管全局的`$(CFLAGS)`的值是什么，在`prog`目标以及其所引发的所有规则中(`prog.o foo.o bar.o`的规则)，`$(CFLAGS)`的值都是`-g`。

#### 模式变量

&emsp;&emsp;`GNU`的`make`还支持模式变量(`Pattern-specific Variable`)。通过上面的`目标变量`可以知道，变量可以定义在某个目标上。模式变量的好处就是，我们可以给定一种`模式`，可以把变量定义在符合这种模式的所有目标上。
&emsp;&emsp;`make`的`模式`一般至少含有一个`%`，所以可以用如下方式给所有以`.o`结尾的目标定义`目标变量`：

``` makefile
%.o : CFLAGS = -O
```

同样，模式变量的语法和`目标变量`一样：

``` makefile
<pattern ...> : <variable-assignment>
```


---

### 使用条件判断

&emsp;&emsp;使用条件判断，可以让`make`根据运行时的不同情况选择不同的执行分支。条件表达式可以是比较变量的值，或是比较变量和常量的值。

#### 示例

&emsp;&emsp;下面的例子判断`$(CC)`变量是否为`gcc`，如果是的话，则使用`GNU`函数编译目标：

``` makefile
libs_for_gcc = -lgnu
normal_libs =

foo: $(objects)
ifeq ($(CC), gcc)
    $(CC) -o foo $(objects) $(libs_for_gcc)
else
    $(CC) -o foo $(objects) $(normal_libs)
endif
```

可见，在上面示例的这个规则中，目标`foo`可以根据变量`$(CC)`值来选取不同的函数库来编译程序。
&emsp;&emsp;我们可以从上面的示例中看到三个关键字：`ifeq`、`else`和`endif`。

- `ifeq`：表示条件语句的开始，并指定一个条件表达式。表达式包含两个参数，以逗号分隔，以圆括号括起。
- `else`：表示条件表达式为假的情况。
- `endif`表示一个条件语句的结束，任何一个条件表达式都应该以`endif`结束。

&emsp;&emsp;当变量`$(CC)`值是`gcc`时，目标`foo`的规则是：

``` makefile
foo: $(objects)
    $(CC) -o foo $(objects) $(libs_for_gcc)
```

而当变量`$(CC)`值不是`gcc`时(例如`cc`)，目标`foo`的规则是：

``` makefile
foo: $(objects)
    $(CC) -o foo $(objects) $(normal_libs)
```

当然还可以把上面的例子写得更简洁一些：

``` makefile
libs_for_gcc = -lgnu
normal_libs =

ifeq ($(CC), gcc)
    libs=$(libs_for_gcc)
else
    libs=$(normal_libs)
endif

foo: $(objects)
    $(CC) -o foo $(objects) $(libs)
```

#### 语法

&emsp;&emsp;条件表达式的语法为：

``` makefile
<conditional-directive>
    <text-if-true>
endif
# 或者为
<conditional-directive>
    <text-if-true>
else
    <text-if-false>
endif
```

其中`<conditional-directive>`表示条件关键字，例如`ifeq`。这个关键字有`4`种变型。
&emsp;&emsp;第一种是`ifeq`，其作用是比较参数`arg1`和`arg2`的值是否相同：

``` makefile
ifeq (<arg1>, <arg2>)
ifeq '<arg1>' '<arg2>'
ifeq "<arg1>" "<arg2>"
ifeq "<arg1>" '<arg2>'
ifeq '<arg1>' "<arg2>"
```

当然，参数中还可以使用`make`的函数：

``` makefile
ifeq ($(strip $(foo)),)
    <text-if-empty>
endif
```

这个示例中使用了`strip`函数，如果这个函数的返回值是空(`Empty`)，那么`<text-if-empty>`就生效。
&emsp;&emsp;第二种是`ifneq`，其作用是比较参数`arg1`和`arg2`的值是否不相同：

``` makefile
ifneq (<arg1>, <arg2>)
ifneq '<arg1>' '<arg2>'
ifneq "<arg1>" "<arg2>"
ifneq "<arg1>" '<arg2>'
ifneq '<arg1>' "<arg2>"
```

&emsp;&emsp;第三种是`ifdef`：

``` makefile
ifdef <variable-name>;
```

如果变量`<variable-name>`的值非空，那么表达式为真，否则为假。当然`<variable-name>`同样可以是一个函数的返回值。

``` makefile
# 示例一
bar =
foo = $(bar)
ifdef foo
    # 进入这个分支
    frobozz = yes
else
    frobozz = no
endif

# 示例二
foo =
ifdef foo
    frobozz = yes
else
    # 进入这个分支
    frobozz = no
endif
```

&emsp;&emsp;第四种是`ifndef`，其作用和`ifdef`是相反的：

``` makefile
ifndef <variable-name>
```

#### 特别注意

&emsp;&emsp;`make`是在读取`Makefile`时就计算条件表达式的值，并根据条件表达式的值来选择语句，所以最好不要把自动化变量(例如`$@`)放入条件表达式中，因为自动化变量是在运行时才有的。

---

### 使用函数

&emsp;&emsp;在`Makefile`中可以使用函数来处理变量，从而让命令或是规则更为灵活。`make`所支持的函数也不算很多，不过已经足够我们的操作了。函数调用后，其返回值可以当做变量来使用。

#### 函数的调用语法

&emsp;&emsp;函数调用很像变量的使用，也是以`$`来标识的：

``` makefile
$(<function> <arguments>)
# 或者
${<function> <arguments>}
```

- `<function>`就是函数名，`<arguments>`是函数的参数。
- 参数间以逗号`,`分隔，而函数名和参数之间以`空格`分隔。
- 函数调用以`$`开头，以圆括号或花括号把函数名和参数括起。
- 函数中的参数可以使用变量，为了风格的统一，函数和变量的括号最好一样。

``` makefile
comma:= ,
empty:=
space:= $(empty) $(empty)
foo:= a b c
bar:= $(subst $(space), $(comma), $(foo))
```

在这个示例中，`$(comma)`的值是一个逗号，`$(space)`使用了`$(empty)`定义了一个空格，`$(foo)`的值是`a b c`，`$(bar)`的定义调用了函数`subst`。这个函数就是把`$(foo)`中的空格替换成逗号，所以`$(bar)`的值是`a,b,c`。

#### 字符串处理函数

##### subst

&emsp;&emsp;`$(subst <from>, <to>, <text>)`功能是把字串`<text>`中的`<from>`字符串替换成`<to>`。函数返回被替换过后的字符串。

``` makefile
# 把“feet on the street”中的“ee”替换成“EE”，返回结果是“fEEt on the strEEt”
$(subst ee, EE, feet on the street)
```

##### patsubst

&emsp;&emsp;`$(patsubst <pattern>, <replacement>, <text>)`功能是查找`<text>`中符合模式`<pattern>`的单词，然后以`<replacement>`替换。函数返回被替换过后的字符串。

``` makefile
# 把字串“x.c.c bar.c”符合模式“%.c”的单词替换成“%.o”，返回结果是“x.c.o bar.o”
$(patsubst %.c, %.o, x.c.c bar.c)
```

##### strip

&emsp;&emsp;`$(strip <string>)`功能是去掉`<string>`字串中开头和结尾的空字符，返回被去掉空格的字符串值。

``` makefile
# 把字串“a b c ”去掉开头和结尾的空格，结果是“a b c”
$(strip a b c )
```

##### findstring

&emsp;&emsp;`$(findstring <find>, <in>)`功能是在字串`<in>`中查找`<find>`字串。如果找到，则返回`<find>`，否则返回空字符串。

``` makefile
$(findstring a, a b c)  # 返回字符串a
$(findstring a, b c)  # 返回空字符串
```

##### filter

&emsp;&emsp;`$(filter <pattern...>, <text>)`功能是以`<pattern>`模式过滤`<text>`字符串中的单词，保留符合模式`<pattern>`的单词，注意可以有多个模式。函数返回符合模式`<pattern>`的字串。

``` makefile
sources := foo.c bar.c baz.s ugh.h
foo: $(sources)
    # “$(filter %.c %.s, $(sources))”返回的值是“foo.c bar.c baz.s”
    cc $(filter %.c %.s, $(sources)) -o foo
```

##### filter-out

&emsp;&emsp;`$(filter-out <pattern...>, <text>)`功能是以`<pattern>`模式过滤`<text>`字符串中的单词，去除符合模式`<pattern>`的单词，注意可以有多个模式。函数返回不符合模式`<pattern>`的字串。

``` makefile
objects=main1.o foo.o main2.o bar.o
mains=main1.o main2.o
# 返回值是“foo.o bar.o”
$(filter-out $(mains), $(objects))
```

##### sort

&emsp;&emsp;`$(sort <list>)`功能是给字符串`<list>`中的单词排序(默认是升序)，注意`sort`函数会去掉`<list>`中相同的单词。函数返回排序后的字符串。

``` makefile
$(sort foo bar lose)  # 返回“bar foo lose”
```

##### word

&emsp;&emsp;`$(word <n>, <text>)`功能是取字符串`<text>`中第`<n>`个单词(从`1`开始计数)。函数返回字符串`<text>`中第`<n>`个单词，如果`<n>`比`<text>`中的单词数要大，那么返回空字符串。

``` makefile
$(word 2, foo bar baz)  # 返回值是“bar”
```

##### wordlist

&emsp;&emsp;`$(wordlist <s>, <e>, <text>)`功能是从字符串`<text>`中取从`<s>`开始到`<e>`的单词串，`<s>`和`<e>`是一个数字。函数返回字符串`<text>`中从`<s>`到`<e>`的单词字串。如果`<s>`比`<text>`中的单词数要大，那么返回空字符串；如果`<e>`大于`<text>`的单词数，那么返回从`<s>`开始到`<text>`结束的单词串。

``` makefile
$(wordlist 2, 3, foo bar baz)  # 返回值是“bar baz”
```

##### words

&emsp;&emsp;`$(words <text>)`功能是统计`<text>`中字符串中的单词个数，函数返回`<text>`中的单词数。

``` makefile
$(words, foo bar baz)  # 返回值是3
```

如果要取`<text>`中最后的一个单词，可以这样写：

``` makefile
$(word $(words <text>), <text>)
```

##### firstword

&emsp;&emsp;`$(firstword <text>)`功能是取字符串`<text>`中的第一个单词。

``` makefile
$(firstword foo bar)  # 返回值是foo
```

这个函数可以用`word`函数来实现：

``` makefile
$(word 1, <text>)
```

#### 文件名操作函数

##### dir

&emsp;&emsp;`$(dir <names...>)`功能是从文件名序列`<names>`中取出目录部分，目录部分是指最后一个反斜杠(`/`)之前的部分。如果没有反斜杠，那么返回`./`。

``` makefile
$(dir src/foo.c hacks)  # 返回值是“src/ ./”
```

##### notdir

&emsp;&emsp;`$(notdir <names...>)`功能是从文件名序列`<names>`中取出非目录部分，非目录部分是指最后一个反斜杠(`/`)之后的部分。

``` makefile
$(notdir src/foo.c hacks)  # 返回值是“foo.c hacks”
```

##### suffix

&emsp;&emsp;`$(suffix <names...>)`功能是从文件名序列`<names>`中取出各个文件名的后缀。如果文件没有后缀，则返回空字串。

``` makefile
$(suffix src/foo.c src-1.0/bar.c hacks)  # 返回值是“.c .c”
```

##### basename

&emsp;&emsp;`$(basename <names...>)`功能是从文件名序列`<names>`中取出各个文件名的前缀部分。函数返回文件名序列`<names>`的前缀序列，如果文件没有前缀，则返回空字串。

``` makefile
# 返回值是“src/foo src-1.0/bar hacks”
$(basename src/foo.c src-1.0/bar.c hacks)
```

##### addsuffix

&emsp;&emsp;`$(addsuffix <suffix>, <names...>)`功能是把后缀`<suffix>`加到`<names>`中的每个单词后面。

``` makefile
$(addsuffix .c, foo bar)  # 返回值是“foo.c bar.c”
```

##### addprefix

&emsp;&emsp;`$(addprefix <prefix>, <names...>)`功能是把前缀`<prefix>`加到`<names>`中的每个单词后面。

``` makefile
$(addprefix src/, foo bar)  # 返回值是“src/foo src/bar”
```

##### join

&emsp;&emsp;`$(join <list1>, <list2>)`功能是把`<list2>`中的单词对应地加到`<list1>`的单词后面。如果`<list1>`的单词个数要比`<list2>`的多，那么`<list1>`中的多出来的单词将保持原样；如果`<list2>`的单词个数要比`<list1>`多，那么`<list2>`多出来的单词将被复制到`<list2>`中。

``` makefile
$(join aaa bbb, 111 222 333)  # 返回值是“aaa111 bbb222 333”
```

#### foreach函数

&emsp;&emsp;`foreach`函数的作用是循环：

``` makefile
$(foreach <var>, <list>, <text>)
```

这个函数是把参数`<list>`中的单词逐一取出放到参数`<var>`所指定的变量中，然后再执行`<text>`所包含的表达式。每一次循环，`<text>`都会返回一个字符串，其返回的每个字符串会以空格分隔。最后当整个循环结束时，`<text>`所返回的每个字符串所组成的整个字符串(以空格分隔)将会是`foreach`函数的返回值。

``` makefile
names := a b c d
# “$(files)”的值是“a.o b.o c.o d.o”
files := $(foreach n, $(names), $(n).o)
```

#### if函数

&emsp;&emsp;`if`函数很像条件语句`ifeq`，其语法如下：

``` makefile
$(if <condition>, <then-part>)
# 或是
$(if <condition>, <then-part>, <else-part>)
```

`<condition>`是`if`的表达式，如果其返回值为`非空字符串`，那么这个表达式就相当于返回真，于是`<then-part>`会被计算，否则`<else-part>`会被计算。
&emsp;&emsp;`if`函数的返回值：如果`<condition>`为真，那么`<then-part>`会是整个函数的返回值；如果`<condition>`为假，那么`<else-part>`会是整个函数的返回值。如果此时`<else-part>`没有被定义，那么整个函数返回空字串。

#### call函数

&emsp;&emsp;`call`函数是唯一一个可以用来创建新的参数化的函数。你可以写一个非常复杂的表达式，在这个表达式中可以定义许多参数，然后用`call`函数来向这个表达式传递参数。

``` makefile
$(call <expression>, <parm1>, <parm2>, <parm3>...)
```

当`make`执行这个函数时，`<expression>`中的变量如`$(1)`、`$(2)`、`$(3)`等，会被参数`<parm1>`、`<parm2>`、`<parm3>`依次取代。而`<expression>`的返回值就是`call`函数的返回值。

``` makefile
reverse = $(1) $(2)
# foo的值就是“a b”
foo = $(call reverse, a, b)
```

当然，参数的次序是可以自定义的，不一定是按照顺序的：

``` makefile
reverse = $(2) $(1)
# foo的值就是“b a”
foo = $(call reverse, a, b)
```

#### shell函数

&emsp;&emsp;`shell`函数可以操作`Shell`命令让，然后把执行操作命令后的输出返回。

``` makefile
contents := $(shell cat foo)
files := $(shell echo *.c)
```

注意，这个函数会新生成一个`Shell`程序来执行命令，所以要注意其运行性能。

#### 控制make的函数

&emsp;&emsp;`make`提供了一些函数来控制`make`的运行。

##### error

&emsp;&emsp;`$(error <text ...>)`：产生一个致命的错误，`<text ...>`是错误信息。

``` makefile
ifdef ERROR_001
    $(error error is $(ERROR_001))
endif
```

##### warning

&emsp;&emsp;`$(warning <text ...>)`：这个函数类似于`error`函数，但它并不会让`make`退出，而只是输出一段警告信息。