---
title: Pillow模块
categories: Python语法
date: 2019-03-02 19:06:03
---
### 打开图片

&emsp;&emsp;使用`Image`中的`open`函数打开一张图片：<!--more-->

``` python
from PIL import Image

im = Image.open('001.jpg')
print(im)
print(im.format, im.size, im.mode)
```

执行结果：

``` python
<PIL.JpegImagePlugin.JpegImageFile image mode=RGB size=2560x1600 at 0x1C75B0EA588>
JPEG (2560, 1600) RGB
```

- `format`定义了图像的格式。
- `size`是一个`tuple`，表示图像的宽和高，单位为像素。
- `mode`为图像的模式：

模式    | 说明                           | 模式   | 说明
--------|-------------------------------|--------|-----
`L`     | `8`位像素，黑白                | `F`    | `32`位浮点型像素
`RGB`   | `3 * 8`位像素，真彩            | `P`    | `8`位像素，使用调色板映射到任何其他模式
`RGBA`  | `4 * 8`位像素，真彩加上透明通道 | `CMYK` | `4 * 8`位像素，颜色隔离
`YCbCr` | `3 * 8`位像素，彩色视频格式     | `I`    | `32`位整型像素

### 显示图片

&emsp;&emsp;显示图片的方法如下：

``` python
im.show()
```

### 保存图片

&emsp;&emsp;`Image`模块中的`save`函数可以保存图片：

``` python
im = Image.open('001.jpg')
im.save('f.png')
```

### 复制图片区域

&emsp;&emsp;代码如下：

``` python
from PIL import Image

im = Image.open('001.jpg')
box = (100, 100, 500, 500)  # 设置要拷贝的区域，即(左, 上, 右, 下)
region = im.crop(box)
region = region.transpose(Image.ROTATE_90)  # 先把region中的Image反转90度，然后再放回到region中
im.paste(region, box)  # 粘贴box大小的region到原先的图片对象中
im.save('1.jpg')
```

<img src="./Pillow模块/1.jpg" width=30%>

&emsp;&emsp;如果只是复制一个图像，则使用如下方法：

``` python
box = im.copy()  # 直接复制图像
```

### 分离和合并通道

&emsp;&emsp;每一个`RGB`图像都是由`3`个通道的灰度图叠加的，`Pillow`提供了将这三个通道分离的方法：

``` python
r, g, b = im.split()  # 分割成三个通道
im = Image.merge("RGB", (b, g, r))  # 将b和r两个通道进行翻转
im.save('rgb.jpg')
```

<img src="./Pillow模块/2.jpg" width=20%>

### 几何转变

&emsp;&emsp;简单几何变换:

``` python
out = im.resize((128, 128))
out = im.rotate(45)
```

&emsp;&emsp;置换图像：

``` python
out = im.transpose(Image.FLIP_LEFT_RIGHT)  # 左右翻转
out = im.transpose(Image.FLIP_TOP_BOTTOM)  # 上下反向
out = im.transpose(Image.ROTATE_90)  # 上下反向
```

&emsp;&emsp;模式转换：

``` python
am = im.convert('L')  # 转为灰度图像
am.show()
```

### 绘图

#### line

&emsp;&emsp;函数`line(xy, options)`用于绘制直线：

- `xy`表示坐标列表。
- `options`可用的选项如下：

1. `fill = (R, G, B)`：用于指定线条的颜色，其中`R`、`G`、`B`都是`0`至`255`的整数。
2. `width = integer`：用于指定线条的宽度，单位是像素。

``` python
from PIL import Image, ImageDraw

sourceFileName = "tu.jpg"
avatar = Image.open(sourceFileName)
drawAvatar = ImageDraw.Draw(avatar)
xSize, ySize = avatar.size
drawAvatar.line([0, 0.33 * ySize, xSize, 0.33 * ySize], fill=(255, 100, 0), width=3)
drawAvatar.line([0, 0.67 * ySize, xSize, 0.67 * ySize], fill=(255, 0, 0), width=3)
del drawAvatar
avatar.show()
```

<img src="./Pillow模块/3.jpg" width=40%>

#### Polygon

&emsp;&emsp;函数`polygon`用于绘制多边形：

``` python
PIL.ImageDraw.Draw.polygon(xy, fill=None, outline=None)
```

- `xy`：由多边形的顶点坐标组成的序列。
- `outline`：边界的颜色。
- `fill`：多边形填充的颜色。

``` python
from PIL import Image, ImageDraw

im01 = Image.open("test.png")
draw = ImageDraw.Draw(im01)
draw.polygon([(0, 0), (100, 150), (210, 350)], fill=(255, 0, 0))
draw.polygon([300, 300, 100, 400, 400, 400], fill=(0, 255, 0))
im01.show()
```

<img src="./Pillow模块/4.jpg" width=40%>

### 文字

&emsp;&emsp;函数`text(position, string, options)`用于在`Image`实例上写字：

- `position`指定了文本左上角的顶点。
- `options`有如下选项：

1. `font = ImageFont instance`：指定字体，接受一个`ImageFont`的实例。
2. `fill = (R, G, B)`：指定线条的颜色，其中`R`、`G`、`B`都是`0`至`255`的整数。

``` python
from PIL import Image, ImageDraw

sourceFileName = "tu.jpg"
avatar = Image.open(sourceFileName)
drawAvatar = ImageDraw.Draw(avatar)
xSize, ySize = avatar.size
drawAvatar.text([0.9 * xSize, 0.1 * ySize - drawAvatar.textsize("3")[1]], "3", fill=(128, 0, 128))
del drawAvatar
avatar.show()
```

<img src="./Pillow模块/5.jpg" width=15%>

### 新建图像

&emsp;&emsp;`Pillow`可以使用`new`函数新建空白图像：

``` python
PIL.Image.new(mode, size, color=0)
```

- `mode`指定颜色空间模式，通常使用`RGB`。
- `size`指定图像的分辨率，即`(宽, 高)`。
- `color`指定颜色。