---
title: Text文本
date: 2019-04-12 09:32:01
tags:
---
    Text(文本)组件用于显示和处理多行文本。在Tkinter的所有组件中，Text组件显得异常强大和灵活，适用于多种任务。虽然该组件的主要目的是显示多行文本，但它常常也被用于作为简单的文本编辑器和网页浏览器使用。
    Text组件用于显示文本文档，包含纯文本或格式化文本(使用不同字体、嵌入图片、显示链接甚至是带CSS格式的HTML等)，因此它常常也被用于作为简单的文本编辑器和网页浏览器使用。
  
### 用法

    当你创建一个Text组件的时候，它里边是没有内容的。为了给其插入内容，你可以使用insert方法以及INSERT或END索引号：

``` python
from tkinter import *
​
root = Tk()
​
text = Text(root)
text.pack()
​
# INSERT索引表示插入光标当前的位置
text.insert(INSERT, "I love ")
text.insert(END, "FishC.com!")
​
mainloop()
```

Text组件的insert方法有一个可选的参数，用于指定一个或多个“标签”(标签用于设置文本的格式，请参考下方“Tags用法”)到新插入的文本中：

``` python
from tkinter import *
​
root = Tk()
​
text = Text(root, width=20, height=5)
text.pack()
​
# 设置tag
text.tag_config("tag_1", backgroun="yellow", foreground="red")
​
# INSERT索引表示插入光标当前的位置
text.insert(INSERT, "I love ")
text.insert(END, "FishC.com!", "tag_1")
​
mainloop()
```

在Text组件中插入对象，可以使用window_create和image_create方法：

``` python
from tkinter import *
​
root = Tk()
​
text = Text(root, width=20, height=5)
text.pack()
​
text.insert(INSERT, "I love FishC.com!")
​
def show():
    print("哟，我被点了一下~")
​
b1 = Button(text, text="点我点我", command=show)
text.window_create(INSERT, window=b1)
​
mainloop()
```

    删除Text组件中的内容可以用delete方法，下边代码用于删除所有内容(也包含window和image对象，但不会删除marks的内容)：

``` python
text.delete(1.0, END)
```

删除单独一个字符(或者一个window对象，或者一个image对象)，你可以仅使用一个参数：

``` python
text.delete(b1)
```

将state选项从默认的NORMAL修改为DISABLED，使得Text组件中的内容为“只读”形式。不过需要注意的是，当你需要进行任何修改的时候，记得将state选项改回NORMAL，否则insert和delete方法都会失效。
    获得Text组件的内容，可以使用get方法(仅获取文本内容)：

``` python
contents = text.get(1.0, END)
```

在下边例子中，通过校检Text组件中文本的MD5摘要来判断内容是否发生改变：

``` python
from tkinter import *
import hashlib
​
root = Tk()
​
text = Text(root, width=20, height=5)
text.pack()
​
text.insert(INSERT, "I love FishC.com!")
contents = text.get(1.0, END)
​
def getSig(contents):
    m = hashlib.md5(contents.encode())
    return m.digest()
​
sig = getSig(contents)
​
def check():
    contents = text.get(1.0, END)
    if sig != getSig(contents):
        print("警报：内容发生变动！")
    else:
        print("风平浪静~")
​
Button(root, text="检查", command=check).pack()
​
mainloop()
```

index方法用于将所有支持的“索引”格式(请参考下方“Indexes用法”)转换为“行.列”格式的索引号：

``` python
print(text.index(INSERT))
text.insert(INSERT, "You are good!")
print(text.index(INSERT))
```

如果你需要跟踪一个位置，那么你可以将该位置“标记”下来(请参考下方“Marks用法”)：

``` python
text.insert(INSERT, "You are good!")
text.mark_set("here", '1.8')
text.insert("here", "very ")
```

使用search方法可以搜索Text组件中的内容。你可以提供一个确切的目标进行搜索(默认)，也可以使用Tcl格式的正则表达式进行搜索(需设置regexp选项为True)：

``` python
from tkinter import *
​
root = Tk()
​
text = Text(root, width=30, height=5)
text.pack()
​
text.insert(INSERT, "I love FishC.com!")
​
# 将任何格式的索引号统一为元祖(行,列)的格式输出
def getIndex(text, index):
    return tuple(map(int, str.split(text.index(index), ".")))
​
start = 1.0
while True:
    pos = text.search("o", start, stopindex=END)
    if not pos:
        break
    print("找到啦，位置是：", getIndex(text, pos))
    start = pos + "+1c"  # 将start指向下一个字符
​
mainloop()
```

