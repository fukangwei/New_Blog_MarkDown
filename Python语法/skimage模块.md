---
title: skimage模块
categories: Python语法
date: 2019-02-19 11:51:39
---
&emsp;&emsp;`scikit-image`是基于`scipy`的图像处理模块，它将图片作为`numpy`数组进行处理。<!--more-->

### skimage操作

#### 图像读取与显示

&emsp;&emsp;图像的读取与保存：

``` python
from skimage import io

img = io.imread('cat.png')
io.imshow(img)
io.show()
```

<img src="./skimage模块/1.png" width=25%>

&emsp;&emsp;以灰度的形式读取图片：

``` python
from skimage import io

img = io.imread('./cat.png', as_grey=True)
io.imshow(img)
io.show()
```

<img src="./skimage模块/2.png" width=25%>

#### 自带图片

&emsp;&emsp;自带图片如下：

图片名称     | 说明    | 图片名称             | 说明    | 图片名称  | 说明 | 图片名称                | 说明
------------|---------|---------------------|---------|-----------|-----|------------------------|----
`astronaut` | 宇航员  | `binary_blobs`       | 二元斑点 | `camera` | 相机 | `checkerboard`         | 棋盘
`chelsea`   | 猫      | `clock`             | 时钟     | `coffee` | 咖啡 | `coins`                | 硬币
`horse`     | 马      | `hubble_deep_field` | 星空     | `logo`   | 商标 | `immunohistochemistry` | 结肠图片
`moon`      | 月球表面 | `page`              | 书页内容 | `rocket` | 火箭 | `text`                 | 文字图片

``` python
from skimage import io, data

img = data.hubble_deep_field()
io.imshow(img)
io.show()
```

<img src="./skimage模块/3.jpg" width=25%>

#### 保存图片

&emsp;&emsp;对图片进行保存：

``` python
from skimage import io, data

img = data.checkerboard()
io.imshow(img)
io.imsave('checkerboard_copy.jpg', img)
```

&emsp;&emsp;在保存图片的同时，也起到了转换格式的作用。若读取的是`png`图片，当保存为`jpg`时，则图片从`png`格式转换为`jpg`格式。

#### 获取图片信息

&emsp;&emsp;代码如下：

``` python
from skimage import io, data

img = data.chelsea()
io.imshow(img)
io.show()
print(type(img))  # 类型
print(img.shape)  # 形状
print(img.shape[0])  # 图片宽度
print(img.shape[1])  # 图片高度
print(img.shape[2])  # 图片通道数
print(img.size)  # 显示总像素个数
print(img.max())  # 最大像素值
print(img.min())  # 最小像素值
print(img.mean())  # 像素平均值
```

#### 图像像素访问

&emsp;&emsp;图片读入程序后，以`numpy`数组方式存储，因此对数组元素的访问，实际上就是对图片像素点的访问。
&emsp;&emsp;对彩色图片的像素点访问方式如下：

``` python
img[i, j, c]
```

`i`表示图片的行数，`j`表示图片的列数，`c`表示图片的通道数(`RGB`通道分别对应`0`、`1`和`2`)。
&emsp;&emsp;对灰度图片的像素点访问方式如下：

``` python
gray[i, j]
```

&emsp;&emsp;例如输出`data`中宇航员图片第`20`行第`10`列的`B`通道数值：

``` python
from skimage import data

img = data.astronaut()
pixel = img[20, 10, 2]
print(pixel)  # 输出“69”
```

&emsp;&emsp;显示图片的`R`通道：

``` python
from skimage import io, data

img = data.astronaut()
R = img[:, :, 0]
io.imshow(R)
io.show()
```

<img src="./skimage模块/4.jpg" width=20%>

#### 像素修改

&emsp;&emsp;例如对宇航员图片随机添加椒盐噪声：

``` python
from skimage import io, data
import numpy as np

img = data.astronaut()
rows, cols, dims = img.shape

for i in range(5000):  # 随机生成5000个椒盐点
    x = np.random.randint(0, rows)
    y = np.random.randint(0, cols)
    img[x, y, :] = 255

io.imshow(img)
io.show()
```

<img src="./skimage模块/5.jpg" width=20%>

#### 图片裁剪

&emsp;&emsp;例如对宇航员图片进行裁剪：

``` python
from skimage import io, data

img = data.astronaut()
partial_img = img[50:150, 170:270, :]
io.imshow(partial_img)
io.show()
```

<img src="./skimage模块/6.png" width=20%>

#### 二值化

&emsp;&emsp;将宇航员图片进行二值化，像素值大于`128`的变为`1`，否则变为`0`：

