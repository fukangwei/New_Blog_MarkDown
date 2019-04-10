---
title: Tkinter的事件绑定
date: 2019-04-09 13:59:19
tags:
---
&emsp;&emsp;正如我们此前提到的，一个`Tkinter`应用程序大部分时间花费在事件循环中(通过`mainloop`方法进入)。事件可以有各种来源，包括用户触发的鼠标和键盘操作和窗口管理器触发的重绘事件(在多数情况下是由用户间接引起的)。
&emsp;&emsp;`Tkinter`提供一个强大的机制可以让你自由地处理事件，对于每个组件来说，你可以通过`bind`方法将函数或方法绑定到具体的事件上。
<!--more-->

``` python
widget.bind(event, handler)
```

当被触发的事件满足该组件绑定的事件时，`Tkinter`就会带着事件对象(`Event`)去调用`handler`方法。在下面的例子中，我们使用`Frame`组件的`bind`方法将鼠标点击事件(`Button-1`)和自定义的`callback`方法绑定起来。

``` python
# 捕获点击鼠标的位置
from tkinter import *
​
root = Tk()
​
def callback(event):
    print("点击位置：", event.x, event.y)
​
frame = Frame(root, width=200, height=200)
frame.bind("<Button-1>", callback)
frame.pack()
​
mainloop()
```

&emsp;&emsp;只有当组件获得焦点的时候才能接收键盘事件(`Key`)，下面的例子中，我们用`focus_set`获得焦点，你可以设置`Frame`的`takefocus`选项为`True`，然后使用`Tab`将焦点转移上来。

``` python
# 捕获键盘事件
from tkinter import *
​
root = Tk()
​
def callback(event):
    print("敲击位置：", repr(event.char))
​
frame = Frame(root, width=200, height=200)
frame.bind("<Key>", callback)
frame.focus_set()
frame.pack()
​
mainloop()
```

&emsp;&emsp;下面的例子展示捕获鼠标在组件上的运动轨迹，这里需要关注的是`Motion`事件：

``` python
from tkinter import *
​
root = Tk()
​
def callback(event):
    print("当前位置：", event.x, event.y)
​
frame = Frame(root, width=200, height=200)
frame.bind("<Motion>", callback)
frame.pack()
​
mainloop()
```

### 事件序列

&emsp;&emsp;`Tkinter`使用一种称为事件序列的机制来允许用户定义事件，用户需使用`bind`方法将具体的事件序列与自定义的方法相绑定。事件序列是以字符串的形式表示的，可以表示一个或多个相关联的事件(如果是多个事件，那么对应的方法只有在满足所有事件的前提下才会被调用)。事件序列使用以下语法描述：

``` html
<modifier-type-detail>
```

- 事件序列是包含在尖括号(`<...>`)中。
- `type`部分的内容是最重要的，它通常用于描述普通的事件类型，例如鼠标点击或键盘按键点击。
- `modifier`部分的内容是可选的，它通常用于描述组合键，例如`Ctrl + C`，`Shift + 鼠标左键点击`。
- `detail`部分的内容是可选的，它通常用于描述具体的按键，例如`Button-1`表示鼠标左键。

事件序列                      | 含义
-----------------------------|-----
`<Button-1>`                 | 用户点击鼠标左键
`<KeyPress-H>`               | 用户点击`H`按键
`<Control-Shift-KeyPress-H>` | 用户同时点击`Ctrl + Shift + H`

### type