如果忽略stopindex选项，表示直到文本的末尾结束搜索。设置backwards选项为True，则是修改搜索的方向(变为向后搜索，那么start变量你应该设置为END，stopindex选项设置为1.0，最后“+1c”改为“-1c”)。

### “恢复”和“撤销”操作  

    通过设置undo选项为True可以开启Text组件的“撤销”功能。然后用edit_undo方法实现“撤销”操作，用edit_redo方法实现“恢复”操作。这是因为Text组件内部有一个栈专门用于记录内容的每次变动，所以每次“撤销”操作就是一次弹栈操作，“恢复”就是再次压栈。

    默认情况下，每一次完整的操作将会放入栈中。但怎么样算是一次完整的操作呢？Tkinter觉得每次焦点切换、用户按下Enter键、删除/插入操作的转换等之前的操作算是一次完整的操作。也就是说你连续输入“FishC 是个P”的话，一次的“撤销”操作就会将所有的内容删除。
    那我们能不能自定义呢？比如我希望插入一个字符就算一次完整的操作，然后每次点击“撤销”就去掉一个字符。当然可以！做法就是先将autoseparators选项设置为False(因为这个选项是让Tkinter在认为一次完整的操作结束后自动插入“分隔符”)，然后绑定键盘事件，每次有输入就用edit_separator方法人为地插入一个“分隔符”：

``` python
from tkinter import *
​
root = Tk()
​
text = Text(root, width=30, height=5, autoseparators=False, undo=True, maxundo=10)
text.pack()
​
def callback(event):
    text.edit_separator()
​
text.bind('<Key>', callback)
​
text.insert(INSERT, "I love FishC")
​
def show():
    text.edit_undo()
​
Button(root, text="撤销", command=show).pack()
​
mainloop()
```

Indexes用法
    Indexes(索引)是用来指向Text组件中文本的位置，跟Python的序列索引一样，Text组件索引也是对应实际字符之间的位置。Tkinter提供一系列不同的索引类型：
“line.column”(行/列)
“line.end”(某一行的末尾)
INSERT
CURRENT
END
user-defined marks
user-defined tags("tag.first", "tag.last")
selection(SEL_FIRST, SEL_LAST)
window coordinate("@x,y")
embedded object name(window, images)
expressions

“line.column”
    “行/列”是最基础的索引方式，它们将索引位置的行号和列号以字符串的形式表示出来(中间以“.”分隔，例如“1.0”)。需要注意的是，行号以“1”开始，列号则以“0”开始。你还可以使用以下语法构建索引：
"%d.%d" % (line, column)
指定超出现有文本的最后一行的行号，或超出一行中列数的列号都不会引发错误。对于这样的指定，Tkinter解释为已有内容的末尾的下一个位置。
    需要注意的是，使用“行/列”的索引方式看起来像是浮点值。其实不只像而已，你在需要指定索引的时候使用浮点值代替也是可以的：
text.insert(INSERT, "I love FishC")
print(text.get("1.2", 1.6))

使用index方法可以将所有支持的“索引”格式转换为“行/列”格式的索引号。

“line.end” 
    行号加上字符串“.end”的格式表示为该行最后一个字符的位置：
text.insert(INSERT, "I love FishC")
print(text.get("1.2", "1.end"))


其他

INSERT(或“insert”)：对应插入光标的位置。
CURRENT(或“current”)：对应与鼠标坐标最接近的位置。不过，如果你紧按鼠标任何一个按钮，它会直到你松开它才响应。
END(或“end”)：对应Text组件的文本缓冲区最后一个字符的下一个位置。 
“user-defined marks”：“user-defined marks”是对Text组件中位置的命名。INSERT和CURRENT是两个预先命名好的marks，除此之外你可以自定义marks(请参考下方“Marks用法”)。
“User-defined tags”：“User-defined tags”代表可以分配给Text组件的特殊事件绑定和风格(请参考下方“Tags用法”)。你可以使用“tag.first”(使用tag的文本的第一个字符之前)和“tag.last”(使用tag的文本的最后一个字符之后)语法表示标签的范围。
"%s.first" % tagname
"%s.last" % tagname
如果查无此tag，那么Tkinter会抛出一个TclError异常。
selection(SEL_FIRST, SEL_LAST)：selection是一个名为SEL(或“sel”)的特殊tag，表示当前被选中的范围，你可以使用SEL_FIRST到SEL_LAST来表示这个范围。如果没有选中的内容，那么Tkinter会抛出一个TclError异常。
“window coordinate("@x,y")”：你还可以使用窗口坐标作为索引。例如在一个事件绑定中，你可以使用以下代码找到最接近鼠标位置的字符：
"@%d,%d" % (event.x, event.y)
“embedded object name(window, images)”：“embedded object name”用于指向在Text组件中嵌入的window和image对象。要引用一个window，只要简单地将一个Tkinter组件实例作为索引即可。引用一个嵌入的image，只需使用相应的PhotoImage和BitmapImage对象。
expressions：expressions用于修改任何格式的索引，用字符串的形式实现修改索引的表达式。具体表达式实现如下：
表达式             含义
-----------------------
"+ count chars"    1、将索引向前(->)移动count个字符
                   2、可以越过换行符，但不能超过END的位置