``` python
from skimage import io, data, color

img = data.astronaut()
img_gray = color.rgb2gray(img)
rows, cols = img_gray.shape

for i in range(rows):
    for j in range(cols):
        if (img_gray[i, j] <= 0.5):
            img_gray[i, j] = 0
        else:
            img_gray[i, j] = 1

io.imshow(img_gray)
io.show()
```

<img src="./skimage模块/7.png" width=25%>

### 图像数据类型

&emsp;&emsp;在`skimage`中，图像数据类型以及取值范围如下：

数据类型  | 数值范围            | 数据类型   | 数值范围
---------|---------------------|-----------|---------
`uint8`  | `0`至`255`          | `float16` | 半精度浮点数：`16`位，正负号`1`位，指数`5`位，精度`10`位
`uint16` | `0`至`65535`        | `float32` | 单精度浮点数：`32`位，正负号`1`位，指数`8`位，精度`23`位
`uint32` | `0`至`2^32 - 1`     | `float64` | 双精度浮点数：`64`位，正负号`1`位，指数`11`位，精度`52`位
`float`  | `-1`至`1`或`0`至`1` | `int8`    | `-128`至`127`
`int16`  | `-32768`至`32767`   | `int32`   | `-2^31`至`2^32 - 1`

一张图片的像素值范围是`[0, 255]`，因此默认类型是`unit8`。
&emsp;&emsp;查看数据类型：

``` python
from skimage import io, data

img = data.astronaut()
print(img.dtype.name)  # 输出“uint8”
```

&emsp;&emsp;`uint8`转换为`float`：

``` python
from skimage import data, img_as_float

img = data.astronaut()
print(img.dtype.name)  # 输出“uint8”
dst = img_as_float(img)
print(dst.dtype.name)  # 输出“float64”
```

&emsp;&emsp;`float`转换为`uint8`：

``` python
from skimage import img_as_ubyte
import numpy as np

img = np.array([0, 0.5, 1], dtype=float)
print(img.dtype.name)  # 输出“float64”
dst = img_as_ubyte(img)
print(dst.dtype.name)  # 输出“uint8”
```

`float`转换为`uint8`可能会造成数据损失，因此会有警告。

### 颜色空间

&emsp;&emsp;常用的颜色空间有灰度空间、`rgb`空间、`hsv`空间和`cmyk`空间。颜色空间转换以后，图片类型都变成了`float`型。
&emsp;&emsp;例如`RGB`转为灰度图：

``` python
from skimage import io, data, color

img = data.camera()
gray = color.rgb2gray(img)
io.imshow(gray)
io.show()
```

<img src="./skimage模块/9.jpg" width=25%>

&emsp;&emsp;其它转换的用法都是一样的：

``` python
skimage.color.rgb2grey(rgb)
skimage.color.rgb2hsv(rgb)
skimage.color.rgb2lab(rgb)
skimage.color.gray2rgb(image)
skimage.color.hsv2rgb(hsv)
skimage.color.lab2rgb(lab)
```

&emsp;&emsp;上面的所有转换函数都可以用一个函数来代替：

``` python
skimage.color.convert_colorspace(arr, fromspace, tospace)
```

表示将`arr`从`fromspace`颜色空间转换到`tospace`颜色空间。
&emsp;&emsp;`RGB`转为`HSV`如下：

``` python
from skimage import io, data, color

img = data.coffee()
hsv = color.convert_colorspace(img, 'RGB', 'HSV')
io.imshow(hsv)
io.show()
```

<img src="./skimage模块/10.jpg" width=30%>

### 图像的批量处理

&emsp;&emsp;有时需要对一批图片进行处理，这时可以调用图片集合函数：

``` python
skimage.io.ImageCollection(load_pattern, load_func=None)
```

- `load_pattern`表示图片组的路径。
- `load_func`是一个回调函数，对图片进行批量处理可以通过该回调函数实现。

``` python
import skimage.io as io
from skimage import data_dir

str = data_dir + '/*.png'
coll = io.ImageCollection(str)
print(len(coll))
```

显示结果为`23`，说明`skimage`自带了`23`张`png`图片。如果想显示其中一张图片，可以使用如下代码：

``` python
io.imshow(coll[10])
io.show()
```

<img src="./skimage模块/12.jpg" width=30%>

&emsp;&emsp;如果一个文件夹里既有`jpg`格式的图片，又有`png`格式的图片，可以采用如下方式：

``` python
import skimage.io as io

str = 'd:/pic/*.jpg:d:/pic/*.png'
coll = io.ImageCollection(str)
print(len(coll))
```

