---
title: C语言协程实现
categories: C语言语法详解
date: 2018-12-08 14:34:40
---
&emsp;&emsp;先看这段代码：<!--more-->

``` cpp
#include <stdio.h>

typedef struct {
    int i;
    int num;
    int state;
} task;

#define crBegin(state) \
    switch (state) { case 0:

#define crReturn(state, ret) \
    (state) = __LINE__; return (ret); case __LINE__:

#define crEnd() \
    }

int cb ( task* t ) {
    crBegin ( t->state );

    for ( ;; ) {
        t->num = 1;

        for ( t->i = 0; t->i < 20; t->i++ ) {
            crReturn ( t->state, t->num );
            t->num += 1;
        }
    }

    crEnd();
}

int main() {
    task t;
    int i;
    t.state = 0;

    for ( i = 0; i < 100; i++ ) {
        printf ( "%d ", cb ( &t ) );
    }

    return 0;
}
```

它会输出`5`组`1`至`20`的数字。把宏展开再看一下`cb`函数(代码做了一些调整)：

``` cpp
int cb ( task* t ) {
    switch ( t->state ) {
        case 0:
            for ( ;; ) {
                t->num = 1;

                for ( t->i = 0; t->i < 20; t->i++ ) {
                    t->state = __LINE__ + 3;
                    return t->num;

                case __LINE__:
                    t->num += 1;
                }
            }
    }
}
```

这其实是`switch`的一个技巧，通过`switch`实现了一种断点的效果，类似于`Python`的`yield`。它是一个非常简单的`C`语言协程实现。

### 协程的概念

&emsp;&emsp;我们再看看`Wiki`上对协程的解释：与子例程一样，协程也是一种程序组件。相对子例程而言，协程更为一般和灵活，但在实践中使用没有子例程那样广泛。协程源自`Simula`和`Modula-2`语言，但也有其他语言支持。协程更适合于用来实现彼此熟悉的程序组件，如合作式多任务、迭代器、无限列表和管道。因为相对于子例程协程可以有多个入口和出口点，可以用协程来实现任何的子例程。事实上，正如Knuth所说：`子例程是协程的特例`。
&emsp;&emsp;每当子例程被调用时，执行从被调用子例程的起始处开始；然而，接下来的每次协程被调用时，从协程返回的位置接着执行。
&emsp;&emsp;因为子例程只返回一次，要返回多个值就要通过集合的形式。这在有些语言(例如`Forth`)中很方便，而其他语言(例如`C`语言)只允许单一的返回值，所以就需要引用一个集合。相反地，因为协程可以返回多次，返回多个值只需要在后继的协程调用中返回附加的值即可。在后继调用中返回附加值的协程常被称为`产生器`。
&emsp;&emsp;子例程容易实现于堆栈之上，因为子例程将调用的其他子例程作为下级。相反地，协程对等地调用其他协程，最好的实现是用`continuations`(由有垃圾回收的堆实现)以跟踪控制流程。
&emsp;&emsp;在当今的主流编程环境里，线程是协程的合适的替代者，线程提供了用来管理同时执行(实际上是线程的不断切换)的代码段实时交互的功能。因为要解决大量困难的问题，线程包括了许多强大和复杂的功能并导致了困难的学习曲线。当需要的只是一个协程时，使用线程就过于技巧了。然而不像其他的替代者，在支持`C`语言的环境中，线程也是广泛有效的，对很多程序员也比较熟悉，并被很好地实现、文档化和支持。

### C语言的协程

&emsp;&emsp;关于`C`语言的协程编程，`PuTTY`的作者`Simon Tatham`写有一篇很棒的文章，其译文如下。
&emsp;&emsp;设计大型程序一直都是件困难的事情，其中常常会遇到的一个难题是：一段生成数据的代码和一段处理这些数据的代码，让哪一个做调用函数，哪一个做被调函数。下面是用于游程编码的解码器代码和解析器代码，都很简单：

