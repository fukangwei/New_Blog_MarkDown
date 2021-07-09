---
title: visdom模块
categories: Python语法
date: 2019-04-13 15:26:26
---
&emsp;&emsp;`visdom`可以进行多种数据的可视化，包括数值、图像甚至是视频。<!--more-->
&emsp;&emsp;通过`python -m visdom.server`命令启动`visdom`服务，默认绑定`8097`端口，需要在浏览器上输入`http://localhost:8097`。
&emsp;&emsp;`visdom`同时支持`PyTorch`的`tensor`和`Numpy`的`ndarray`两种数据结构，但不支持`Python`的`int`、`float`等类型，因此需要将数据转成`ndarray`或`tensor`。

### viz.maplotlib

&emsp;&emsp;代码如下：

``` python
from visdom import Visdom
import matplotlib.pyplot as plt

viz = Visdom()
assert viz.check_connection()

try:
    plt.plot([1, 23, 2, 4])
    plt.ylabel('some numbers')
    viz.matplot(plt)
except BaseException as err:
    print('Skipped matplotlib example')
    print('Error message: ', err)
```

<img src="./visdom模块/1.png" height="238" width="329">

### vis.video

&emsp;&emsp;代码如下：

``` python
from visdom import Visdom
import urllib.request
import os

viz = Visdom()
assert viz.check_connection()

try:
    video_url = 'http://media.w3.org/2010/05/sintel/trailer.ogv'
    videofile = 'trailer.ogv'
    urllib.request.urlretrieve(video_url, videofile)

    if os.path.isfile(videofile):
        viz.video(videofile=videofile)
except ImportError:
    print('Skipped video example')
```

<img src="./visdom模块/2.png" height="231" width="262">

### vis.image

&emsp;&emsp;代码如下：

``` python
from visdom import Visdom
import numpy as np

viz = Visdom()
assert viz.check_connection()

viz.image(  # 单张图像
    np.random.rand(3, 512, 256),
    opts=dict(title='Random!', caption='How random.'))
viz.images(  # 多张图像
    np.random.randn(20, 3, 64, 64),
    opts=dict(title='Random images', caption='How random.'))
```

<img src="./visdom模块/3.png" height="271" width="404">

### vis.scatter

&emsp;&emsp;代码如下：

``` python
from visdom import Visdom
import numpy as np
import time

viz = Visdom()
assert viz.check_connection()

Y = np.random.rand(100)
old_scatter = viz.scatter(  # 画出随机的散点图
    X=np.random.rand(100, 2),
    Y=(Y[Y > 0] + 1.5).astype(int),
    opts=dict(legend=['Didnt', 'Update'],
            xtickmin=-50, xtickmax=50, xtickstep=0.5,
            ytickmin=-50, ytickmax=50, ytickstep=0.5,
            markersymbol='cross-thin-open',),
)

time.sleep(5)

viz.update_window_opts(  # 对窗口进行更新，包括标注、坐标和样式等
    win=old_scatter,
    opts=dict(legend=['Apples', 'Pears'],
            xtickmin=0, xtickmax=1, xtickstep=0.5, ytickmin=0,
            ytickmax=1, ytickstep=0.5, markersymbol='cross-thin-open',),
)
```

<img src="./visdom模块/4.png" height="294" width="392">

&emsp;&emsp;通过`update='new'`添加新散点：

``` python
from visdom import Visdom
import numpy as np
import time

viz = Visdom()
assert viz.check_connection()

win = viz.scatter(
    X=np.random.rand(255, 2),
    opts=dict(markersize=10, markercolor=np.random.randint(0, 255, (255, 3,)),),)
assert viz.win_exists(win), 'Created window marked as not existing'  # 判断窗口是否存在
time.sleep(2)
# 向散点图中加入新的描述
viz.scatter(X=np.random.rand(255), Y=np.random.rand(255), win=win, name='new_trace', update='new')
```

<img src="./visdom模块/5.png">

&emsp;&emsp;为`2D`散点图分配不同颜色：