&emsp;&emsp;批量对图片进行处理的方法如下：

``` python
from skimage import data_dir, io, color

def convert_gray(f):
    rgb = io.imread(f)
    return color.rgb2gray(rgb)

str = data_dir + '/*.png'
coll = io.ImageCollection(str, load_func=convert_gray)
io.imshow(coll[10])
io.show()
```

<img src="./skimage模块/13.jpg" width=30%>

### 图像的形变与缩放

#### 改变图片尺寸

&emsp;&emsp;函数原型如下：

``` python
skimage.transform.resize(image, output_shape)
```

参数`image`是需要改变尺寸的图片，`output_shape`是新的图片尺寸。

``` python
from skimage import transform, data
import matplotlib.pyplot as plt

img = data.camera()
dst = transform.resize(img, (80, 60))
plt.figure('resize')
plt.subplot(121)
plt.title('before resize')
plt.imshow(img, plt.cm.gray)
plt.subplot(122)
plt.title('before resize')
plt.imshow(dst, plt.cm.gray)
plt.show()
```

<img src="./skimage模块/14.jpg" width=30%>

#### 按比例缩放

&emsp;&emsp;函数原型如下：

``` python
skimage.transform.rescale(image, scale[, ...])
```

参数`scale`可以是单个`float`数，表示长和宽缩放的倍数；也可以是一个`float`型的`tuple`，将行列分别进行缩放：

``` python
from skimage import transform, data

img = data.camera()
print(img.shape)  # 图片原始大小
print(transform.rescale(img, 0.1).shape)  # 长和宽缩小为原来图片的十分之一
print(transform.rescale(img, [0.5, 0.25]).shape)  # 行缩小为原来图片的一半，列缩小为四分之一
print(transform.rescale(img, 2).shape)  # 长和宽放大为原来图片的2倍
```

执行结果：

``` python
(512, 512)
(51, 51)
(256, 128)
(1024, 1024)
```

#### 旋转

&emsp;&emsp;函数原型如下：

``` python
skimage.transform.rotate(image, angle, resize=False)
```

- 参数`angle`表示旋转的度数。
- 参数`resize`用于控制在旋转时是否改变大小。

``` python
from skimage import transform, data
import matplotlib.pyplot as plt

img = data.camera()
print(img.shape)  # 图片原始大小
img1 = transform.rotate(img, 60)  # 旋转90度，不改变大小
print(img1.shape)
img2 = transform.rotate(img, 30, resize=True)  # 旋转30度，同时改变大小
print(img2.shape)
plt.figure('resize')
plt.subplot(121)
plt.title('rotate 60')
plt.imshow(img1, plt.cm.gray)
plt.subplot(122)
plt.title('rotate 30')
plt.imshow(img2, plt.cm.gray)
plt.show()
```

执行结果：

``` python
(512, 512)
(512, 512)
(700, 700)
```

<img src="./skimage模块/15.jpg" width=40%>

### 图像金字塔

&emsp;&emsp;一幅图像的金字塔是一系列以金字塔形状排列的、分辨率逐步降低的图像集合。金字塔的底部是待处理图像的高分辨率表示，而顶部是低分辨率的近似。
&emsp;&emsp;图像金字塔使用`pyramid_gaussian`生成，其中`downscale`控制着金字塔的缩放比例：

``` python
skimage.transform.pyramid_gaussian(image, downscale=2)
```

&emsp;&emsp;代码实例：

``` python
import numpy as np
import matplotlib.pyplot as plt
from skimage import data, transform

image = data.astronaut()  # 载入宇航员图片
rows, cols, dim = image.shape  # 获取图片的行数、列数和通道数
# 产生高斯金字塔图像，共生成了“log(512) = 9”幅金字塔图像，加上原始图像共10幅
pyramid = tuple(transform.pyramid_gaussian(image, downscale=2))
composite_image = np.ones((rows, cols + cols // 2, 3), dtype=np.float)  # 生成背景
composite_image[:rows, :cols, :] = pyramid[0]  # 融合原始图像
i_row = 0

for p in pyramid[1:]:
    n_rows, n_cols = p.shape[:2]
    composite_image[i_row:i_row + n_rows, cols:cols + n_cols] = p  # 循环融合9幅金字塔图像
    i_row += n_rows

plt.imshow(composite_image)
plt.show()
```

<img src="./skimage模块/16.jpg" width=30%>

### 对比度与亮度调整

#### gamma调整

&emsp;&emsp;原理是`I = I * g`，对原图像的像素进行幂运算，得到新的像素值：

