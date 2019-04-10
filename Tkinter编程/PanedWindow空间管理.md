---
title: PanedWindow空间管理
date: 2019-04-10 21:52:42
tags:
---
    PanedWindow组件(Tk8.4新增)是一个空间管理组件。跟Frame组件类似，都是为组件提供一个框架，不过PanedWindow允许让用户调整应用程序的空间划分。
    当你需要提供一个可供用户调整的多空间框架的时候，可以使用PanedWindow组件。PanedWindow组件会为每一个子组件生成一个独立地窗格，用户可以自由调整窗格的大小。

用法
    创建一个2窗格的PanedWindow组件非常简单：
from tkinter import *
​
m = PanedWindow(orient=VERTICAL)
m.pack(fill=BOTH, expand=1)
​
top = Label(m, text="top pane")
m.add(top)
​
bottom = Label(m, text="bottom pane")
m.add(bottom)
​
mainloop()

创建一个3窗格的PanedWindow组件则需要一点小技巧：
from tkinter import *
​
m1 = PanedWindow()
m1.pack(fill=BOTH, expand=1)
​
left = Label(m1, text="left pane")
m1.add(left)
​
m2 = PanedWindow(orient=VERTICAL)
m1.add(m2)
​
top = Label(m2, text="top pane")
m2.add(top)
​
bottom = Label(m2, text="bottom pane")
m2.add(bottom)
​
mainloop()l

这里不同窗格事实上是有一条“分割线”(sash)隔开，虽然看不到，但是却可以感受到它的存在。也可以把“分割线”给显式地显示出来，并且可以为它附上一个“手柄”(handle)：
from tkinter import *
​
m1 = PanedWindow(showhandle=True, sashrelief=SUNKEN)
m1.pack(fill=BOTH, expand=1)
​
left = Label(m1, text="left pane")
m1.add(left)
​
m2 = PanedWindow(orient=VERTICAL, showhandle=True, sashrelief=SUNKEN)
m1.add(m2)
​
top = Label(m2, text="top pane")
m2.add(top)
​
bottom = Label(m2, text="bottom pane")
m2.add(bottom)
​
mainloop()


参数
    PanedWindow(master=None, **options) (class)：master是父组件，options是组件选项，下方表格列举了各个选项的具体含义和用法：
选项            含义
--------------------
background      设置背景颜色
bg              跟background一样
borderwidth     设置边框宽度
bd              跟borderwidth一样
cursor          1、指定当鼠标在PanedWindow上飘过的时候的鼠标样式
                2、默认值由系统指定
handlepad       1、调节“手柄”的位置
                2、例如“orient=VERTICAL”，则handlepad选项表示“分割线”上的手柄与左端的距离
                3、默认值是 8 像素
handlesize      1、设置“手柄”的尺寸(由于“手柄”必须是一个正方形，所以是设置正方形的边长)
                2、默认值是8像素
height          1、设置PanedWindow的高度
                2、如果忽略该选项，则高度由子组件的尺寸决定
opaqueresize    1、该选项定义了用户调整窗格尺寸的操作
                2、如果该选项的值为True(默认)，窗格的尺寸随用户鼠标的拖拽而改变
                3、如果该选项的值为False，窗格的尺寸在用户释放鼠标的时候才更新到新的位置
orient          1、指定窗格的分布方式
                2、可以是HORIZONTAL(横向分布)和VERTICAL(纵向分布)
relief          1、指定边框样式
                2、默认值是FLAT
                3、另外你还可以设置SUNKEN、RAISED、GROOVE或RIDGE
sashpad         设置每一条分割线到窗格间的间距
sashrelief      1、设置分割线的样式
                2、默认值是FLAT
                3、另外你还可以设置SUNKEN、RAISED、GROOVE或RIDGE
sashwidth       设置分割线的宽度
showhandle      1、设置是否显示调节窗格的手柄
                2、默认值为False
width           1、设置PanedWindow的宽度
                2、如果忽略该选项，则高度由子组件的尺寸决定

方法
add(child, **options)：添加一个新的子组件到窗格中，下方表格列举了各个options选项具体含义：
选项       含义
---------------
after      添加新的子组件到指定子组件后边
before     添加新的子组件到指定子组件前边
height     指定子组件的高度
minsize    1、该选项控制窗格不得低于的值
           2、如果“orient = HORIZONTAL”，表示窗格的宽度不得低于该选项的值
           3、如果“orient = VERTICAL”，表示窗格的高度不得低于该选项的值
padx       指定子组件的水平间距
pady       指定子组件的垂直间距
sticky     1、当窗格的尺寸大于子组件时，该选项指定子组件位于窗格的位置
           2、可选的值有E、S、W、N(分别代表东南西北四个方位)以及它们的组合值
           3、例如NE表示子组件显示在右上角的位置
width      指定子组件的宽度
forget(child)：删除一个子组件。
identify(x, y)：给定一个坐标(x, y)，返回该坐标所在的元素名称。如果该坐标位于子组件上，返回空字符串；如果该坐标位于分割线上，返回一个二元组(n, 'sash')，n为0表示第一个分割线；如果该坐标位于手柄上，返回一个二元组(n, 'handle')，n为0表示第一个手柄。
panecget(child, option)：获得子组件指定选项的值。
paneconfig(child, **options)：设置子组件的各种选项，下方表格列举了各个options选项具体含义：
选项       含义
---------------
after      添加新的子组件到指定子组件后边
before     添加新的子组件到指定子组件前边
height     指定子组件的高度
minsize    1、该选项控制窗格不得低于的值
           2、如果“orient = HORIZONTAL”，表示窗格的宽度不得低于该选项的值
           3、如果“orient = VERTICAL”，表示窗格的高度不得低于该选项的值
padx       指定子组件的水平间距
pady       指定子组件的垂直间距
sticky     1、当窗格的尺寸大于子组件时，该选项指定子组件位于窗格的位置
           2、可选的值有E、S、W、N(分别代表东南西北四个方位)以及它们的组合值
           3、例如NE表示子组件显示在右上角的位置
width      指定子组件的宽度
paneconfigure(child, **options)：跟paneconfig一样。
panes：将子组件以列表的形式返回。
remove(child)：跟forget一样。
sash_coord(index)：返回一个二元组表示指定分割线的起点坐标。
sash_dragto(index, x, y)：实现将指定的分割线拖拽到一个新的位置，与sash_mark一起实现。
sash_mark(index, x, y)：注册当前的鼠标位置。
sash_place(index, x, y)：将指定分割线移动到一个新的位置。