``` cpp
while ( 1 ) { /* Decompression code */
    c = getchar();

    if ( c == EOF ) {
        break;
    }

    if ( c == 0xFF ) {
        len = getchar();
        c = getchar();

        while ( len-- ) {
            emit ( c );
        }
    } else {
        emit ( c );
    }
}

emit ( EOF );

while ( 1 ) { /* Parser code */
    c = getchar();

    if ( c == EOF ) {
        break;
    }

    if ( isalpha ( c ) ) {
        do {
            add_to_token ( c );
            c = getchar();
        } while ( isalpha ( c ) );

        got_token ( WORD );
    }

    add_to_token ( c );
    got_token ( PUNCT );
}
```

第一段每次调用`emit`生成一个字符；第二段每次调用`getchar`消费一个字符。只要能想办法在调用`emit`和`getchar`时能让二者互相传数据，那么就能很容易地将这两段代码连到一起，从而将解码器的输出直接送进解析器。
&emsp;&emsp;很多现代操作系统中，在两个进程或线程之间使用管道就可以实现。解码器中的`emit`向管道中写入数据，解析器的`getchar`在管道的另一端读出数据，简单可靠，但是却不轻量也不易移植，而且也不值得为这么简单的任务将程序拆分成几个线程。本文将给出一个极具创造性的方法来解决这种结构问题。
&emsp;&emsp;传统的方法是改写管道任意一端的代码，使其能够被调用。下面给出了如何对上面两段代码进行改写的例子：

``` cpp
int decompressor ( void ) {
    static int repchar;
    static int replen;

    if ( replen > 0 ) {
        replen--;
        return repchar;
    }

    c = getchar();

    if ( c == EOF ) {
        return EOF;
    }

    if ( c == 0xFF ) {
        replen = getchar();
        repchar = getchar();
        replen--;
        return repchar;
    } else {
        return c;
    }
}

void parser ( int c ) {
    static enum {
        START, IN_WORD
    } state;

    switch ( state ) {
        case IN_WORD:
            if ( isalpha ( c ) ) {
                add_to_token ( c );
                return;
            }

            got_token ( WORD );
            state = START;
        case START: /* fall through */
            add_to_token ( c );

            if ( isalpha ( c ) ) {
                state = IN_WORD;
            } else {
                got_token ( PUNCT );
            }

            break;
    }
}
```

&emsp;&emsp;当然了，你不必将两段程序都进行改写，只要改一段就行了。如果你将解码器改成上面那样，它每次调用会返回一个字符，那么只要将解析器中对`getchar`的调用替换成对`decompressor`的调用就好了。相反地，如果你像上面那样改写了解析器，对每个输入字符调用一次，那么就可以将解码器中调用的`emit`换成`parser`。别把两个函数都改写成被调函数，除非你是想受罪。
&emsp;&emsp;其实这里已经暴露出一个问题：这两个改写过的函数都比原来的要难看。把这两个过程写成调用函数要比被调函数更易读。如果单单通过看代码来推断解析器所要解析的语法或者解码器所要处理的压缩格式，那么原来的两段代码都比改写后的代码要清晰些，所以我们最好是能不改写任何一段代码。

### Knuth的协程

&emsp;&emsp;在`计算机程序设计艺术`一书中，`Donald Knuth`给出了一个解决这类问题的方法。他的方法是彻底抛弃栈的概念，别再考虑要有一个调用函数和一个被调函数，试着将它们想象成平等的，相互合作的。
&emsp;&emsp;用专业术语来说就是：将传统的`调用`原语用个稍微不同的`调用`来代替。这个新的`调用`能够将返回值保存在其它地方，而不是栈上，并且还能够跳转到由另一个已保存的返回值所指定的地址上。这样，解码器每次生成一个新的字符，就保存自己的程序计数器并跳转到上次离开解析器时的地址上；而对解析器来说，它每次需要一个新字符时，它保存自己的程序计数器并跳转到上一次离开解码器的地址上。程序可以这样在两个函数之间切换所需要的次数。
&emsp;&emsp;理论上是不错，但在实际中，我们却只能用汇编语言这么做，因为通用的高级语言没有一个支持协程调用原语。类似于`C`这样的语言全都要依赖于基于栈的结构，因此在函数间传递控制时，必须要有一个调用函数和一个被调函数。所以如果你想写可移植的代码，这个技术和使用`Unix`管道的方法一样不可行。

### 基于栈的协程