"- count chars"    1、将索引向后(<-)移动count个字符
                   2、可以越过换行符，但不能超过"1.0"的位置
"+ count lines"    1、将索引向前(->)移动count行
                   2、索引会尽量保持与移动前在同一列上，但如果移动后的那一行字符太少，将移动到该行的末尾
"- count lines"    1、将索引向后(<-)移动count行
                   2、索引会尽量保持与移动前在同一列上，但如果移动后的那一行字符太少，将移动到该行的末尾
" linestart"       1、将索引移动到当前索引所在行的起始位置
                   2、注意，使用该表达式前边必须有一个空格隔开
" lineend"         1、将索引移动到当前索引所在行的末尾
                   2、注意，使用该表达式前边必须有一个空格隔开
" wordstart"       1、将索引移动到当前索引指向的单词的开头
                   2、单词的定义是一系列字母、数字、下划线或任何非空白字符的组合
                   3、注意，使用该表达式前边必须有一个空格隔开
" wordend"         1、将索引移动到当前索引指向的单词的末尾
                   2、单词的定义是一系列字母、数字、下划线或任何非空白字符的组合
                   3、注意，使用该表达式前边必须有一个空格隔开
只要结果不产生歧义，关键字可以被缩写，空格也可以省略。例如：“+ 5 chars”可以简写成“+5c”。
    在实现中，为了确保表达式为普通字符串，你可以使用str或格式化操作来创建一个表达式字符串。下边例子演示了如何删除插入光标前边的一个字符：
def backspace(event):
    event.widget.delete("%s-1c" % INSERT, INSERT)

Marks用法
    Marks(标记)通常是嵌入到Text组件文本中的不可见对象。事实上Marks是指定字符间的位置，并跟随相应的字符一起移动。Marks有INSERT、CURRENT和“user-defined marks”(用户自定义的Marks)。其中，INSERT和CURRENT是Tkinter预定义的特殊Marks，它们不能够被删除。
    INSERT(或“insert”)用于指定当前插入光标的位置，Tkinter会在该位置绘制一个闪烁的光标，因此并不是所有的Marks都不可见。
    CURRENT(或“current”)用于指定与鼠标坐标最接近的位置。不过，如果你紧按鼠标任何一个按钮，它会直到你松开它才响应。 
    你还可以自定义任意数量的Marks，Marks的名字是由普通字符串组成，可以是除了空白字符外的任何字符(为了避免歧义，你应该起一个有意义的名字)。使用mark_set方法创建和移动Marks。如果你在一个Mark标记的位置之前插入或删除文本，那么Mark跟着一并移动。删除Marks你需要使用mark_unset方法，删除Mark周围的文本并不会删除Mark本身。
    Mark事实上就是索引，用于表示位置：
text.insert(INSERT, "I love FishC")
text.mark_set("here", "1.2")
text.insert("here", "插")

    如果Mark前边的内容发生改变，那么Mark的位置也会跟着移动：
text.insert(INSERT, "I love FishC")
text.mark_set("here", "1.2")
text.insert("here", "插")
text.insert("here", "入")

    如果Mark周围的文本被删除了，Mark仍然还在，但初始化为“1.0”：
text.insert(INSERT, "I love FishC")
text.mark_set("here", "1.2")
text.insert("here", "插")
​
text.delete("1.0", END)
text.insert("here", "入")

    只有mark_unset方法可以解除Mark的封印：
text.insert(INSERT, "I love FishC")
text.mark_set("here", "1.2")
text.insert("here", "插")
​
text.mark_unset("here")
​
text.delete("1.0", END)
text.insert("here", "入")
 
    默认插入内容到Mark，是插入到它的左侧(就是说插入一个字符的话，Mark向后移动了一个字符的位置)。那能不能插入到Mark的右侧呢？其实是可以的，通过mark_gravity方法就可以实现：
text.insert(INSERT, "I love FishC")
​
text.mark_set("here", "1.2")
text.mark_gravity("here", LEFT)
​
text.insert("here", "插")
text.insert("here", "入")


