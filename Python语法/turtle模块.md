---
title: turtle模块
categories: Python语法
date: 2019-03-02 15:10:42
---
&emsp;&emsp;`Turtle`库是`Python`中一个很流行的绘制图像的函数库。<!--more-->

### 画布

#### screensize

&emsp;&emsp;画布就是`turtle`绘图区域，可以设置它的大小：

``` python
turtle.screensize(canvwidth=None, canvheight=None, bg=None)
```

- `canvwidth`是画布的宽，单位是像素。
- `canvheight`是画布的高，单位是像素。
- `bg`是画布的背景颜色。

``` python
turtle.screensize(800, 600, "green")
turtle.screensize()  # 返回默认大小(400, 300)
```

#### setup

&emsp;&emsp;`setup`函数用于设置画布的初始位置：

``` python
turtle.setup(width=0.5, height=0.75, startx=None, starty=None)
```

- `width`和`height`：

1. 为整数时，表示像素。
2. 为小数时，表示占据电脑屏幕的比例。

- `startx`和`starty`：表示矩形窗口左上角顶点的位置；如果为空，则窗口位于屏幕中心。

``` python
turtle.setup(width=0.6, height=0.6)
turtle.setup(width=800, height=800, startx=100, starty=100)
```

### 画笔

#### 画笔状态

&emsp;&emsp;在画布上，默认有一个坐标原点为画布中心的坐标轴，坐标原点上有一只面朝`x`轴正方向小乌龟。

#### 画笔属性

&emsp;&emsp;画笔属性包括颜色、画线的宽度等：

- `turtle.pensize()`：设置画笔的宽度。
- `turtle.pencolor()`：设置画笔颜色。
- `turtle.speed()`：设置画笔移动速度。

#### 绘图命令

&emsp;&emsp;画笔运动命令如下：

命令                       | 说明                            | 命令                        | 说明
---------------------------|--------------------------------|-----------------------------|-----
`turtle.forward(distance)` | 向当前画笔方向移动`distance`像素 | `turtle.backward(distance)` | 向当前画笔相反方向移动`distance`像素
`turtle.right(degree)`     | 顺时针移动`degree`度            | `turtle.left(degree)`       | 逆时针移动`degree`度
`turtle.goto(x,y)`         | 将画笔移动到坐标为`(x, y)`的位置 | `turtle.penup()`            | 提起笔移动，用于另起一个地方绘制

&emsp;&emsp;画笔控制命令如下：

命令                            | 说明              | 命令                           | 说明
--------------------------------|------------------|--------------------------------|-----
`turtle.fillcolor(colorstring)` | 绘制图形的填充颜色 | `turtle.color(color1, color2)` | 同时设置`pencolor = color1`和`fillcolor = color2`
`turtle.begin_fill()`           | 准备开始填充图形   | `turtle.end_fill()`            | 填充完成

&emsp;&emsp;全局控制命令如下：

- `turtle.clear()`：清空窗口，但是`turtle`的位置和状态不会改变。
- `turtle.reset()`：清空窗口，重置`turtle`状态为起始状态。
- `turtle.undo()`：撤销上一个`turtle`动作。
- `turtle.write(s[, font=("font_name", font_size, "font_type")])`：用于显示文本。

&emsp;&emsp;其他命令如下：

- `turtle.mainloop()`或`turtle.done()`：启动事件循环，必须是图形程序中的最后一个语句。
- `turtle.delay(delay=None)`：设置以毫秒为单位的绘图延迟。

### 具体应用

#### 太阳花

&emsp;&emsp;太阳花如下：

``` python
import turtle

turtle.color("red", "yellow")
turtle.begin_fill()

for _ in range(50):
    turtle.forward(200)
    turtle.left(170)

turtle.end_fill()
turtle.mainloop()
```

<img src="./turtle模块/1.png" width=15%>

#### 五角星

&emsp;&emsp;五角星如下：

``` python
import turtle
import time

turtle.pensize(5)
turtle.pencolor("yellow")
turtle.fillcolor("red")
turtle.begin_fill()

for _ in range(5):
    turtle.forward(200)
    turtle.right(144)

turtle.end_fill()
time.sleep(2)

turtle.penup()
turtle.goto(-150, -120)
turtle.color("violet")
turtle.write("Done", font=('Arial', 40, 'normal'))
turtle.mainloop()
```

<img src="./turtle模块/2.png" width=25%>

#### 递归树

&emsp;&emsp;递归画出树：

``` python
import turtle

def branch(length, level):
    if level <= 0:
        return

    turtle.forward(length)
    turtle.left(45)
    branch(0.6 * length, level - 1)
    turtle.right(90)
    branch(0.6 * length, level - 1)
    turtle.left(45)
    turtle.backward(length)
    return

if __name__ == "__main__":
    turtle.left(90)
    branch(100, 4)
    turtle.mainloop()
```

<img src="./turtle模块/3.png" width=15%>