``` python
from visdom import Visdom
import numpy as np

viz = Visdom()
assert viz.check_connection()

viz.scatter(
    X=np.random.rand(255, 2),
    Y=(np.random.rand(255) + 1.5).astype(int),  # 随机指定1或者2
    opts=dict(
        markersize=10,
        markercolor=np.random.randint(0, 255, (2, 3,)),  # 分配两种颜色
    ),
)
```

<img src="./visdom模块/6.png" height="272" width="368">

### vis.bar

&emsp;&emsp;代码如下：

``` python
from visdom import Visdom
import numpy as np

viz = Visdom()
assert viz.check_connection()

viz.bar(X=np.random.rand(20))

viz.bar(
    X=np.abs(np.random.rand(5, 3)),
    opts=dict(stacked=True, legend=['Facebook', 'Google', 'Twitter'], \
              rownames=['2012', '2013', '2014', '2015', '2016'])
)

viz.bar(X=np.random.rand(20, 3), opts=dict(stacked=False, \
        legend=['The Netherlands', 'France', 'United States']))
```

<img src="./visdom模块/7.png" height="289" width="944">

### vis.heat/contour/surface

&emsp;&emsp;代码如下：

``` python
from visdom import Visdom
import numpy as np

viz = Visdom()
assert viz.check_connection()

viz.heatmap(
    X=np.outer(np.arange(1, 6), np.arange(1, 11)),
    opts=dict(
        columnnames=['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'],
        rownames=['y1', 'y2', 'y3', 'y4', 'y5'], colormap='Electric',)
)

# contour
x = np.tile(np.arange(1, 101), (100, 1))
y = x.transpose()
X = np.exp((((x - 50) ** 2) + ((y - 50) ** 2)) / -(20.0 ** 2))
viz.contour(X=X, opts=dict(colormap='Viridis'))
viz.surf(X=X, opts=dict(colormap='Hot'))  # surface
```

<img src="./visdom模块/8.png" height="260" width="928">

### viz.boxplot/stem/quiver

&emsp;&emsp;代码如下：

``` python
from visdom import Visdom
import numpy as np
import math

viz = Visdom()
assert viz.check_connection()

# boxplot
X = np.random.rand(100, 2)
X[:, 1] += 2
viz.boxplot(X=X, opts=dict(legend=['Men', 'Women']))

# stemplot
Y = np.linspace(0, 2 * math.pi, 70)
X = np.column_stack((np.sin(Y), np.cos(Y)))
viz.stem(X=X, Y=Y, opts=dict(legend=['Sine', 'Cosine']))

# quiver plot
X = np.arange(0, 2.1, .2)
Y = np.arange(0, 2.1, .2)
X = np.broadcast_to(np.expand_dims(X, axis=1), (len(X), len(X)))
Y = np.broadcast_to(np.expand_dims(Y, axis=0), (len(Y), len(Y)))
U = np.multiply(np.cos(X), Y)
V = np.multiply(np.sin(X), Y)
viz.quiver(X=U, Y=V, opts=dict(normalize=0.9),)
```

<img src="./visdom模块/9.png" height="251" width="902">

### viz.text/pie/mesh

&emsp;&emsp;代码如下：

``` python
from visdom import Visdom
import numpy as np

viz = Visdom()
assert viz.check_connection()

# text window with Callbacks
txt = 'This is a write demo notepad. Type below. Delete clears text:<br>'
callback_text_window = viz.text(txt)

# pie chart
X = np.asarray([19, 26, 55])
viz.pie(X=X, opts=dict(legend=['Residential', 'Non-Residential', 'Utility']))

# mesh plot
x = [0, 0, 1, 1, 0, 0, 1, 1]
y = [0, 1, 1, 0, 0, 1, 1, 0]
z = [0, 0, 0, 0, 1, 1, 1, 1]
X = np.c_[x, y, z]
i = [7, 0, 0, 0, 4, 4, 6, 6, 4, 0, 3, 2]
j = [3, 4, 1, 2, 5, 6, 5, 2, 0, 1, 6, 3]
k = [0, 7, 2, 3, 6, 7, 1, 1, 5, 5, 7, 6]
Y = np.c_[i, j, k]
viz.mesh(X=X, Y=Y, opts=dict(opacity=0.5))
```

<img src="./visdom模块/10.png" height="242" width="762">