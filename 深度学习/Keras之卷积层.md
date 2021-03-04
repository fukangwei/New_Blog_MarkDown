---
title: Keras之卷积层
categories: 深度学习
date: 2019-01-16 08:06:29
---
### Conv1D

&emsp;&emsp;该函数是`1D`卷积层(例如`时序卷积`)：<!--more-->

``` python
keras.layers.Conv1D(
    filters, kernel_size, strides=1, padding='valid', dilation_rate=1,
    activation=None, use_bias=True, kernel_initializer='glorot_uniform',
    bias_initializer='zeros', kernel_regularizer=None, bias_regularizer=None,
    activity_regularizer=None, kernel_constraint=None, bias_constraint=None)
```

一维卷积层(即`时域卷积`)用以在一维输入信号上进行邻域滤波。当使用该层作为首层时，需要提供关键字参数`input_shape`。例如(`10, 128`)代表一个长为`10`的序列，序列中每个信号为`128`向量，而(`None, 128`)代表变长的`128`维向量序列。该层生成将输入信号与卷积核按照单一的空域(或时域)方向进行卷积。如果`use_bias = True`，则还会加上一个偏置项，若`activation`不为`None`，则输出为经过激活函数的输出。

- `filters`：卷积核的数目(即输出的维度)。
- `kernel_size`：一个整数，或者单个整数表示的元组或列表，指明卷积核的空域或时域窗长度。
- `strides`：一个整数，或者单个整数表示的元组或列表，指明卷积的步长。任何不为`1`的`strides`均与任何不为`1`的`dilation_rate`均不兼容。
- `padding`：`valid`、`causal`或`same`之一(大小写敏感)。`valid`表示`不填充`；`same`表示填充输入以使输出具有与原始输入相同的长度；`causal`表示因果(膨胀)卷积，例如`output[t]`不依赖于`input[t + 1:]`，当对不能违反时间顺序的时序信号建模时有用。
- `dilation_rate`：一个整数，或者单个整数表示的元组或列表，指定用于膨胀卷积的膨胀率。任何不为`1`的`dilation_rate`均与任何不为`1`的`strides`均不兼容。
- `activation`：要使用的激活函数。如果你不指定，则不使用激活函数(即线性激活`a(x) = x`)。
- `use_bias`：布尔值，该层是否使用偏置向量。
- `kernel_initializer`：`kernel`权值矩阵的初始化器。
- `bias_initializer`：偏置向量的初始化器。
- `kernel_regularizer`：运用到`kernel`权值矩阵的正则化函数。
- `bias_regularizer`：运用到偏置向量的正则化函数。
- `activity_regularizer`：运用到层的输出的正则化函数。
- `kernel_constraint`：运用到`kernel`权值矩阵的约束函数。
- `bias_constraint`：运用到偏置向量的约束函数。

&emsp;&emsp;输入尺寸：`3D`张量，尺寸为(`batch_size, steps, input_dim`)。
&emsp;&emsp;输出尺寸：`3D`张量，尺寸为(`batch_size, new_steps, filters`)。由于填充或窗口按步长滑动，`steps`值可能已更改。

### Conv2D

&emsp;&emsp;该函数是`2D`卷积层(例如对图像的`空间卷积`)：

``` python
keras.layers.Conv2D(
    filters, kernel_size, strides=(1, 1), padding='valid', data_format=None,
    dilation_rate=(1, 1), activation=None, use_bias=True,
    kernel_initializer='glorot_uniform', bias_initializer='zeros',
    kernel_regularizer=None, bias_regularizer=None, activity_regularizer=None,
    kernel_constraint=None, bias_constraint=None)
```

二维卷积层即是对图像的空域卷积，该层对二维输入进行滑动窗卷积。当使用该层作为第一层时，应提供`input_shape`参数，例如`input_shape = (128, 128, 3)`代表`128 * 128`的彩色`RGB`图像(`data_format = 'channels_last'`)。