Tags用法
    Tags(标签)通常用于改变Text组件中内容的样式和功能。你可以修改文本的字体、尺寸和颜色。另外，Tags还允许你将文本、嵌入的组件和图片与键盘和鼠标等事件相关联。除了“user-defined tags”(用户自定义的Tags)，还有一个预定义的特殊Tag，即SEL。SEL(或“sel”)用于表示对应的选中内容(如果有的话)。
    你可以自定义任意数量的Tags，Tags的名字是由普通字符串组成，可以是除了空白字符外的任何字符。另外，任何文本内容都支持多个Tags描述，任何Tag也可以用于描述多个不同的文本内容。
    为指定文本添加Tags可以使用tag_add方法：
text.insert(INSERT, "I love FishC.com!")
text.tag_add("tag1", "1.7", "1.12", "1.14")
text.tag_config("tag1", background="yellow", foreground="red")

使用tag_config方法可以设置Tags的样式。下边列举了tag_congif方法可以使用的选项：
选项           含义
-------------------
background     1、指定该Tag所描述的内容的背景颜色
               2、注意：bg并不是该选项的缩写，在这里bg被解释为bgstipple选项的缩写
bgstipple      1、指定一个位图作为背景，并使用background选项指定的颜色填充
               2、只有设置了background选项该选项才会生效
               3、默认的标准位图有“error”、“gray75”、“gray50”、“gray25”、“gray12”、“hourglass”、“info”、“questhead”、“question”和“warning”
borderwidth    1、指定文本框的宽度
               2、默认值是0
               3、只有设置了relief选项该选项才会生效
               4、注意：该选项不能使用bd缩写
fgstipple      1、指定一个位图作为前景色
               2、默认的标准位图有“error”、“gray75”、“gray50”、“gray25”、“gray12”、“hourglass”、“info”、“questhead”、“question”和“warning”
font           指定该Tag所描述的内容使用的字体
foreground     1、指定该Tag所描述的内容的前景色
               2、注意：fg并不是该选项的缩写，在这里fg被解释为fgstipple选项的缩写
justify        1、控制文本的对齐方式
               2、默认是LEFT(左对齐)，还可以选择RIGHT(右对齐)和CENTER(居中)
               3、注意：需要将Tag指向该行的第一个字符，该选项才能生效
lmargin1       1、设置Tag指向的文本块第一行的缩进
               2、默认值是0
               3、注意：需要将Tag指向该文本块的第一个字符或整个文本块，该选项才能生效
lmargin2       1、设置Tag指向的文本块除了第一行其他行的缩进
               2、默认值是0
               3、注意：需要将Tag指向整个文本块，该选项才能生效
offset         1、设置Tag指向的文本相对于基线的偏移距离
               2、可以控制文本相对于基线是升高(正数值)或者降低(负数值)
               3、默认值是0
overstrike     1、在Tag指定的文本范围画一条删除线
               2、默认值是False
relief         1、指定Tag对应范围的文本的边框样式
               2、可以使用的值有：SUNKEN、RAISED、GROOVE、RIDGE或FLAT
               3、默认值是FLAT(没有边框)
rmargin        1、设置Tag指向的文本块右侧的缩进
               2、默认值是0
spacing1       1、设置Tag所描述的文本块中每一行与上方的空白间隔
               2、注意：自动换行不算
               3、默认值是0
spacing2       1、设置Tag所描述的文本块中自动换行的各行间的空白间隔
               2、注意：换行符(“\n”)不算
               3、默认值是0
spacing3       1、设置Tag所描述的文本块中每一行与下方的空白间隔
               2、注意：自动换行不算
               3、默认值是0
tabs           1、定制Tag所描述的文本块中Tab按键的功能
               2、默认Tab被定义为8个字符的宽度
               3、你还可以定义多个制表位：“tabs=('3c', '5c', '12c')”表示前3个Tab宽度分别为3厘米、5厘米、12厘米，接着的Tab按照最后两个的差值计算，即19厘米、26厘米和33厘米
               4、你应该注意到了，它上边“c”的含义是“厘米”而不是“字符”，还可以选择的单位有“i”(英寸)、“m”(毫米)和“p”(DPI，大约是“1i”等于“72p”)
               5、如果是一个整型值，则单位是像素
underline      1、该选项设置为True的话，则Tag所描述的范围内文本将被画上下划线
               2、默认值是False
wrap           1、设置当一行文本的长度超过width选项设置的宽度时，是否自动换行
               2、该选项的值可以是：NONE(不自动换行)，CHAR(按字符自动换行)和WORD(按单词自动换行)