&emsp;&emsp;我们真正要的是找到`C`语言中能摹仿`Knuth`的协程调用原语的能力。我们必须接受这个现实：在`C`语言中，必须要有调用函数和被调函数。调用函数对我们来说没有任何问题，我们完全按照原算法写代码就行，无论什么时候，如果它生成(或者需要)一个字符，那就调用另外一个函数。
&emsp;&emsp;问题是出在被调函数上。对于被调函数，我们希望有一个`能从返回处再继续`的函数，也就是说从这个函数返回后，当再次调用它时，能从上次返回语句之后的位置继续运行。例如，我们希望能写出这样一个函数：

``` cpp
int function ( void ) {
    int i;

    for ( i = 0; i < 10; i++ ) {
        return i; /* won't work, but wouldn't it be nice */
    }
}
```

连续对它调用`10`次，它能分别返回`0`到`9`。
&emsp;&emsp;我们该怎样实现呢？其实我们可以利用`goto`语句跳转到函数中的任意一点。如果我们在函数中加入一个状态变量，就可以这样实现：

``` cpp
int function ( void ) {
    static int i, state = 0;

    switch ( state ) {
        case 0:
            goto LABEL0;
        case 1:
            goto LABEL1;
    }

LABEL0: /* start of function */

    for ( i = 0; i < 10; i++ ) {
        state = 1; /* so we will come back to LABEL1 */
        return i;
LABEL1:; /* resume control straight after the return */
    }
}
```

&emsp;&emsp;这个方法是可行的。我们在所有可能需要恢复执行的位置都加上标签：起始位置加一个，还有所有`return`语句之后都加一个。我们在状态变量中保存每次调用这个函数时的状态，这样它就能在下次调用时告诉我们应该跳到哪个标签上。每次返回前，更新状态变量，指向到正确的标签；不论调用多少次，针对状态变量的`switch`语句都能找到我们要跳转到的位置。
&emsp;&emsp;但这还是难看得很。最糟糕的部分是所有的标签都需要手工维护，还必须保证函数中的标签和开头`switch`语句中的一致。每次新增一个`return`语句，就必须想一个新的标签名并将其加到`switch`语句中；每次删除`return`语句时，同样也必须删除对应的标签。这使得维护代码的工作量增加了一倍。

### Duff的装置

&emsp;&emsp;`C`语言中著名的`Duff`装置利用了一个事实：`switch`的`case`语句即使放在`switch`的一个子块中，仍然是合法的。`Tom Duff`利用这一点给出了一个优化的输出循环：

``` cpp
switch ( count % 8 ) {
    case 0:
        do {
            *to = *from++;
    case 7:
        *to = *from++;
    case 6:
        *to = *from++;
    case 5:
        *to = *from++;
    case 4:
        *to = *from++;
    case 3:
        *to = *from++;
    case 2:
        *to = *from++;
    case 1:
        *to = *from++;
    } while ( ( count -= 8 ) > 0 );
}
```

我们可以把这个技巧用在协程上。不用`switch`语句来决定要跳转到哪里去执行，而是直接利用`switch`语句本身来实现跳转：

``` cpp
int function ( void ) {
    static int i, state = 0;

    switch ( state ) {
        case 0: /* start of function */
            for ( i = 0; i < 10; i++ ) {
                state = 1; /* so we will come back to "case 1" */
                return i;

                case 1:; /* resume control straight after the return */
            }
    }
}
```

现在看到希望了，我们要做的事情就是精心构造抽取出几个宏来，将这种看起来不伦不类的实现用一些看起来更结构化的语句隐藏起来：

``` cpp
#define crBegin        static int state = 0; switch(state) { case 0:
#define crReturn(i, x) do { state = i; return x; case i:; } while (0)
#define crFinish       }

int function ( void ) {
    static int i;
    crBegin;

    for ( i = 0; i < 10; i++ ) {
        crReturn ( 1, i );
    }

    crFinish;
}
```

注意，使用`do ... while(0)`是为了确保`crReturn`出现在`if-else`之间时不需要使用大括号将它包裹起来。
&emsp;&emsp;好了，这基本上就是我们想要的了。我们可以使用`crReturn`将函数返回，并能够在下次调用时从返回的位置之后继续执行。当然必须要遵守几条规则：

- 函数体要嵌在`crBegin`和`crFinish`之间。
- 所有需要跨`crReturn`使用的局部变量都要定义成`static`变量。
- 不能把`crReturn`放在其他`switch`语句中。