type            | 含义
----------------|-------
`Activate`      | 当组件的状态从`未激活`变为`激活`的时候触发该事件
`Button`        | 当用户点击鼠标按键的时候触发该事件。`detail`部分指定具体的按键：`<Button-1>`鼠标左键、`<Button-2>`鼠标中键、`<Button-3>`鼠标右键、`<Button-4>`滚轮上滚(`Linux`)、`<Button-5>`滚轮下滚(`Linux`)
`ButtonRelease` | 当用户释放鼠标按键的时候触发该事。在大多数情况下，比`Button`要更好用，因为如果当用户不小心按下鼠标，用户可以将鼠标移出组件再释放鼠标，从而避免不小心触发事件
`Configure`     | 当组件的尺寸发生改变的时候触发该事件
`Deactivate`    | 当组件的状态从`激活`变为`未激活`的时候触发该事件
`Destroy`       | 当组件被销毁的时候触发该事件
`Enter`         | 当鼠标指针进入组件的时候触发该事件。注意，不是指用户按下回车键
`Expose`        | 当窗口或组件的某部分不再被覆盖的时候触发该事件
`FocusIn`       | 当组件获得焦点的时候触发该事件。用户可以用`Tab`键将焦点转移到该组件上(需要该组件的`takefocus`选项为`True`)；你也可以调用`focus_set`方法使该组件获得焦点
`FocusOut`      | 当组件失去焦点的时候触发该事件
`KeyPress`      | 当用户按下键盘按键的时候触发该事件；`detail`可以指定具体的按键，例如`<KeyPress-H>`表示当大写字母`H`被按下的时候触发该事件；`KeyPress`可以简写为`Key`
`KeyRelease`    | 当用户释放键盘按键的时候触发该事件
`Leave`         | 当鼠标指针离开组件的时候触发该事件
`Map`           | 当组件被映射的时候触发该事件，意思是在应用程序中显示该组件的时候，例如调用`grid`方法
`Motion`        | 当鼠标在组件内移动的整个过程均触发该事件
`MouseWheel`    | 当鼠标滚轮滚动的时候触发该事件；目前该事件仅支持`Windows`和`Mac`系统，`Linux`系统请参考`Button`
`Unmap`         | 当组件被取消映射的时候触发该事件；意思是在应用程序中不再显示该组件的时候，例如调用`grid_remove`方法
`Visibility`    | 当应用程序至少有一部分在屏幕中是可见的时候触发该事件

### modifier

&emsp;&emsp;在事件序列中，`modifier`部分的内容可以是以下这些：

modifier  | 含义
----------|------
`Alt`     | 当按下`Alt`按键的时候
`Any`     | 表示任何类型的按键被按下的时候，例如`<Any-KeyPress>`表示当用户按下任何按键时触发事件
`Control` | 当按下`Ctrl`按键的时候
`Double`  | 当后续两个事件被连续触发的时候，例如`<Double-Button-1>`表示当用户双击鼠标左键时触发事件
`Lock`    | 当打开大写字母锁定键(`CapsLock`)的时候
`Shift`   | 当按下`Shift`按键的时候
`Triple`  | 跟`Double`类似，当后续三个事件被连续触发的时候

### Event对象

&emsp;&emsp;当`Tkinter`去回调你定义的函数的时候，都会带着`Event`对象(作为参数)去调用，可以使用`Event`对象的以下这些属性：

属性             | 含义
-----------------|-----
`widget`         | 产生该事件的组件
`x, y`           | 当前的鼠标位置坐标(相对于窗口左上角，像素为单位)
`x_root, y_root` | 当前的鼠标位置坐标(相对于屏幕左上角，像素为单位)
`char`           | 按键对应的字符(键盘事件专属)
`keysym`         | 按键名，见下方`Key names`(键盘事件专属)
`keycode`        | 按键码，见下方`Key names`(键盘事件专属)
`num`            | 按钮数字(鼠标事件专属)
`width, height`  | 组件的新尺寸(`Configure`事件专属)
`type`           | 该事件类型

### Key names

&emsp;&emsp;当事件为`Key`、`KeyPress`和`KeyRelease`的时候，`detail`可以通过设定具体的按键名(`keysym`)来筛选。例如`Key-H`表示按下键盘上的大写字母`H`时候触发事件，`Key-Tab`表示按下键盘上的`Tab`按键的时候触发事件。下表列举了键盘所有特殊按键的`keysym`和`keycode`(下边按键码是对应美国标准`101`键盘的`Latin-1`字符集，键盘标准不同对应的按键码不同，但按键名是一样的)：