如果你对同一个范围内的文本加上多个Tags，并且设置相同的选项，那么新创建的Tag样式会覆盖比较旧的Tag：
text.tag_config("tag1", background="yellow", foreground="red")  # 旧的Tag
text.tag_config("tag2", foreground="blue")  # 新的Tag
​
# 那么新创建的Tag2会覆盖比较旧的Tag1的相同选项
# 注意，与下边的调用顺序没有关系
text.insert(INSERT, "I love FishC.com!", ("tag2", "tag1"))

    你或许想控制Tags间的优先级，这可以实现吗？完全没有问题，可以使用tag_raise和tag_lower方法来提高和降低某个Tag的优先级：
text.tag_config("tag1", background="yellow", foreground="red")
text.tag_config("tag2", foreground="blue")
​
text.tag_lower("tag2")
​
text.insert(INSERT, "I love FishC.com!", ("tag2", "tag1"))

另外Tags还支持事件绑定，使用的是tag_bind的方法。下边例子中将文本“FishC.com”与鼠标事件进行绑定，当鼠标进入该文本段的时候，鼠标样式切换为“arrow”形态，离开文本段的时候切换回“xterm”形态。当触发鼠标“左键点击操作”事件的时候，使用默认浏览器打开鱼C工作室的首页(www.fishc.com)：
from tkinter import *
import webbrowser
​
root = Tk()
​
text = Text(root, width=30, height=5)
text.pack()
​
text.insert(INSERT, "I love FishC.com!")
​
text.tag_add("link", "1.7", "1.16")
text.tag_config("link", foreground="blue", underline=True)
​
def show_hand_cursor(event):
    text.config(cursor="arrow")
​
def show_arrow_cursor(event):
    text.config(cursor="xterm")
​
def click(event):
    webbrowser.open("http://www.fishc.com")
​
text.tag_bind("link", "<Enter>", show_hand_cursor)
text.tag_bind("link", "<Leave>", show_arrow_cursor)
text.tag_bind("link", "<Button-1>", click)
​
mainloop()


参数
    Text(master=None, **options) (class)：master是父组件；options是组件选项，下方表格列举了各个选项的具体含义和用法：
选项                   含义
---------------------------
autoseparators         1、指定实现“撤销”操作的时候是否自动插入一个“分隔符”(用于分隔操作记录)
                       2、默认值是True
                       3、详见上方用法[“撤销”和“恢复”操作]
background             1、设置Text组件的背景颜色
                       2、注意：通过使用Tags可以使Text组件中的文本支持多种背景颜色显示(请参考上方[Tags用法])
bg                     跟background一样
borderwidth            1、设置Entry的边框宽度
                       2、默认值是1像素
bd                     跟borderwidth一样
cursor                 1、指定当鼠标在Text组件上飘过的时候的鼠标样式
                       2、默认值由系统指定
exportselection        1、指定选中的文本是否可以被复制到剪贴板
                       2、默认值是True    
                       3、可以修改为False表示不允许复制文本
font                   1、设置Text组件中文本的默认字体
                       2、注意：通过使用Tags可以使Text组件中的文本支持多种字体显示(请参考上方[Tags用法])
foreground             1、设置Text组件中文本的颜色
                       2、注意：通过使用Tags可以使Text组件中的文本支持多种颜色显示(请参考上方[Tags用法])
fg                     跟foreground一样
height                 1、设置Text组件的高度
                       2、注意：单位是行数，不是像素
highlightbackground    1、指定当Text组件没有获得焦点的时候高亮边框的颜色
                       2、默认值由系统指定
highlightcolor         1、指定当Text组件获得焦点的时候高亮边框的颜色
                       2、默认值由系统指定
highlightthickness     1、指定高亮边框的宽度
                       2、默认值是0
insertbackground       1、设置插入光标的颜色
                       2、默认是BLACK(或“black”)
insertborderwidth      1、设置插入光标的边框宽度
                       2、默认值是0
                       3、提示：需要设置insertwidth选项为比较大的数值才能看出来噢
insertofftime          1、该选项控制光标的闪烁频率(灭)
                       2、单位是毫秒
insertontime           1、该选项控制光标的闪烁频率(亮)
                       2、单位是毫秒
insertwidth            1、指定光标的宽度
                       2、默认值是2像素
maxundo                1、设置允许“撤销”操作的最大次数
                       2、默认值是0
                       3、设置为“-1”，表示不限制
padx                   1、指定水平方向上的额外间距(内容和边框间)
                       2、默认值是1
pady                   1、指定垂直方向上的额外间距(内容和边框间)
                       2、默认值是1