不过这几条规则对我们并没有太大限制。
&emsp;&emsp;现在就剩下一个问题了，就是`crReturn`的第一个参数。就像在上一节中我们每增加一个新的标签都要考虑防止标签名重复一样，现在必须保证`crReturn`的第一个参数不能相同。其实就算重名的话，后果也不严重。编译器能够发现这个错误，因此不会在运行时导致严重后果，但我们还是要避免这个问题。这个问题是能够解决的，`ANSI C`提供了`__LINE__`宏，这个宏展开后是当前代码行的行号，接下来我们就修改一下`crReturn`：

``` cpp
#define crReturn(x) do { state = __LINE__; return x; \
    case __LINE__:; } while (0)
```

### 评估

&emsp;&emsp;现在我们有了这个东西，就可以用它再来改写一下原来那两段代码了：

``` cpp
int decompressor ( void ) {
    static int c, len;
    crBegin;

    while ( 1 ) {
        c = getchar();

        if ( c == EOF ) {
            break;
        }

        if ( c == 0xFF ) {
            len = getchar();
            c = getchar();

            while ( len-- ) {
                crReturn ( c );
            }
        } else {
            crReturn ( c );
        }
    }

    crReturn ( EOF );
    crFinish;
}

void parser ( int c ) {
    crBegin;

    while ( 1 ) {
        /* first char already in c */
        if ( c == EOF ) {
            break;
        }

        if ( isalpha ( c ) ) {
            do {
                add_to_token ( c );
                crReturn();
            } while ( isalpha ( c ) );

            got_token ( WORD );
        }

        add_to_token ( c );
        got_token ( PUNCT );
        crReturn();
    }

    crFinish;
}
```

&emsp;&emsp;我们已经把解码器和解析器都改写成了被调函数，它们不需要像上次那样的大规模的重构工作了，每个函数的结构完全是原来算法的翻版。读代码的人能够推出解析器所能识别的语法，也能推出解码器所使用的压缩格式，与读那两段使用状态机来实现的代码比起来，这要容易多了。一旦你适应了这种新的代码形式，就会发现这两段代码的控制流程相当直观：解码器产生出一个字符时，它就调用`crReturn`将这个字符传给调用函数，并等待当需要下一个字符时再次被调用；当解析器需要一个字符时，它就通过`crReturn`返回，并等待下一次被调用时通过参数`c`传入新的字符。
&emsp;&emsp;不过，代码中还是有一处小小的结构调整：`getchar`(其实，在改写的代码中是`crReturn`)放在了`parser`的循环的结尾，而不是开头，这是因为在进入函数时，第一个字符已经通过`c`传进来了。我们应该能接受这个结构上的小改动，要是你真的对此感觉强烈，可以这样认为：在开始向`parser`送入数据之前，它需要一次初始化调用。当然和前面一样，我们不必把两个函数都用协程的宏改写。改一个就足够了，另一个可以作为它的调用函数。
&emsp;&emsp;我们已经实现了文章开始时的目标：一个可移植的`ANSI C`方法，解决数据在生产者和消费者之间的传递问题，而不必把代码用状态机重写。我们用`switch`语句的一个很少用到的特性，结合`C`语言的预处理器，构造出一个隐式的状态机，进而实现了我们的目标。

### 编程规范

&emsp;&emsp;显然，这个技巧跟每本编程规范书中的内容都相悖。如果你在公司的代码中这样用，你很有可能会受到指责，并因为这种不遵守纪律的行为而被警告：你的宏定义中大括号没有匹配完整，在子代码块中包含了未用到的`case`，还有`crReturn`中乱七八糟的不完整的内容…写出这样不负责任的代码，不炒了你才怪。你应该为此感到羞愧。
&emsp;&emsp;我要声明将编程规范用在这里是不对的。文章里给出的示例代码不是很长，也不很复杂，即便以状态机的方式改写还是能够看懂的。但是随着代码越来越长，改写的难度将越来越大，改写对直观性造成的损失也变得相当相当大。
&emsp;&emsp;想一想，一个函数如果包含这样的小代码块：

``` cpp
case STATE1:
/* perform some activity */
if ( condition ) {
    state = STATE2;
} else {
    state = STATE3;
}
```