- `filters`：卷积核的数目(即输出的维度)。
- `kernel_size`：一个整数，或者`2`个整数表示的元组或列表，指明`2D`卷积窗口的宽度和高度。如为单个整数，则表示在各个空间维度的相同长度。
- `strides`：一个整数，或者`2`个整数表示的元组或列表，指明卷积沿宽度和高度方向的步长。如为单个整数，则表示在各个空间维度的相同步长。任何不为`1`的`strides`均与任何不为`1`的`dilation_rate`均不兼容。
- `padding`：`valid`或`same`。`valid`代表只进行有效的卷积，即对边界数据不处理；`same`代表保留边界处的卷积结果，通常会导致输出`shape`与输入`shape`相同。
- `data_format`：字符串，`channels_last`(默认)或`channels_first`之一，代表图像的通道维的位置。`channels_last`对应输入尺寸为(`batch, height, width, channels`)，`channels_first`对应输入尺寸为(`batch, channels, height, width`)。
- `dilation_rate`：一个整数或`2`个整数的元组或列表，指定膨胀卷积的膨胀率。任何不为`1`的`dilation_rate`均与任何不为`1`的`strides`均不兼容。
- `activation`：要使用的激活函数。如果你不指定，则不使用激活函数(即线性激活`a(x) = x`)。

&emsp;&emsp;输入尺寸(注意这里的输入`shape`指的是函数内部实现的输入`shape`，而非函数接口应指定的`input_shape`)：

- 如果`data_format = 'channels_first'`，输入`4D`张量，尺寸为(`samples, channels, rows, cols`)。
- 如果`data_format = 'channels_last'`，输入`4D`张量，尺寸为(`samples, rows, cols, channels`)。

&emsp;&emsp;输出尺寸(输出的行列数可能会因为填充方法而改变)：

- 如果`data_format = 'channels_first'`，输出`4D`张量，尺寸为(`samples, filters, new_rows, new_cols`)。
- 如果`data_format = 'channels_last'`，输出`4D`张量，尺寸为(`samples, new_rows, new_cols, filters`)。

### SeparableConv2D

&emsp;&emsp;该函数在深度方向的可分离`2D`卷积：

``` python
keras.layers.SeparableConv2D(
    filters, kernel_size, strides=(1, 1), padding='valid', data_format=None,
    depth_multiplier=1, activation=None, use_bias=True,
    depthwise_initializer='glorot_uniform', pointwise_initializer='glorot_uniform',
    bias_initializer='zeros', depthwise_regularizer=None, pointwise_regularizer=None,
    bias_regularizer=None, activity_regularizer=None, depthwise_constraint=None,
    pointwise_constraint=None, bias_constraint=None)
```

可分离卷积首先按深度方向进行卷积(对每个输入通道分别卷积)，然后逐点进行卷积，将上一步的卷积结果混合到输出通道中。参数`depth_multiplier`控制了在`depthwise`卷积(第一步)的过程中，每个输入通道信号产生多少个输出通道。

- `depth_multiplier`：在按深度卷积的步骤中，每个输入通道使用多少个输出通道。
- `depthwise_initializer`：运用到深度方向的核矩阵的初始化器。
- `pointwise_initializer`：运用到逐点核矩阵的初始化器。
- `depthwise_regularizer`：运用到深度方向的核矩阵的正则化函数。
- `pointwise_regularizer`：运用到逐点核矩阵的正则化函数。
- `depthwise_constraint`：运用到深度方向的核矩阵的约束函数。
- `pointwise_constraint`：运用到逐点核矩阵的约束函数。

&emsp;&emsp;输入尺寸(注意这里的输入`shape`指的是函数内部实现的输入`shape`，而非函数接口应指定的`input_shape`)：

- 如果`data_format = 'channels_first'`，输入`4D`张量，尺寸为(`batch, channels, rows, cols`)。
- 如果`data_format = 'channels_last'`，输入`4D`张量，尺寸为(`batch, rows, cols, channels`)。

&emsp;&emsp;输出尺寸(输出的行列数可能会因为填充方法而改变)：

- 如果`data_format = 'channels_first'`，输出`4D`张量，尺寸为(`batch, filters, new_rows, new_cols`)。
- 如果`data_format = 'channels_last'`，输出`4D`张量，尺寸为(`batch, new_rows, new_cols, filters`)。

### Conv2DTranspose

&emsp;&emsp;该函数转置卷积层(有时被称为`反卷积`)：