按键名(keysym) | 按键码(keycode) | 代表的按键
--------------|-----------------|---------------
`Alt_L`       | `64`            | 左边的`Alt`按键
`Alt_R`       | `113`           | 右边的`Alt`按键
`BackSpace`   | `22`            | `Backspace`(退格)按键
`Cancel`      | `110`           | `break`按键
`Caps_Lock`   | `66`            | `CapsLock`(大写字母锁定)按键
`Control_L`   | `37`            | 左边的`Ctrl`按键
`Control_R`   | `109`           | 右边的`Ctrl`按键
`Delete`      | `107`           | `Delete`按键
`Down`        | `104`           | `↓`按键
`End`         | `103`           | `End`按键
`Escape`      | `9`             | `Esc`按键
`Execute`     | `111`           | `SysReq`按键
`F1`          | `67`            | `F1`按键
`F2`          | `68`            | `F2`按键
`F3`          | `69`            | `F3`按键
`F4`          | `70`            | `F4`按键
`F5`          | `71`            | `F5`按键
`F6`          | `72`            | `F6`按键
`F7`          | `73`            | `F7`按键
`F8`          | `74`            | `F8`按键
`F9`          | `75`            | `F9`按键
`F10`         | `76`            | `F10`按键
`F11`         | `77`            | `F11`按键
`F12`         | `96`            | `F12`按键
`Home`        | `97`            | `Home`按键
`Insert`      | `106`           | `Insert`按键
`Left`        | `100`           | `←`按键
`Linefeed`    | `54`            | `Linefeed`(`Ctrl + J`)
`KP_0`        | `90`            | 小键盘数字`0`
`KP_1`        | `87`            | 小键盘数字`1`
`KP_2`        | `88`            | 小键盘数字`2`
`KP_3`        | `89`            | 小键盘数字`3`
`KP_4`        | `83`            | 小键盘数字`4`
`KP_5`        | `84`            | 小键盘数字`5`
`KP_6`        | `85`            | 小键盘数字`6`
`KP_7`        | `79`            | 小键盘数字`7`
`KP_8`        | `80`            | 小键盘数字`8`
`KP_9`        | `81`            | 小键盘数字`9`
`KP_Add`      | `86`            | 小键盘的`+`按键
`KP_Begin`    | `84`            | 小键盘的中间按键(`5`)
`KP_Decimal`  | `91`            | 小键盘的点按键(`.`)
`KP_Delete`   | `91`            | 小键盘的删除键
`KP_Divide`   | `112`           | 小键盘的`/`按键
`KP_Down`     | `88`            | 小键盘的`↓`按键
`KP_End`      | `87`            | 小键盘的`End`按键
`KP_Enter`    | `108`           | 小键盘的`Enter`按键
`KP_Home`     | `79`            | 小键盘的`Home`按键
`KP_Insert`   | `90`            | 小键盘的`Insert`按键
`KP_Left`     | `83`            | 小键盘的`←`按键
`KP_Multiply` | `63`            | 小键盘的`*`按键
`KP_Next`     | `89`            | 小键盘的`PageDown`按键
`KP_Prior`    | `81`            | 小键盘的`PageUp`按键
`KP_Right`    | `85`            | 小键盘的`→`按键
`KP_Subtract` | `82`            | 小键盘的`-`按键
`KP_Up`       | `80`            | 小键盘的`↑`按键
`Next`        | `105`           | `PageDown`按键
`Num_Lock`    | `77`            | `NumLock`(数字锁定)按键
`Pause`       | `110`           | `Pause`(暂停)按键
`Print`       | `111`           | `PrintScrn`(打印屏幕)按键
`Prior`       | `99`            | `PageUp`按键
`Return`      | `36`            | `Enter`(回车)按键
`Right`       | `102`           | `→`按键
`Scroll_Lock` | `78`            | `ScrollLock`按键
`Shift_L`     | `50`            | 左边的`Shift`按键
`Shift_R`     | `62`            | 右边的`Shift`按键
`Tab`         | `23`            | `Tab`按键
`Up`          | `98`            | `↑`按键