对于看代码的人说，这和包含下面小代码块的函数没有多大区别：

``` cpp
LABEL1:
/* perform some activity */
if ( condition ) {
    goto LABEL2;
} else {
    goto LABEL3;
}
```

一个是调用函数，另一个是被调函数。是的，这两个函数的结构在视觉上是一样的，而对于函数中实现的算法，两个函数都一样不利于查看。因为你使用协程的宏而炒你鱿鱼的人，一样会因为你写的函数是由小块的代码和`goto`语句组成而吼着炒了你。只是这次他们没有冤枉你，因为像那样设计的函数会严重扰乱算法的结构。
&emsp;&emsp;编程规范的目标就是为了代码清晰。如果将一些重要的东西，像`switch`、`return`以及`case`语句，隐藏到起`障眼`作用的宏中，从编程规范的角度讲，可以说你扰乱了程序的语法结构，并且违背了代码清晰这一要求。但是我们这样做是为了突出程序的算法结构，而算法结构恰恰是看代码的人更想了解的。任何编程规范，如果非要牺牲算法清晰度来换取语法的清晰度，都应该进行修改。如果你的上司因为使用了这一技巧而解雇你，那么在保安把你往外拖的时候要不断告诉他这一点。

### 提炼及编码

&emsp;&emsp;在正规的应用程序中，这个协程的实现很少能用得上，因为它用到了静态变量，因此是不可重入的，也不能支持多线程。理想情况下，在一个实际的应用程序中，你会希望能在几个不同的上下文中调用同一个函数，并且每次在某个上下文中调用这个函数时，都能在这个上下文中上一次返回的位置之后恢复执行。
&emsp;&emsp;这非常容易实现。我们给函数增加一个新的参数，一个指向上下文结构体的指针；我们将所有的局部变量还有协程用到的状态变量都声明成结构体中的元素。
&emsp;&emsp;这确实有点难看，因为你突然不能用i作为循环计数，而要用`ctx->i`；事实上，所有重要的变量都成了协程上下文结构体中的成员。但是这解决了重入的问题，并且没影响到函数的结构。
&emsp;&emsp;当然，要是`C`语言支持`Pascal`语言的`with`语句，我们就可以将这个间接的引用隐藏掉，但是很遗憾。而对于`C++`语言，我们可以把协程的两个函数设计成类的成员函数，所有的局部变量设计成类的成员变量，从而将作用域的问题隐藏掉。
&emsp;&emsp;这里引用的`C`语言头文件实现了一套预定义的协程使用的宏。文件中定义了两套宏函数，前缀分别是`scr`和`ccr`。`scr`宏是一套简单的实现，用于可以使用静态变量的情况；`ccr`宏更高级一些，能支持重入。在头文件的注释中有完整的说明。以下是`coroutine.h`头文件：