``` python
keras.layers.Conv2DTranspose(
    filters, kernel_size, strides=(1, 1), padding='valid', data_format=None,
    activation=None, use_bias=True, kernel_initializer='glorot_uniform',
    bias_initializer='zeros', kernel_regularizer=None, bias_regularizer=None,
    activity_regularizer=None, kernel_constraint=None, bias_constraint=None)
```

需要反卷积的情况通常发生在用户想要对一个普通卷积的结果做反方向的变换，例如将具有该卷积层输出`shape`的`tensor`转换为具有该卷积层输入`shape`的`tensor`，同时保留与卷积层兼容的连接模式。当使用该层作为第一层时，应提供`input_shape`参数。例如`input_shape = (3, 128, 128)`代表`128 * 128`的彩色`RGB`图像。
&emsp;&emsp;输入尺寸(注意这里的输入`shape`指的是函数内部实现的输入`shape`，而非函数接口应指定的`input_shape`)：

- 如果`data_format='channels_first'`，输入`4D`张量，尺寸为(`batch, channels, rows, cols`)。
- 如果`data_format='channels_last'`，输入`4D`张量，尺寸为(`batch, rows, cols, channels`)。

&emsp;&emsp;输出尺寸(输出的行列数可能会因为填充方法而改变)：

- 如果`data_format='channels_first'`，输出`4D`张量，尺寸为(`batch, filters, new_rows, new_cols`)。
- 如果`data_format='channels_last'`，输出`4D`张量，尺寸为(`batch, new_rows, new_cols, filters`)。

### Conv3D

&emsp;&emsp;该函数是`3D`卷积层(例如立体空间卷积)：

``` python
keras.layers.Conv3D(
    filters, kernel_size, strides=(1, 1, 1), padding='valid', data_format=None,
    dilation_rate=(1, 1, 1), activation=None, use_bias=True,
    kernel_initializer='glorot_uniform', bias_initializer='zeros',
    kernel_regularizer=None, bias_regularizer=None, activity_regularizer=None,
    kernel_constraint=None, bias_constraint=None)
```

三维卷积对三维的输入进行滑动窗卷积，当使用该层作为第一层时，应提供`input_shape`参数。例如`input_shape = (3, 10, 128, 128)`代表对`10`帧`128 * 128`的彩色`RGB`图像进行卷积。数据的通道位置仍然由`data_format`参数指定。

- `filters`：整数，输出空间的维度(即卷积中滤波器的输出数量)。
- `kernel_size`：一个整数，或者`3`个整数表示的元组或列表，指明`3D`卷积窗口的深度、高度和宽度。如为单个整数，则表示在各个空间维度的相同长度。
- `strides`：一个整数，或者`3`个整数表示的元组或列表，指明卷积沿每一个空间维度的步长。如为单个整数，则表示在各个空间维度的相同步长。任何不为`1`的`strides`均与任何不为`1`的`dilation_rate`均不兼容。
- `data_format`：字符串，`channels_last`(默认)或`channels_first`之一，代表数据的通道维的位置。`channels_last`对应输入尺寸为(`batch, spatial_dim1, spatial_dim2, spatial_dim3, channels`)，`channels_first`对应输入尺寸为(`batch, channels, spatial_dim1, spatial_dim2, spatial_dim3`)。
- `dilation_rate`：一个整数或`3`个整数的元组或列表，指定`dilated convolution`中的膨胀比例。任何不为`1`的`dilation_rate`均与任何不为`1`的`strides`均不兼容。

&emsp;&emsp;输入尺寸(这里的输入`shape`指的是函数内部实现的输入`shape`，而非函数接口应指定的`input_shape`)：

- 如果`data_format='channels_first'`，输入`5D`张量，尺寸为(`samples, channels, conv_dim1, conv_dim2, conv_dim3`)。
- 如果`data_format='channels_last'`，输入`5D`张量，尺寸为(`samples, conv_dim1, conv_dim2, conv_dim3, channels`)。

&emsp;&emsp;输出尺寸(由于填充的原因，`new_conv_dim1`、`new_conv_dim2`和`new_conv_dim3`值可能已更改)：

