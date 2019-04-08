---
title: TensorFlow之layers模块
date: 2019-01-16 14:56:39
categories: 深度学习
---
&emsp;&emsp;`TensorFlow`的`layers`模块提供用于深度学习的更高层次封装的`API`，利用它可以轻松地构建模型。`tf.layers`模块提供的方法有：

方法                  | 说明
----------------------|------
`Input`               | 用于实例化一个输入`Tensor`，作为神经网络的输入
`average_pooling1d`   | 一维平均池化层
`average_pooling2d`   | 二维平均池化层
`average_pooling3d`   | 三维平均池化层
`batch_normalization` | 批量标准化层
`conv1d`              | 一维卷积层
`conv2d`              | 二维卷积层
`conv2d_transpose`    | 二维反卷积层
`conv3d`              | 三维卷积层
`conv3d_transpose`    | 三维反卷积层
`dense`               | 全连接层
`dropout`             | `Dropout`层
`flatten`             | `Flatten`层，把一个`Tensor`展平
`max_pooling1d`       | 一维最大池化层
`max_pooling2d`       | 二维最大池化层
`max_pooling3d`       | 三维最大池化层
`separable_conv2d`    | 二维深度可分离卷积层

### Input

&emsp;&emsp;`tf.layers.Input`(目前已更名为`tf.keras.Input`)这个方法用于输入数据，类似于`tf.placeholder`，相当于一个占位符，可以通过传入`tensor`参数来进行赋值：

``` python
Input(
    shape=None, batch_size=None, name=None,
    dtype=tf.float32, sparse=False, tensor=None)
```

- `shape`：可选参数，是一个由数字组成的元组或列表。这个`shape`比较特殊，它不包含`batch_size`，比如传入的`shape`为`[32]`，那么它会将`shape`转化为`[?, 32]`。
- `batch_size`：可选参数，代表输入数据的`batch_size`，可以是数字或者`None`。
- `name`：可选参数，输入层的名称。
- `dtype`：可选参数，元素的类型。
- `sparse`：可选参数，指定是否以稀疏矩阵的形式来创建`placeholder`。
- `tensor`：可选参数，如果指定的话，那么创建的内容便不再是一个`placeholder`，会用此`Tensor`初始化。

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[32])
print(x)
y = tf.layers.dense(x, 16, activation=tf.nn.softmax)
print(y)
```

首先用`Input`方法初始化了一个`placeholder`，注意这时我们没有传入`tensor`参数。然后调用了`dense`方法构建了一个全连接网络，激活函数使用`softmax`。执行结果如下：

``` python
Tensor("input_1:0", shape=(?, 32), dtype=float32)
Tensor("dense/Softmax:0", shape=(?, 16), dtype=float32)
```

注意此时`shape`给我们做了转化，本来是`[32]`，结果转化成了`[?, 32]`，第一维代表`batch_size`。所以我们需要注意，在调用此方法时不需要去关心`batch_size`这一维。
&emsp;&emsp;如果我们在初始化时传入一个`Tensor`：

``` python
import tensorflow as tf
​
data = tf.constant([1, 2, 3])
x = tf.keras.Input(tensor=data)
print(x)
```

执行结果如下，可以看到它可以自动计算出其`shape`和`dtype`：

``` python
Tensor("Const:0", shape=(3,), dtype=int32)
```

### batch_normalization

&emsp;&emsp;此方法是批量标准化的方法，对数据经过处理之后可以加快训练速度。

``` python
batch_normalization(
    inputs, axis=-1, momentum=0.99, epsilon=0.001, center=True, scale=True,
    beta_initializer=tf.zeros_initializer(), gamma_initializer=tf.ones_initializer(),
    moving_mean_initializer=tf.zeros_initializer(), moving_variance_initializer=tf.ones_initializer(),
    beta_regularizer=None, gamma_regularizer=None, beta_constraint=None, gamma_constraint=None,
    training=False, trainable=True, name=None, reuse=None, renorm=False, renorm_clipping=None,
    renorm_momentum=0.99, fused=None, virtual_batch_size=None, adjustment=None)
