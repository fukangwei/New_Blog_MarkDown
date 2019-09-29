---
title: Tkinter标准对话框
categories: Tkinter编程
abbrlink: a8893bdb
date: 2019-04-12 10:40:25
---
&emsp;&emsp;`Tkinter`为了提供了三种标准对话框模块，它们分别是`messagebox`、`filedialog`和`colorchooser`。这三个模块原来是独立的，分别是`tkMessageBox`、`tkFileDialog`和`tkColorChooser`，需要导入才能使用。在`Python 3`之后，这些模块全部被收归到`tkinter`模块的麾下。下边的所有演示都是在`Python 3`下实现，如果你用的是`Python 2.x`，请在文件头`import tkMessageBox`，然后将`messagebox`替换为`tkMessageBox`即可。
<!--more-->

### messagebox(消息对话框)

&emsp;&emsp;下表列出了使用`messagebox`可以创建的所有标准对话框样式：

- `askokcancel(title, message, options)`：

<img src="./Tkinter标准对话框/1.png">

- `askquestion(title, message, options)`：

<img src="./Tkinter标准对话框/2.png">

- `askretrycancel(title, message, options)`：

<img src="./Tkinter标准对话框/3.png">

- `askyesno(title, message, options)`：

<img src="./Tkinter标准对话框/4.png">

- `showerror(title, message, options)`：

<img src="./Tkinter标准对话框/5.png">

- `showinfo(title, message, options)`：

<img src="./Tkinter标准对话框/6.png">

- `showwarning(title, message, options)`：

<img src="./Tkinter标准对话框/7.png">

#### 参数

&emsp;&emsp;所有的这些函数都有相同的参数：`title`参数是设置标题栏的文本；`message`参数是设置对话框的主要文本内容，你可以用`\n`来实现换行；`options`参数可以设置的选项和含义如下：

选项      | 含义
----------|-----
`default` | 设置默认的按钮(也就是按下回车响应的那个按钮)，默认是第一个按钮(像`确定`、`是`或`重试`)。可以设置的值根据对话框函数的不同可以选择`CANCEL`、`IGNORE`、`OK`、`NO`、`RETRY`或`YES`
`icon`    | 指定对话框显示的图标，可以指定的值有`ERROR`、`INFO`、`QUESTION`或`WARNING`。注意，不能指定自己的图标
`parent`  | 如果不指定该选项，那么对话框默认显示在根窗口上；如果想要将对话框显示在子窗口`w`上，那么可以设置`parent=w`

#### 返回值

&emsp;&emsp;`askokcancel`、`askretrycancel`和`askyesno`返回布尔类型的值：

- 返回`True`表示用户点击了`确定`或`是`按钮。
- 返回`False`表示用户点击了`取消`或`否`按钮。

`askquestion`返回`yes`或`no`字符串表示用户点击了`是`或`否`按钮；`showerror`、`showinfo`和`showwarning`返回`ok`表示用户按下了`是`按钮。

### filedialog(文件对话框)

&emsp;&emsp;当你的应用程序需要使用打开文件或保存文件的功能时，文件对话框显得尤为重要：

``` python
from tkinter import *
​
root = Tk()
​
def callback():
    fileName = filedialog.askopenfilename()
    print(fileName)
​
Button(root, text="打开文件", command=callback).pack()
​
mainloop()
```

<img src="./Tkinter标准对话框/8.jpg" height="240" width="334">

`filedialog`模块提供了两个函数：`askopenfilename(**option)`和`asksaveasfilename(**option)`，分别用于打开文件和保存文件。

#### 参数

&emsp;&emsp;两个函数可供设置的选项是一样的，下边列举了可用的选项及含义：

选项               | 含义
-------------------|-----
`defaultextension` | 指定文件的后缀，例如`defaultextension=".jpg"`，那么当用户输入一个文件名`FishC`的时候，文件名会自动添加后缀为`FishC.jpg`。注意，如果用户输入文件名包含后缀，那么该选项不生效
`filetypes`        | 指定筛选文件类型的下拉菜单选项，该选项的值是由`2`元祖构成的列表。每个`2`元祖由`(类型名, 后缀)`构成，例如`filetypes=[("PNG", ".png"), ("JPG", ".jpg"), ("GIF", ".gif")]`
`initialdir`       | 指定打开/保存文件的默认路径，默认路径是当前文件夹
`parent`           | 如果不指定该选项，那么对话框默认显示在根窗口上；如果想要将对话框显示在子窗口`w`上，那么可以设置`parent=w`
`title`            | 指定文件对话框的标题栏文本

#### 返回值

&emsp;&emsp;如果用户选择了一个文件，那么返回值是该文件的完整路径；如果用户点击了取消按钮，那么返回值是空字符串。

### colorchooser(颜色选择对话框)

&emsp;&emsp;颜色选择对话框提供一个友善的界面让用户选择需要的颜色：

``` python
from tkinter import *
​
root = Tk()
​
def callback():
    fileName = colorchooser.askcolor()
    print(fileName)
​
Button(root, text="选择颜色", command=callback).pack()
​
mainloop()
```

<img src="./Tkinter标准对话框/9.jpg" height="252" width="334">

#### 参数

&emsp;&emsp;`askcolor(color, **option)`函数的`color`参数用于指定初始化的颜色，默认是浅灰色；`option`参数可以指定的选项及含义如下：

选项     | 含义
---------|-----
`title`  | 指定颜色对话框的标题栏文本
`parent` | 如果不指定该选项，那么对话框默认显示在根窗口上；如果想要将对话框显示在子窗口`w`上，那么可以设置`parent=w`

#### 返回值

&emsp;&emsp;如果用户选择一个颜色并按下`确定`按钮后，返回值是一个`2`元祖，第`1`个元素是选择的`RGB`颜色值，第`2`个元素是对应的`16`进制颜色值；如果用户按下`取消`按钮，那么返回值是`(None, None)`。