- 如果`data_format='channels_first'`，输出`5D`张量，尺寸为(`samples, filters, new_conv_dim1, new_conv_dim2, new_conv_dim3`)。
- 如果`data_format='channels_last'`，输出`5D`张量，尺寸为(`samples, new_conv_dim1, new_conv_dim2, new_conv_dim3, filters`)。

### Cropping1D

&emsp;&emsp;该函数是`1D`输入的裁剪层(例如时间序列)：

``` python
keras.layers.Cropping1D(cropping=(1, 1))
```

它沿着时间维度(第`1`个轴)对输入进行裁剪。参数`cropping`是整数或整数元组(长度为`2`)，决定在裁剪维度(第`1`个轴)的开始和结束位置应该裁剪多少个单位。如果只提供了一个整数，那么这两个位置将使用相同的值。
&emsp;&emsp;输入尺寸：`3D`张量，尺寸为(`batch, axis_to_crop, features`)。
&emsp;&emsp;输出尺寸：`3D`张量，尺寸为(`batch, cropped_axis, features`)。

### Cropping2D

&emsp;&emsp;该函数是`2D`输入的裁剪层(例如图像)：

``` python
keras.layers.Cropping2D(cropping=((0, 0), (0, 0)), data_format=None)
```

它对`2D`输入(图像)进行裁剪，将在空域维度(即宽和高的方向上)裁剪。

- `cropping`：整数，或`2`个整数的元组，或`2`个整数的`2`个元组。

1. 如果为整数：将对宽度和高度应用相同的对称裁剪。
2. 如果为`2`个整数的元组：解释为对高度和宽度使用两个不同的裁剪值(`symmetric_height_crop, symmetric_width_crop`)。
3. 如果为`2`个整数的`2`个元组：解释为(`(top_crop, bottom_crop), (left_crop, right_crop)`)。

- `data_format`：字符串，`channels_last`(默认)或`channels_first`之一，代表图像的通道维的位置。`channels_last`对应输入尺寸为(`batch, height, width, channels`)，`channels_first`对应输入尺寸为(`batch, channels, height, width`)。

&emsp;&emsp;输入尺寸：

- 如果`data_format`为`channels_last`，则输入`4D`张量，尺寸为(`batch, rows, cols, channels`)。
- 如果`data_format`为`channels_first`，则输入`4D`张量，尺寸为(`batch, channels, rows, cols`)。

&emsp;&emsp;输出尺寸：

- 如果`data_format`为`channels_last`，则输出`4D`张量，尺寸为(`batch, cropped_rows, cropped_cols, channels`)。
- 如果`data_format`为`channels_first`，则输出`4D`张量，尺寸为(`batch, channels, cropped_rows, cropped_cols`)。

``` python
model = Sequential()
# 现在“model.output_shape == (None, 24, 20, 3)”
model.add(Cropping2D(cropping=((2, 2), (4, 4)),input_shape=(28, 28, 3)))
model.add(Conv2D(64, (3, 3), padding='same'))
# 现在“model.output_shape == (None, 20, 16. 64)”
model.add(Cropping2D(cropping=((2, 2), (2, 2))))
```

### Cropping3D

&emsp;&emsp;该函数是`3D`数据的裁剪层(例如空间或时空)：

``` python
keras.layers.Cropping3D(cropping=((1, 1), (1, 1), (1, 1)), data_format=None)
```

- `cropping`：整数，或`3`个整数的元组，或`2`个整数的`3`个元组。

1. 如果为整数：将对深度、高度和宽度应用相同的对称裁剪。
2. 如果为`3`个整数的元组：解释为对深度、高度和宽度的`3`个不同的对称裁剪值(`symmetric_dim1_crop, symmetric_dim2_crop, symmetric_dim3_crop`)。
3. 如果为`2`个整数的`3`个元组：解释为(`(left_dim1_crop, right_dim1_crop), (left_dim2_crop, right_dim2_crop), (left_dim3_crop, right_dim3_crop)`)。

- `data_format`：字符串，`channels_last`(默认)或`channels_first`之一，代表数据的通道维的位置。`channels_last`对应输入尺寸为(`batch, spatial_dim1, spatial_dim2, spatial_dim3, channels`)，`channels_first`对应输入尺寸为(`batch, channels, spatial_dim1, spatial_dim2, spatial_dim3`)。