```

- `inputs`：输入数据。
- `axis`：可选参数，进行标准化操作时操作数据的维度。
- `momentum`：可选参数，动态均值的动量。
- `epsilon`：可选参数，大于`0`的小浮点数，用于防止除`0`错误。
- `center`：可选参数，若设为`True`，将会把`beta`作为偏置加上去，否则忽略参数`beta`。
- `scale`：可选参数，若设为`True`，则会乘以`gamma`，否则不使用`gamma`。
- `beta_initializer`：可选参数，`beta`权重的初始方法。
- `gamma_initializer`：可选参数，`gamma`的初始化方法。
- `moving_mean_initializer`：可选参数，动态均值的初始化方法。
- `moving_variance_initializer`：可选参数，动态方差的初始化方法。
- `beta_regularizer`: 可选参数，`beta`的正则化方法。
- `gamma_regularizer`: 可选参数，`gamma`的正则化方法。
- `beta_constraint`: 可选参数，加在`beta`上的约束项。
- `gamma_constraint`: 可选参数，加在`gamma`上的约束项。
- `training`：可选参数，返回结果是`training`模式。
- `trainable`：可选参数，布尔类型。如果为`True`，则将变量添加到`GraphKeys.TRAINABLE_VARIABLES`中。
- `name`：可选参数，层名称。
- `reuse`：可选参数，根据层名判断是否重复利用。
- `renorm`：可选参数，是否要用`Batch Renormalization`。
- `renorm_clipping`：可选参数，是否要用`rmax`、`rmin`、`dmax`来`scalar Tensor`。
- `renorm_momentum`：可选参数，用来更新动态均值和标准差的`Momentum`值。
- `fused`：可选参数，是否使用一个更快的、融合的实现方法。
- `virtual_batch_size`：可选参数，`int`类型数字，指定一个虚拟`batch size`。
- `adjustment`：可选参数，对标准化后的结果进行适当调整。详细用法参考`https://www.tensorflow.org/api_docs/python/tf/layers/batch_normalization`。

该函数的用法是在输入数据后面加一层`batch_normalization`即可：

``` python
x = tf.keras.Input(shape=[32])
x = tf.layers.batch_normalization(x)
y = tf.layers.dense(x, 20)
```

### dense

&emsp;&emsp;`dense`是全连接网络，`layers`模块提供了一个`dense`方法来实现此操作，定义在`tensorflow/python/layers/core.py`中：

``` python
dense(
    inputs, units, activation=None, use_bias=True, kernel_initializer=None,
    bias_initializer=tf.zeros_initializer(), kernel_regularizer=None,
    bias_regularizer=None, activity_regularizer=None, kernel_constraint=None,
    bias_constraint=None, trainable=True, name=None, reuse=None)
```

- `inputs`：输入数据。
- `units`：神经元的数量。
- `activation`：可选参数，如果为`None`，则是线性激活。
- `use_bias`：可选参数，是否使用偏置。
- `kernel_initializer`：可选参数，权重的初始化方法。如果为`None`，则使用默认的`Xavier`初始化方法。
- `bias_initializer`：可选参数，偏置的初始化方法。
- `kernel_regularizer`：可选参数，施加在权重上的正则项。
- `bias_regularizer`：可选参数，施加在偏置上的正则项。
- `activity_regularizer`：可选参数，施加在输出上的正则项。
- `kernel_constraint`：可选参数，施加在权重上的约束项。
- `bias_constraint`：可选参数，施加在偏置上的约束项。
- `trainable`：可选参数，布尔类型。如果为`True`，则将变量添加到`GraphKeys.TRAINABLE_VARIABLES`中。
- `name`：可选参数，卷积层的名称。
- `reuse`：可选参数，布尔类型。