relief                 1、指定边框样式
                       2、默认值是SUNKEN
                       3、其他可以选择的值是FLAT、RAISED、GROOVE和RIDGE
selectbackground       1、指定被选中文本的背景颜色
                       2、默认值由系统指定
selectborderwidth      1、指定被选中文本的边框宽度
                       2、默认值是0
selectforeground       1、指定被选中文本的字体颜色
                       2、默认值由系统指定
setgrid                1、指定一个布尔类型的值，确定是否启用网格控制
                       2、默认值是False
spacing1               1、指定Text组件的文本块中每一行与上方的空白间隔
                       2、注意：自动换行不算
                       3、默认值是0
spacing2               1、指定Text组件的文本块中自动换行的各行间的空白间隔
                       2、注意：换行符(“\n”)不算
                       3、默认值是0
spacing3               1、指定Text组件的文本中每一行与下方的空白间隔
                       2、注意：自动换行不算
                       3、默认值是0
state                  1、默认情况下Text组件响应键盘和鼠标事件(NORMAL)
                       2、如果将该选项的值设置为DISABLED，那么上述响应就不会发生，并且你无法修改里边的内容
tabs                   1、定制Tag所描述的文本块中Tab按键的功能
                       2、默认Tab被定义为8个字符的宽度
                       3、你还可以定义多个制表位：“tabs=('3c', '5c', '12c')”表示前3个Tab宽度分别为3厘米、5厘米、12厘米，接着的Tab按照最后两个的差值计算，即19厘米、26厘米和33厘米
                       4、你应该注意到了，它上边“c”的含义是“厘米”而不是“字符”，还可以选择的单位有“i”(英寸)、“m”(毫米)和“p”(DPI，大约是“1i”等于“72p”)
                       5、如果是一个整型值，则单位是像素
takefocus              1、指定使用Tab键可以将焦点移动到Text组件中
                       2、默认是开启的，可以将该选项设置为False避免焦点在此Text组件中
undo                   1、该选项设置为True开启“撤销”功能
                       2、该选项设置为False关闭“撤销”功能
                       3、默认值是False
width                  1、设置Text组件的宽度
                       2、注意：单位是字符数，因此Text组件的实际宽度还取决于字体的大小
wrap                   1、设置当一行文本的长度超过width选项设置的宽度时，是否自动换行
                       2、该选项的值可以是：NONE(不自动换行)、CHAR(按字符自动换行)和WORD(按单词自动换行)
xscrollcommand         1、与scrollbar(滚动条)组件相关联(水平方向)
                       2、使用方法可以参考Scrollbar组件
yscrollcommand         1、与scrollbar(滚动条)组件相关联(垂直方向)
                       2、使用方法可以参考Scrollbar组件

