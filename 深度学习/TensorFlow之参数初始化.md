---
title: TensorFlow之参数初始化
date: 2019-01-01 14:23:16
categories: 深度学习
---
&emsp;&emsp;`CNN`中最重要的就是参数了，包括`W`和`b`。训练`CNN`的最终目的就是得到最好的参数，使得目标函数取得最小值。参数的初始化也同样重要，因此微调受到很多人的重视。`tf`提供的所有初始化方法都定义在`tensorflow/python/ops/init_ops.py`。

### tf.constant_initializer

&emsp;&emsp;可以简写为`tf.Constant`，初始化为常数，通常偏置项就是用它初始化的。由它衍生出两个初始化方法：

- `tf.zeros_initializer`：可以简写为`tf.Zeros`。
- `tf.ones_initializer`：可以简写为`tf.Ones`。

在卷积层中，将偏置项`b`初始化为`0`，有多种写法：

``` python
conv1 = tf.layers.conv2d(  # 方法1
    batch_images, filters=64, kernel_size=7, strides=2, activation=tf.nn.relu,
    kernel_initializer=tf.TruncatedNormal(stddev=0.01), bias_initializer=tf.Constant(0))

bias_initializer = tf.constant_initializer(0)  # 方法2
bias_initializer = tf.zeros_initializer()  # 方法3
bias_initializer = tf.Zeros()  # 方法4
```

将`W`初始化成拉普拉斯算子的方法如下：

``` python
value = [1, 1, 1, 1, -8, 1, 1, 1, 1]
init = tf.constant_initializer(value)
W = tf.get_variable('W', shape=[3, 3], initializer=init)
```

### tf.truncated_normal_initializer

&emsp;&emsp;可以简写为`tf.TruncatedNormal`，生成截断正态分布的随机数：

``` python
tf.TruncatedNormal(mean=0.0, stddev=1.0, seed=None, dtype=dtypes.float32)
```

这四个参数分别用于指定均值、标准差、随机数种子和随机数的数据类型，一般只需要设置`stddev`这一个参数。

``` python
conv1 = tf.layers.conv2d(  # 代码示例1
    batch_images, filters=64, kernel_size=7, strides=2, activation=tf.nn.relu,
    kernel_initializer=tf.TruncatedNormal(stddev=0.01), bias_initializer=tf.Constant(0))
​
conv1 = tf.layers.conv2d(  # 代码示例2
    batch_images, filters=64, kernel_size=7, strides=2, activation=tf.nn.relu,
    kernel_initializer=tf.truncated_normal_initializer(stddev=0.01),
    bias_initializer=tf.zero_initializer())
```

### tf.random_normal_initializer

&emsp;&emsp;可以简写为`tf.RandomNormal`，生成标准正态分布的随机数，参数和`truncated_normal_initializer`一样。

### tf.random_uniform_initializer

&emsp;&emsp;可以简写为`tf.RandomUniform`，生成均匀分布的随机数：

``` python
tf.RandomUniform(minval=0, maxval=None, seed=None, dtype=dtypes.float32)
```

这四个参数分别用于指定最小值、最大值、随机数种子和类型。

### tf.uniform_unit_scaling_initializer

&emsp;&emsp;可以简写为`tf.UniformUnitScaling`，和均匀分布差不多：

``` python
tf.UniformUnitScaling(factor=1.0, seed=None, dtype=dtypes.float32)
```

只是这个初始化方法不需要指定最小最大值，它们是通过计算得到的：

``` python
max_val = math.sqrt(3 / input_size) * factor
min_val = -max_val
```

这里的`input_size`是指输入数据的维数，假设输入为`x`，运算为`x * W`，则`input_size= W.shape[0]`，它的分布区间为`[-max_val, max_val]`。

### tf.variance_scaling_initializer

&emsp;&emsp;可以简写为`tf.VarianceScaling`：

``` python
tf.VarianceScaling(scale=1.0, mode="fan_in", distribution="normal", seed=None, dtype=dtypes.float32)
```

- `scale`：缩放尺度(正浮点数)。
- `mode`：`fan_in`、`fan_out`和`fan_avg`中的一个，用于计算标准差`stddev`的值。
- `distribution`：分布类型，`normal`或`uniform`中的一个。

1. 当`distribution = "normal"`时，生成`truncated normal distribution`(截断正态分布)的随机数，其中`stddev = sqrt(scale / n)`，`n`的计算与`mode`参数有关。如果`mode = "fan_in"`，`n`为输入单元的结点数；如果`mode = "fan_out"`，`n`为输出单元的结点数；如果`mode = "fan_avg"`，`n`为输入和输出单元结点数的平均值
2. 当`distribution = "uniform"`时，生成均匀分布的随机数，假设分布区间为`[-limit, limit]`，则：

``` python
limit = sqrt(3 * scale / n)
```

### tf.orthogonal_initializer

&emsp;&emsp;可以简写为`tf.Orthogonal`，生成正交矩阵的随机数。当需要生成的参数是`2`维时，这个正交矩阵是由均匀分布的随机数矩阵经过`SVD`分解而来。

### tf.glorot_uniform_initializer

&emsp;&emsp;也称为`Xavier uniform initializer`，由一个均匀分布(`uniform distribution`)来初始化数据。假设均匀分布的区间是`[-limit, limit]`，则：

``` python
limit = sqrt(6 / (fan_in + fan_out))
```

其中的`fan_in`和`fan_out`分别表示输入单元的结点数和输出单元的结点数。

### glorot_normal_initializer

&emsp;&emsp;也称之为`Xavier normal initializer`，由一个`truncated normal distribution`来初始化数据：

``` python
stddev = sqrt(2 / (fan_in + fan_out))
```

其中的`fan_in`和`fan_out`分别表示输入单元的结点数和输出单元的结点数。