&emsp;&emsp;输入尺寸：

- 如果`data_format`为`channels_last`，则输入`5D`张量，尺寸为(`batch, first_axis_to_crop, second_axis_to_crop, third_axis_to_crop, depth`)。
- 如果`data_format`为`channels_first`，则输入`5D`张量，尺寸为(`batch, depth, first_axis_to_crop, second_axis_to_crop, third_axis_to_crop`)。

&emsp;&emsp;输出尺寸：

- 如果`data_format`为`channels_last`，则输出`5D`张量，尺寸为(`batch, first_cropped_axis, second_cropped_axis, third_cropped_axis, depth`)。
- 如果`data_format`为`channels_first`，则输出`5D`张量，尺寸为(`batch, depth, first_cropped_axis, second_cropped_axis, third_cropped_axis`)。

### UpSampling1D

&emsp;&emsp;该函数是`1D`输入的上采样层：

``` pythbn
keras.layers.UpSampling1D(size=2)
```

沿着时间轴重复每个时间步`size`次。参数`size`是整数，上采样因子。
&emsp;&emsp;输入尺寸：`3D`张量，尺寸为(`batch, steps, features`)。
&emsp;&emsp;输出尺寸：`3D`张量，尺寸为(`batch, upsampled_steps, features`)。

### UpSampling2D

&emsp;&emsp;该函数是`2D`输入的上采样层：

``` python
keras.layers.UpSampling2D(size=(2, 2), data_format=None)
```

沿着数据的行和列分别重复`size[0]`和`size[1]`次。

- `size`：整数，或`2`个整数的元组，分别是行和列的上采样因子。
- `data_format`：字符串，`channels_last`(默认)或`channels_first`之一，代表图像的通道维的位置。`channels_last`对应输入尺寸为(`batch, height, width, channels`)，`channels_first`对应输入尺寸为(`batch, channels, height, width`)。

&emsp;&emsp;输入尺寸：

- 如果`data_format`为`channels_last`，则输入`4D`张量，尺寸为(`batch, rows, cols, channels`)。
- 如果`data_format`为`channels_first`，则输入`4D`张量，尺寸为(`batch, channels, rows, cols`)。

&emsp;&emsp;输出尺寸：

- 如果`data_format`为`channels_last`，则输出`4D`张量，尺寸为(`batch, upsampled_rows, upsampled_cols, channels`)。
- 如果`data_format`为`channels_first`，则输出`4D`张量，尺寸为(`batch, channels, upsampled_rows, upsampled_cols`)。

### UpSampling3D

&emsp;&emsp;该函数是`3D`输入的上采样层：

``` python
keras.layers.UpSampling3D(size=(2, 2, 2), data_format=None)
```

沿着数据的第`1`、`2`、`3`维度分别重复`size[0]`、`size[1]`和`size[2]`次。

- `size`：整数，或`3`个整数的元组，代表`dim1`、`dim2`和`dim3`的上采样因子。
- `data_format`：字符串，`channels_last`(默认)或`channels_first`之一，代表数据的通道维的位置。`channels_last`对应输入尺寸为(`batch, spatial_dim1, spatial_dim2, spatial_dim3, channels`)，`channels_first`对应输入尺寸为(`batch, channels, spatial_dim1, spatial_dim2, spatial_dim3`)。

&emsp;&emsp;输入尺寸：

- 如果`data_format`为`channels_last`，则输入`5D`张量，尺寸为(`batch, dim1, dim2, dim3, channels`)。
- 如果`data_format`为`channels_first`，则输入`5D`张量，尺寸为(`batch, channels, dim1, dim2, dim3`)。

&emsp;&emsp;输出尺寸：

- 如果`data_format`为`channels_last`，则输出`5D`张量，尺寸为(`batch, upsampled_dim1, upsampled_dim2, upsampled_dim3, channels`)。
- 如果`data_format`为`channels_first`，则输出`5D`张量，尺寸为(`batch, channels, upsampled_dim1, upsampled_dim2, upsampled_dim3`)。

### ZeroPadding1D

&emsp;&emsp;对`1D`输入的首尾端(如时域序列)填充`0`，以控制卷积以后向量的长度：