方法
bbox(index)：返回给定索引指定的字符的边界框，返回值是一个4元组(x, y, width, height)。如果该字符是不可见的，那么返回None。注意，只有当Text组件被更新的时候该方法才有效，可以使用update_idletasks(方法先更新Text组件。 
compare(index1, op, index2)：返回对比index1和index2指定的两个字符的结果。op是操作符“<”、“<=”、“==”、“>=”、“>”或“!=”(不支持Python的“<>”操作符)。返回布尔类型的值表示对比的结果。
debug(boolean=None)：开启或关闭Debug状态。
delete(start, end=None)：删除给定范围的文本或嵌入对象。如果在给定范围内有任何Marks标记的位置，则将Marks移动到start参数开始的位置。
dlineinfo(index)：返回给定索引指定的字符所在行的边界框，返回值是一个5元组(x, y, width, height, offset)，offset表示从该行的顶端到基线的偏移。如果该行不可见，则返回None。注意，只有当Text组件被更新的时候该方法才有效，可以使用update_idletasks方法先更新Text组件。
dump(index1, index2=None, command=None, **kw)：返回index1和index2之间的内容。返回的值是一个由3元组(关键词，值，索引组成的列表，关键词参数的顺序为all、image、mark、tag、text、window。默认关键词是“all”，表示全部关键词均为选中状态。如果需要筛选个别关键词，可以用dump(index1, index2, image=True, text=True)这样的形式调用。如果指定了command函数，那么会为列表中的每一个三元组作为参数调用一次该函数(这种情况下，dump不返回值)。
edit_modified(arg=None)：该方法用于查询和设置modified标志(该标志用于追踪Text组件的内容是否发生变化)。如果不指定arg参数，那么返回modified标志是否被设置。你可以传递显式地使用True或False作为参数来设置或清除modified标志，任何代码或用户的插入或删除文本操作，“撤销”或“恢复”操作，都会使得modified标志被设置。
edit_redo(self)：“恢复”上一次的“撤销”操作，如果undo选项为False，该方法无效。详见上方用法[“撤销”和“恢复”操作]。
edit_reset：清空存放操作记录的栈。
edit_separator：插入一个“分隔符”到存放操作记录的栈中，用于表示已经完成一次完整的操作。如果undo选项为False，该方法无效。详见上方用法[“撤销”和“恢复”操作]。
edit_undo：撤销最近一次操作，如果undo选项为False，该方法无效。详见上方用法[“撤销”和“恢复”操作]。
get(index1, index2=None)：返回index1到index2(不包含)之间的文本。如果index2参数忽略，则返回一个字符；如果包含image和window的嵌入对象，均被忽略；如果包含有多行文本，那么自动插入换行符(“\n”)。
image_cget(index, option)：返回index参数指定的嵌入image对象的option选项的值。如果给定的位置没有嵌入image对象，则抛出TclError异常。
image_configure(index, **options)：修改index参数指定的嵌入image对象的一个或多个option选项的值。如果给定的位置没有嵌入image对象，则抛出TclError异常。
image_create(index, cnf={}, **kw)：在index参数指定的位置嵌入一个image对象，该image对象必须是Tkinter的PhotoImage或BitmapImage实例。
可选选项align：设定此图像的垂直对齐，可以是TOP、CENTER、BOTTOM或BASELINE
可选选项image：PhotoImage或BitmapImage对象
可选选项name：你可以为该图像实例命名，如果你忽略此选项，那么Tkinter会自动为其取一个独一无二的名字
可选选项padx：设置水平方向上的额外间距
可选选项pady：设置垂直方向上的额外间距
image_names：返回Text组件中嵌入的所有image对象的名字。
index(index)：将index参数指定的位置以“line.column”的索引形式返回，index参数支持任何格式的索引。
insert(index, text, *tags)：在index参数指定的位置插入字符串，可选参数tags用于指定文本的样式，详见上方“Tags用法”。
mark_gravity(self, markName, direction=None)：设置Mark的方向，可以是LEFT或RIGHT(默认是RIGHT，即如果在Mark处插入文本的话，Mark将发生相应的移动以保持在插入文本的右侧)。如果设置为LEFT，那么在Mark处插入文本并不会移动Mark(因为Mark在插入文本的左侧)。如果忽略direction参数，则返回指定Mark的方向，详见上方“Marks用法”。
mark_names：返回Text组件中所有Marks的名字，包括两个特殊Mark(INSERT和CURRENT)。注意，END是特殊的索引，不是Mark。
mark_next(index)：返回在index指定的位置后边的一个Mark的名字，如果不存在，则返回空字符串。
mark_previous(index)：返回在index指定的位置前边的一个Mark的名字，如果不存在，则返回空字符串。
mark_set(markName, index)：移动Mark到index参数指定的位置。如果markName参数指定的Mark不存在，则创建一个新的Mark。
mark_unset(*markNames)：删除markNames指定的Marks，不能删除预定义的INSERT和CURRENT。
replace(index1, index2, chars, *args)：将index1到index2之间的内容替换为chars参数指定的字符串。如果需要为替换的内容添加Tag，可以在args参数指定Tag，详见上方“Tags用法”。
scan_dragto(x, y)：详见下方scan_mark(x, y)。
scan_mark(x, y)：使用这种方式来实现Text组件内容的滚动。需要将鼠标按钮事件以及鼠标当前位置绑定到scan_mark(x, y)方法，然后将motion事件及当前鼠标位置绑定到scan_dragto(x, y)方法，就可以实现Text组件的内容在当前位置和scan_mark(x, y)指定的位置(x, y)之间滚动。
search(pattern, index, stopindex=None, forwards=None, backwards=None, exact=None, regexp=None, nocase=None, count=None)：从index开始搜索pattern，到stopindex结束(不指定表示搜索到末尾)。如果成功找到，以“line.column”返回第一个匹配的字符，否则返回空字符串。
forwards参数设置为True，表示向前(->)搜索
backwards参数设置为True，表示向后(<-)搜索
exact参数设置为True，表示搜索与pattern完全匹配的结果
regexp参数设置为True，则pattern被解释为Tcl格式的正则表达式
nocase参数设置为True，表示忽略大小写，默认是区分大小写的搜索
count参数指定为一个IntVar的Tkinter变量，用于存放当找到匹配的字符个数(如果匹配结果中没有嵌入的image或window对象的话，一般该值等于pattern的字符个数)
see(index)：滚动内容，确保index指定的位置可见。
tag_add(tagName, index1, index2=None)：为index1到index2之间的内容添加一个Tag(tagName参数指定)。如果index2参数忽略，则单独为index1指定的内容添加Tag，详见上方“Tags用法”。
tag_bind(tagName, sequence, func, add=None)：为Tag绑定事件，详见上方“Tags用法”。
tag_cget(tagName, option)：返回tagName指定的option选项的值。
tag_config(tagName, cnf=None, **kw)：跟tag_configure(tagName, cnf=None, **kw)一样。
tag_configure(tagName, cnf=None, **kw)：设置tagName的选项，详见上方“Tags用法”。
tag_delete(*tagNames)：删除tagNames指定的Tags。
tag_lower(tagName, belowThis=None)：降低Tag的优先级。如果belowThis参数不为空，则表示tagName需要比belowThis指定的Tag优先级更低，详见上方“Tags用法”。
tag_names(index=None)：如果不带参数，表示返回Text组件中所有Tags的名字。index参数表示返回该位置上所有的Tags的名字。
tag_nextrange(tagName, index1, index2=None)：在index1到index2的范围内第一个tagName的位置，如果没有，则返回空字符串。
tag_prevrange(tagName, index1, index2=None)：tag_nextrange的反向查找，也就是查找范围是index2到index1。
tag_raise(tagName, aboveThis=None)：提高Tag的优先级。如果aboveThis参数不为空，则表示tagName需要比aboveThis指定的Tag优先级更高，详见上方“Tags用法”。
tag_ranges(tagName)：返回所有tagName指定的文本，并将它们的范围以列表的形式返回。
tag_remove(tagName, index1, index2=None)：删除index1到index2之间所有的tagName。如果忽略index2参数，那么只删除index1指定的那个字符的tagName(如果有的话)。
tag_unbind(tagName, sequence, funcid=None)：解除与tagName绑定的事件(sequence指定)。
window_cget(index, option)：返回index参数指定的嵌入window对象的option选项的值。如果给定的位置没有嵌入window对象，则抛出TclError异常。
window_config(index, cnf=None, **kw)：跟window_configure(index, cnf=None, **kw)一样。
window_configure(index, cnf=None, **kw)：修改index参数指定的嵌入window对象的一个或多个option选项的值。如果给定的位置没有嵌入window对象，则抛出TclError异常。
window_create(index, **options)：在index参数指定的位置嵌入一个window对象。支持两种方式在Text组件中嵌入window对象(请看下方create选项和window选项的描述)：
可选选项align：设定此图像的垂直对齐，可以是TOP、CENTER、BOTTOM或BASELINE
可选选项create：指定一个回调函数用于创建嵌入的window组件，该函数没有参数，并且必须创建Text的子组件并返回
可选选项padx：设置水平方向上的额外间距
可选选项pady：设置垂直方向上的额外间距
可选选项stretch：该选项控制当行的高度大于嵌入组件的高度时，嵌入组件是否延伸。默认值是False，表示组件保持原形；设置为True表示将该组件垂直部分延伸至行的高度
可选选项window：指定一个已经创建好的window组件，该组件必须是Text组件的子组件
window_names：返回Text组件中嵌入的所有window对象的名字。
xview(*args)：该方法用于在水平方向上滚动Text组件的内容，一般通过绑定Scollbar组件的command选项来实现(具体操作参考Scrollbar)。如果第一个参数是MOVETO，则第二个参数表示滚动到指定的位置：0.0表示最左端，1.0表示最右端；如果第一个参数是SCROLL，则第二个参数表示滚动的数量，第三个参数表示滚动的单位(可以是UNITS或PAGES)，例如xview(SCROLL, 3, UNITS)表示向右滚动三行。
xview_moveto(fraction)：跟xview(MOVETO, fraction)一样。
xview_scroll(number, what)：跟xview(SCROLL, number, what)一样。
yview(*args)：该方法用于在垂直方向上滚动Text组件的内容，一般通过绑定Scollbar组件的command选项来实现(具体操作参考Scrollbar)。如果第一个参数是MOVETO，则第二个参数表示滚动到指定的位置：0.0表示最顶端，1.0表示最底端；如果第一个参数是SCROLL，则第二个参数表示滚动的数量，第三个参数表示滚动的单位(可以是UNITS或PAGES)，例如yview(SCROLL, 3, PAGES)表示向下滚动三页。
yview_moveto(fraction)：跟yview(MOVETO, fraction)一样。
yview_scroll(number, what)：跟yview(SCROLL, number, what)一样。