该函数返回全连接网络处理后的`Tensor`。

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[32])
print(x)
y1 = tf.layers.dense(x, 16, activation=tf.nn.relu)
print(y1)
y2 = tf.layers.dense(y1, 5, activation=tf.nn.sigmoid)
print(y2)
```

首先我们用`Input`定义了`[?, 32]`的输入数据，然后经过第一层全连接网络，此时指定了神经元个数为`16`，激活函数为`relu`。接着输出结果经过第二层全连接网络，此时指定了神经元个数为`5`，激活函数为`sigmoid`。执行结果如下，可以看到输出结果的最后一维度就等于神经元的个数：

``` python
Tensor("input_1:0", shape=(?, 32), dtype=float32)
Tensor("dense/Relu:0", shape=(?, 16), dtype=float32)
Tensor("dense_1/Sigmoid:0", shape=(?, 5), dtype=float32)
```

### convolution

&emsp;&emsp;`convolution`就是卷积，`layers`层提供了多个卷积方法，例如`conv1d`、`conv2d`和`conv3d`分别代表一维、二维、三维卷积。另外还有`conv2d_transpose`、`conv3d_transpose`，分别代表二维和三维反卷积，还有`separable_conv2d`方法代表二维深度可分离卷积。

``` python
conv2d(
    inputs, filters, kernel_size, strides=(1, 1), padding='valid', data_format='channels_last',
    dilation_rate=(1, 1), activation=None, use_bias=True, kernel_initializer=None,
    bias_initializer=tf.zeros_initializer(), kernel_regularizer=None, bias_regularizer=None,
    activity_regularizer=None, kernel_constraint=None, bias_constraint=None,
    trainable=True, name=None, reuse=None)
```

- `inputs`：需要进行操作的输入数据。
- `filters`：输出通道的个数，即`output_channels`。
- `kernel_size`：卷积核大小，必须是一个数字(高和宽都是此数字)或者长度为`2`的列表(分别代表高、宽)。
- `strides`：可选参数，卷积步长，必须是一个数字(高和宽都是此数字)或者长度为`2`的列表(分别代表高、宽)。
- `padding`：可选参数，`padding`的模式，有`valid`和`same`两种，大小写不区分。
- `data_format`：可选参数，分为`channels_last`和`channels_first`两种模式，代表了输入数据的维度类型。如果是`channels_last`，那么输入数据的`shape`为(`batch, height, width, channels`)；如果是`channels_first`，那么输入数据的`shape`为(`batch, channels, height, width`)。
- `dilation_rate`：可选参数，卷积的扩张率。例如当扩张率为`2`时，卷积核内部就会有边距，`3 * 3`的卷积核就会变成`5 * 5`。
- `activation`：可选参数。如果为`None`，则是线性激活。
- `use_bias`：可选参数，是否使用偏置。
- `kernel_initializer`：可选参数，权重的初始化方法。如果为`None`，则使用默认的`Xavier`初始化方法。
- `bias_initializer`：可选参数，偏置的初始化方法。
- `kernel_regularizer`：可选参数，施加在权重上的正则项。
- `bias_regularizer`：可选参数，施加在偏置上的正则项。
- `activity_regularizer`：可选参数，施加在输出上的正则项。
- `kernel_constraint`：可选参数，施加在权重上的约束项。
- `bias_constraint`：可选参数，施加在偏置上的约束项。
- `trainable`：可选参数，布尔类型。如果为`True`，则将变量添加到`GraphKeys.TRAINABLE_VARIABLES`中。
- `name`：可选参数，卷积层的名称。
- `reuse`：可选参数，布尔类型。

该函数返回卷积后的`Tensor`。

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[20, 20, 3])
y = tf.layers.conv2d(x, filters=6, kernel_size=2, padding='same')
print(y)
```

首先声明了一个`[?, 20, 20, 3]`的输入`x`，然后将其传给`conv2d`方法。`filters`设定为`6`，即输出通道为`6`；`kernel_size`为`2`，即卷积核大小为`2 * 2`；`padding`方式设置为`same`，那么输出结果的宽高和原来一定是相同的，结果如下：