``` python
keras.layers.ZeroPadding1D(padding=1)
```

参数`padding`是整数，或长度为`2`的整数元组。

- 整数：在填充维度(第一个轴)的开始和结束处添加多少个零。
- 长度为`2`的整数元组：在填充维度的开始和结尾处添加多少个零(`(left_pad, right_pad)`)。

&emsp;&emsp;输入尺寸：`3D`张量，尺寸为(`batch, axis_to_pad, features`)。
&emsp;&emsp;输出尺寸：`3D`张量，尺寸为(`batch, padded_axis, features`)。

### ZeroPadding2D

&emsp;&emsp;对`2D`输入(如图片)的边界填充`0`，以控制卷积以后特征图的大小：

``` python
keras.layers.ZeroPadding2D(padding=(1, 1), data_format=None)
```

- `padding`：整数，或`2`个整数的元组，或`2`个整数的`2`个元组。

1. 如果为整数：将对宽度和高度运用相同的对称填充。
2. 如果为`2`个整数的元组：解释为高度和宽度的`2`个不同的对称裁剪值(`symmetric_height_pad, symmetric_width_pad`)。
3. 如果为`2`个整数的`2`个元组：解释为(`(top_pad, bottom_pad), (left_pad, right_pad)`)。

- `data_format`：字符串，`channels_last`(默认)或`channels_first`之一，代表图像的通道维的位置。`channels_last`对应输入尺寸为(`batch, height, width, channels`)，`channels_first`对应输入尺寸为(`batch, channels, height, width`)。

&emsp;&emsp;输入尺寸：

- 如果`data_format`为`channels_last`，则输入`4D`张量，尺寸为(`batch, rows, cols, channels`)。
- 如果`data_format`为`channels_first`，则输入`4D`张量，尺寸为(`batch, channels, rows, cols`)。

&emsp;&emsp;输出尺寸：

- 如果`data_format`为`channels_last`，则输出`4D`张量，尺寸为(`batch, padded_rows, padded_cols, channels`)。
- 如果`data_format`为`channels_first`，则输出`4D`张量，尺寸为(`batch, channels, padded_rows, padded_cols`)。

### ZeroPadding3D

&emsp;&emsp;将数据的三个维度上填充`0`，本层目前只能在使用`Theano`为后端时可用：

``` python
keras.layers.ZeroPadding3D(padding=(1, 1, 1), data_format=None)
```

- `padding`：整数，或`3`个整数的元组，或`2`个整数的`3`个元组。

1. 如果为整数：将对深度、高度和宽度运用相同的对称填充。
2. 如果为`3`个整数的元组：解释为深度、高度和宽度的三个不同的对称填充值(`symmetric_dim1_pad, symmetric_dim2_pad, symmetric_dim3_pad`)。
3. 如果为`2`个整数的`3`个元组：解释为(`(left_dim1_pad, right_dim1_pad), (left_dim2_pad, right_dim2_pad), (left_dim3_pad, right_dim3_pad)`)。

- `data_format`：字符串，`channels_last`(默认)或`channels_first`之一，代表数据的通道维的位置。`channels_last`对应输入尺寸为(`batch, spatial_dim1, spatial_dim2, spatial_dim3, channels`)，`channels_first`对应输入尺寸为(`batch, channels, spatial_dim1, spatial_dim2, spatial_dim3`)。

&emsp;&emsp;输入尺寸：

- 如果`data_format`为`channels_last`，输入`5D`张量，尺寸为(`batch, first_axis_to_pad, second_axis_to_pad, third_axis_to_pad, depth`)。
- 如果`data_format`为`channels_first`，输入`5D`张量，尺寸为(`batch, depth, first_axis_to_pad, second_axis_to_pad, third_axis_to_pad`)。

&emsp;&emsp;输出尺寸：

- 如果`data_format`为`channels_last`，输出`5D`张量，尺寸为(`batch, first_padded_axis, second_padded_axis, third_axis_to_pad, depth`)。
- 如果`data_format`为`channels_first`，输出`5D`张量，尺寸为(`batch, depth, first_padded_axis, second_padded_axis, third_axis_to_pad`)。