``` cpp
/* coroutine.h
*
* Coroutine mechanics, implemented on top of standard ANSI C. See
* http://www.chiark.greenend.org.uk/~sgtatham/coroutines.html for
* a full discussion of the theory behind this.
*
* To use these macros to define a coroutine, you need to write a
* function that looks something like this.
*
* [Simple version using static variables (scr macros)]
* int ascending (void) {
*     static int i;
*
*     scrBegin;
*     for (i = 0; i < 10; i++) {
*         scrReturn(i);
*     }
*     scrFinish(-1);
* }
*
* [Re-entrant version using an explicit context structure (ccr macros)]
* int ascending (ccrContParam) {
*     ccrBeginContext;
*     int i;
*     ccrEndContext(foo);
*
*     ccrBegin(foo);
*     for (foo->i = 0; foo->i < 10; foo->i++) {
*         ccrReturn(foo->i);
*     }
*     ccrFinish(-1);
* }
*
* In the static version, you need only surround the function body
* with `scrBegin' and `scrFinish', and then you can do `scrReturn'
* within the function and on the next call control will resume
* just after the scrReturn statement. Any local variables you need
* to be persistent across an `scrReturn' must be declared static.
*
* In the re-entrant version, you need to declare your persistent
* variables between `ccrBeginContext' and `ccrEndContext'. These
* will be members of a structure whose name you specify in the
* parameter to `ccrEndContext'.
*
* The re-entrant macros will malloc() the state structure on first
* call, and free() it when `ccrFinish' is reached. If you want to
* abort in the middle, you can use `ccrStop' to free the state
* structure immediately (equivalent to an explicit return() in a
* caller-type routine).
*
* A coroutine returning void type may call `ccrReturnV',
* `ccrFinishV' and `ccrStopV', or `scrReturnV', to avoid having to
* specify an empty parameter to the ordinary return macros.
*
* Ground rules:
*  - never put `ccrReturn' or `scrReturn' within an explicit `switch'.
*  - never put two `ccrReturn' or `scrReturn' statements on the same source line.
*
* The caller of a static coroutine calls it just as if it were an ordinary function:
*
* void main(void) {
*     int i;
*     do {
*         i = ascending();
*         printf("got number %d\n", i);
*     } while (i != -1);
* }
*
* The caller of a re-entrant coroutine must provide a context variable:
*
* void main(void) {
*     ccrContext z = 0;
*     do {
*         printf("got number %d\n", ascending (&z));
*     } while (z);
* }
*
* Note that the context variable is set back to zero when the
* coroutine terminates (by crStop, or by control reaching
* crFinish). This can make the re-entrant coroutines more useful
* than the static ones, because you can tell when they have finished.
*
* If you need to dispose of a crContext when it is non-zero (that
* is, if you want to stop calling a coroutine without suffering a
* memory leak), the caller should call `ccrAbort(ctx)' where `ctx'
* is the context variable.
*
* This mechanism could have been better implemented using GNU C
* and its ability to store pointers to labels, but sadly this is
* not part of the ANSI C standard and so the mechanism is done by
* case statements instead. That's why you can't put a crReturn()
* inside a switch() statement.
*/

/*
* coroutine.h is copyright 1995,2000 Simon Tatham.
*
* Permission is hereby granted, free of charge, to any person
* obtaining a copy of this software and associated documentation
* files (the "Software"), to deal in the Software without
* restriction, including without limitation the rights to use,
* copy, modify, merge, publish, distribute, sublicense, and/or
* sell copies of the Software, and to permit persons to whom the
* Software is furnished to do so, subject to the following
* conditions:
*
* The above copyright notice and this permission notice shall be
* included in all copies or substantial portions of the Software.
*
* THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
* EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
* OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
 * NONINFRINGEMENT.  IN NO EVENT SHALL SIMON TATHAM BE LIABLE FOR
* ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF
* CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
* CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
*/
#ifndef COROUTINE_H
#define COROUTINE_H

#include <stdlib.h>

/* scr macros for static coroutines */
#define scrBegin     static int scrLine = 0; switch(scrLine) { case 0:;
#define scrFinish(z) } return (z)
#define scrFinishV   } return

#define scrReturn(z) \
    do { \
        scrLine=__LINE__; \
    return (z); case __LINE__:; \
    } while (0)

#define scrReturnV \
    do { \
        scrLine=__LINE__; \
        return; case __LINE__:; \
    } while (0)

/* ccr macros for re-entrant coroutines */
#define ccrContParam void **ccrParam
#define ccrBeginContext    struct ccrContextTag { int ccrLine
#define ccrEndContext(x) } *x = (struct ccrContextTag *)*ccrParam

#define ccrBegin(x) if(!x) {x = *ccrParam = malloc(sizeof(*x)); x->ccrLine = 0;} \
    if (x) switch(x->ccrLine) { case 0:;

#define ccrFinish(z) } free(*ccrParam); *ccrParam = 0; return (z)
#define ccrFinishV   } free(*ccrParam); *ccrParam = 0; return

#define ccrReturn(z) \
    do { \
        ((struct ccrContextTag *)*ccrParam)->ccrLine = __LINE__; \
    return (z); case __LINE__:; \
    } while (0)

#define ccrReturnV \
    do { \
        ((struct ccrContextTag *)*ccrParam)->ccrLine = __LINE__; \
    return; case __LINE__:; \
    } while (0)

#define ccrStop(z) do{ free(*ccrParam); *ccrParam = 0; return (z); }while(0)
#define ccrStopV   do{ free(*ccrParam); *ccrParam = 0; return; }while(0)

#define ccrContext    void *
#define ccrAbort(ctx) do { free (ctx); ctx = 0; } while (0)

#endif /* COROUTINE_H */
```