``` python
Tensor("conv2d/BiasAdd:0", shape=(?, 20, 20, 6), dtype=float32)
```

如果我们让`padding`使用默认的`valid`模式：

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[20, 20, 3])
y = tf.layers.conv2d(x, filters=6, kernel_size=2)
print(y)
```

执行结果如下所示，这是因为步长默认为`1`，卷积核大小为`2 * 2`，所以得到的结果的高宽即为`(20 - (2 - 1)) * (20 - (2 - 1)) = 19 * 19`：

``` python
Tensor("conv2d/BiasAdd:0", shape=(?, 19, 19, 6), dtype=float32)
```

对于卷积核的大小，我们可以传入一个列表：

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[20, 20, 3])
y = tf.layers.conv2d(x, filters=6, kernel_size=[2, 3])
print(y)
```

执行结果如下：

``` python
Tensor("conv2d/BiasAdd:0", shape=(?, 19, 18, 6), dtype=float32)
```

这时卷积核的大小变成了`2 * 3`，即高为`2`，宽为`3`，结果就变成了`[?, 19, 18, 6]`。这是因为步长默认为`1`，卷积核大小为`2 * 2`，所以结果的高宽即为`(20 - (2 - 1)) * (20 - (3 - 1)) = 19 * 18`。
&emsp;&emsp;对于步长，我们也可以传入一个列表：

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[20, 20, 3])
y = tf.layers.conv2d(x, filters=6, kernel_size=[2, 3], strides=[2, 2])
print(y)
```

这时卷积核大小变成了`2 * 3`，步长变成了`2 * 2`，所以结果的高宽为`ceil(20 - (2 - 1)) / 2 * ceil(20 - (3 - 1)) / 2 = 10 * 9`，得到的结果即为`[?, 10, 9, 6]`：

``` python
Tensor("conv2d/BiasAdd:0", shape=(?, 10, 9, 6), dtype=float32)
```

我们还可以传入激活函数，或者禁用`bias`等操作：

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[20, 20, 3])
y = tf.layers.conv2d(x, filters=6, kernel_size=2, activation=tf.nn.relu, use_bias=False)
print(y)
```

执行结果如下：

``` python
Tensor("conv2d/Relu:0", shape=(?, 19, 19, 6), dtype=float32)
```

&emsp;&emsp;另外还有反卷积操作，反卷积顾名思义即卷积的反向操作，即输入卷积的结果，输出卷积前的结果：

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[20, 20, 3])
y = tf.layers.conv2d_transpose(x, filters=6, kernel_size=2, strides=2)
print(y)
```

例如此处输入的图像高宽为`20 * 20`，经过卷积核为`2`，步长为`2`的反卷积处理，得到的结果高宽就变为了`40 * 40`，执行结果如下：

``` python
Tensor("conv2d_transpose/BiasAdd:0", shape=(?, 40, 40, 6), dtype=float32)
```

### pooling

&emsp;&emsp;`pooling`即池化层，`layers`模块提供了多个池化方法，这些池化方法都是类似的，包括`max_pooling1d`、`max_pooling2d`、`max_pooling3d`、`average_pooling1d`、`average_pooling2d`和`average_pooling3d`，分别代表一维、二维、三维、最大和平均池化方法，它们都定义在`tensorflow/python/layers/pooling.py`中。这里以`max_pooling2d`方法为例进行介绍：

``` python
max_pooling2d(
    inputs, pool_size, strides, padding='valid',
    data_format='channels_last', name=None)
```

- `inputs`: 需要池化的输入对象，必须是4维的。
- `pool_size`：池化窗口大小，必须是一个数字(高和宽都是此数字)或者长度为`2`的列表(分别代表高、宽)。
- `strides`：池化步长，必须是一个数字(高和宽都是此数字)或者长度为`2`的列表(分别代表高、宽)。
- `padding`：可选参数，`padding`的方法，可选`valid`或者`same`，大小写不区分。
- `data_format`：可选参数，分为`channels_last`和`channels_first`两种模式，代表了输入数据的维度类型。如果是`channels_last`，那么输入数据的`shape`为(`batch, height, width, channels`)；如果是`channels_first`，那么输入数据的`shape`为(`batch, channels, height, width`)。
- `name`：可选参数，池化层的名称。

该函数返回经过池化处理后的`Tensor`。

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[20, 20, 3])
print(x)
y = tf.layers.conv2d(x, filters=6, kernel_size=3, padding='same')
print(y)
p = tf.layers.max_pooling2d(y, pool_size=2, strides=2)
print(p)
```