``` python
skimage.exposure.adjust_gamma(image, gamma=1)
```

- 如果`gamma > 1`，新图像比原图像暗。
- 如果`gamma < 1`，新图像比原图像亮。

``` python
from skimage import data, exposure, img_as_float
import matplotlib.pyplot as plt

image = img_as_float(data.moon())
gam1 = exposure.adjust_gamma(image, 2)  # 调暗
gam2 = exposure.adjust_gamma(image, 0.5)  # 调亮
plt.figure('adjust_gamma', figsize=(8, 8))
plt.subplot(131)
plt.title('origin image')
plt.imshow(image, plt.cm.gray)
plt.axis('off')
plt.subplot(132)
plt.title('gamma = 2')
plt.imshow(gam1, plt.cm.gray)
plt.axis('off')
plt.subplot(133)
plt.title('gamma = 0.5')
plt.imshow(gam2, plt.cm.gray)
plt.axis('off')
plt.show()
```

<img src="./skimage模块/17.jpg" width=40%>

#### log调整

&emsp;&emsp;这个函数刚好和`gamma`相反，原理是`I = log(I)`：

``` python
from skimage import data, exposure, img_as_float
import matplotlib.pyplot as plt

image = img_as_float(data.moon())
gam1 = exposure.adjust_log(image)  # 对数调整
plt.figure('adjust_gamma', figsize=(8, 8))
plt.subplot(121)
plt.title('origin image')
plt.imshow(image, plt.cm.gray)
plt.axis('off')
plt.subplot(122)
plt.title('log')
plt.imshow(gam1, plt.cm.gray)
plt.axis('off')
plt.show()
```

<img src="./skimage模块/18.jpg" width=40%>

#### 调整强度

&emsp;&emsp;函数原型如下：

``` python
skimage.exposure.rescale_intensity(image, in_range='image', out_range='dtype')
```

- 参数`in_range`表示输入图片的强度范围，默认为`image`，表示用图像的最大和最小像素值作为范围。
- 参数`out_range`表示输出图片的强度范围，默认为`dtype`，表示用图像的类型的最大和最小值作为范围。

默认情况下，输入图片的`[min, max]`范围被拉伸到`[dtype.min, dtype.max]`。

``` python
import numpy as np
from skimage import exposure

image = np.array([51, 102, 153], dtype=np.uint8)
mat = exposure.rescale_intensity(image)
print(mat)  # 输出“[0 127 255]”
```

像素最小值由`51`变为`0`，最大值由`153`变为`255`，整体进行了拉伸。
&emsp;&emsp;可以通过`img_as_float`将`unit8`型转换为`float`型，实际上还有更简单的方法，就是乘以`1.0`：

``` python
import numpy as np

image = np.array([51, 102, 153], dtype=np.uint8)
print(image * 1.0)  # 输出“[ 51. 102. 153.]”
```

而`float`类型的范围是`[0, 1]`，因此要对`float`进行`rescale_intensity`调整：

``` python
import numpy as np
from skimage import exposure

image = np.array([51, 102, 153], dtype=np.uint8)
tmp = image * 1.0
mat = exposure.rescale_intensity(tmp)
print(mat)  # 输出“[0.  0.5 1. ]”
```

&emsp;&emsp;如果原始像素值不想被拉伸，只是等比例缩小，就使用`in_range`：

``` python
import numpy as np
from skimage import exposure

image = np.array([51, 102, 153], dtype=np.uint8)
tmp = image * 1.0
mat = exposure.rescale_intensity(tmp, in_range=(0, 255))
print(mat)  # 输出“[0.2 0.4 0.6]”，即原像素值除以255
```

### 直方图与均衡化

#### 计算直方图

&emsp;&emsp;函数原型如下：

``` python
skimage.exposure.histogram(image, nbins=256)
```

该函数返回`(hist, bins_center)`：

- `hist`是直方图的统计量。
- `bins_center`是每个`bin`的中间值。

``` python
import numpy as np
from skimage import exposure, data

image = data.camera() * 1.0
hist1 = np.histogram(image, bins=2)  # 用numpy计算直方图
hist2 = exposure.histogram(image, nbins=2)  # 用skimage计算直方图
print(hist1)
print(hist2)
```

执行结果：

``` python
(array([107432, 154712], dtype=int64), array([  0. , 127.5, 255. ]))
(array([107432, 154712], dtype=int64), array([ 63.75, 191.25]))
```

每个`bin`的统计量是一样的，但`numpy`返回的是每个`bin`的两端的范围值，而`skimage`返回的是每个`bin`的中间值。

#### 绘制直方图

&emsp;&emsp;绘图可以调用`matplotlib.pyplot`库来进行，其中的`hist`函数可以直接绘制直方图：

``` python
n, bins, patches = plt.hist(arr, bins=10, normed=0, facecolor='black', edgecolor='black', alpha=1, histtype='bar')
```

- `arr`：显示直方图的一维数组。
- `bins`：直方图的柱数。
- `normed`：是否将得到的直方图向量归一化。
- `facecolor`：直方图颜色。
- `edgecolor`：直方图边框颜色。
- `alpha`：透明度。
- `histtype`：直方图类型，例如`bar`、`barstacked`、`step`和`stepfilled`。

返回值如下：

- `n`：直方图向量，是否归一化由参数`normed`设定。
- `bins`：各个`bin`的区间范围。
- `patches`：每个`bin`里面包含的数据，是一个`list`。

``` python
from skimage import data
import matplotlib.pyplot as plt

img = data.camera()
plt.figure("hist")
arr = img.flatten()  # 将多维数组序列化成一维数组
n, bins, patches = plt.hist(arr, bins=256, normed=1, edgecolor='None', facecolor='red')
plt.show()
```

<img src="./skimage模块/19.png" width=30%>

#### 三通道直方图

&emsp;&emsp;一般来说，直方图都是针对灰度图的。如果要画`RGB`图像的三通道直方图，实际上就是三个通道的直方图的叠加：

``` python
from skimage import data
import matplotlib.pyplot as plt

img = data.coffee()
ar = img[:, :, 0].flatten()
plt.hist(ar, bins=256, normed=1, facecolor='r', edgecolor='r', hold=1)  # 参数hold表示叠加
ag = img[:, :, 1].flatten()
plt.hist(ag, bins=256, normed=1, facecolor='g', edgecolor='g', hold=1)
ab = img[:, :, 2].flatten()
plt.hist(ab, bins=256, normed=1, facecolor='b', edgecolor='b')
plt.show()
```

<img src="./skimage模块/20.png" width=30%>

#### 直方图均衡化

&emsp;&emsp;如果一幅图像的像素点有很多的灰度级，而且分布均匀，那么这样的图像往往有高对比度和多变的灰度色调。
&emsp;&emsp;直方图均衡化就是一种能仅靠输入图像的直方图信息，就能自动达到这种效果的变换函数。

``` python
from skimage import data, exposure
import matplotlib.pyplot as plt

img = data.moon()
plt.figure("hist", figsize=(8, 8))
arr = img.flatten()
plt.subplot(221)
plt.imshow(img, plt.cm.gray)  # 原始图像
plt.subplot(222)
plt.hist(arr, bins=256, normed=1, edgecolor='None', facecolor='red')  # 原始图像直方图
img1 = exposure.equalize_hist(img)
arr1 = img1.flatten()
plt.subplot(223)
plt.imshow(img1, plt.cm.gray)  # 均衡化图像
plt.subplot(224)
plt.hist(arr1, bins=256, normed=1, edgecolor='None', facecolor='red')  # 均衡化直方图
plt.show()
```

<img src="./skimage模块/21.jpg" width=30%>

### HOG特征

&emsp;&emsp;`hog`函数返回一维`hog`特征和`hog`特征的可视化图像：

``` python
skimage.feature.hog(image, orientations=9, pixels_per_cell=(8, 8), cells_per_block=(3, 3), visualise=False)
```

- `image`：传入要进行`hog`特征计算的灰度图。
- `orientations`：设置方向梯度直方图的箱子个数。
- `pixels_per_cell`：设置每个单元的像素。
- `cells_per_block`：设置每个区块的单元数。
- `visualise`：设置是否返回可视化的`hog`特征。

``` python
import matplotlib.pyplot as plt
from skimage import data, color, exposure
from skimage.feature import hog

image = color.rgb2gray(data.astronaut())
fd, hog_image = hog(image, orientations=8, pixels_per_cell=(16, 16), cells_per_block=(1, 1), visualize=True)
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(8, 4), sharex=True, sharey=True)
ax1.axis('off')
ax1.imshow(image, cmap=plt.cm.gray)
ax1.set_title('Input image')

# Rescale histogram for better display
hog_image_rescaled = exposure.rescale_intensity(hog_image, in_range=(0, 0.02))
ax2.axis('off')
ax2.imshow(hog_image_rescaled, cmap=plt.cm.gray)
ax2.set_title('Histogram of Oriented Gradients')
plt.show()
```

<img src="./skimage模块/22.jpg" width=40%>