首先指定了输入`x`的`shape`为`[20, 20, 3]`，然后对其进行了卷积以及池化操作，最后得到池化后的结果。执行结果如下：

``` python
Tensor("input_1:0", shape=(?, 20, 20, 3), dtype=float32)
Tensor("conv2d/BiasAdd:0", shape=(?, 20, 20, 6), dtype=float32)
Tensor("max_pooling2d/MaxPool:0", shape=(?, 10, 10, 6), dtype=float32)
```

这里池化窗口的大小是`2 * 2`，步长也是`2`，所以原本卷积后的`shape`为`[?, 20, 20, 6]`，结果就变成了`[?, 10, 10, 6]`。

### dropout

&emsp;&emsp;`dropout`是指在深度学习网络的训练过程中，对于神经网络单元，按照一定的概率将其暂时从网络中丢弃，可以用来防止过拟合。`layers`模块提供了`dropout`方法来实现这一操作，定义在`tensorflow/python/layers/core.py`：

``` python
dropout(inputs, rate=0.5, noise_shape=None, seed=None, training=False, name=None)
```

- `inputs`：输入数据。
- `rate`：可选参数，即`dropout rate`。如果设置为`0.1`，则会丢弃`10%`的神经元。
- `noise_shape`：可选参数，`int32`类型的一维`Tensor`，它代表了`dropout mask`的`shape`。
- `seed`：可选参数，产生随机数的种子值。
- `training`：可选参数，布尔类型，代表是否标志为`training`模式。
- `name`：可选参数，`dropout`层的名称。

该函数返回经过`dropout`层之后的`Tensor`。

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[32])
print(x)
y = tf.layers.dense(x, 16, activation=tf.nn.softmax)
print(y)
d = tf.layers.dropout(y, rate=0.2)
print(d)
```

这里我们使用`dropout`方法实现了`droput`操作，并制定`dropout rate`为`0.2`，最后输出结果的`shape`和原来是一致的：

``` python
Tensor("input_1:0", shape=(?, 32), dtype=float32)
Tensor("dense/Softmax:0", shape=(?, 16), dtype=float32)
Tensor("dropout/Identity:0", shape=(?, 16), dtype=float32)
```

### flatten

&emsp;&emsp;`flatten`方法可以对`Tensor`进行展平操作，定义在`tensorflow/python/layers/core.py`：

``` python
flatten(inputs, name=None)
```

参数`inputs`是输入数据，`name`是该层的名称，该函数返回展平后的`Tensor`。

``` python
import tensorflow as tf
​
x = tf.keras.Input(shape=[5, 6])
print(x)
y = tf.layers.flatten(x)
print(y)
```

这里输入数据的`shape`为`[?, 5, 6]`，经过`flatten`层之后，就会变成`[?, 30]`，也就是将除了第一维的数据维度相乘，对原`Tensor`进行展平：

``` python
Tensor("input_1:0", shape=(?, 5, 6), dtype=float32)
Tensor("flatten/Reshape:0", shape=(?, 30), dtype=float32)
```

如果第一维是一个已知数据的话，它依然进行同样的处理：

``` python
import tensorflow as tf
​
x = tf.placeholder(shape=[5, 6, 2], dtype=tf.float32)
print(x)
y = tf.layers.flatten(x)
print(y)
```

执行结果：

``` python
Tensor("Placeholder:0", shape=(5, 6, 2), dtype=float32)
Tensor("flatten/Reshape:0", shape=(5, 12), dtype=float32)
```