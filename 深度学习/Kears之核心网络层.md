---
title: Kears之核心网络层
categories: 深度学习
date: 2019-01-15 14:59:09
---
### Dense

&emsp;&emsp;该函数就是常用的的全连接层：<!--more-->

``` python
keras.layers.Dense(
    units, activation=None, use_bias=True, kernel_initializer='glorot_uniform',
    bias_initializer='zeros', kernel_regularizer=None, bias_regularizer=None,
    activity_regularizer=None, kernel_constraint=None, bias_constraint=None)
```

`Dense`实现以下操作：`output = activation(dot(input, kernel) + bias)`，其中`activation`是按逐个元素计算的激活函数，`kernel`是由网络层创建的权值矩阵，以及`bias`是其创建的偏置向量(只在`use_bias`为`True`时才有用)。如果本层的输入数据的维度大于`2`，则会先被压为与`kernel`相匹配的大小：

``` python
model = Sequential()
# 现在模型就会以尺寸为(*, 16)的数组作为输入，其输出数组的尺寸为(*, 32)
model.add(Dense(32, input_shape=(16,)))
model.add(Dense(32))  # 在第一层之后，你就不再需要指定输入的尺寸了
```

- `units`：正整数，输出空间维度。
- `activation`：激活函数。若不指定，则不使用激活函数(即线性激活`a(x) = x`)。
- `use_bias`：布尔值，该层是否使用偏置向量。
- `kernel_initializer`：权值初始化方法，为预定义初始化方法名的字符串，或用于初始化权重的初始化器。
- `bias_initializer`：偏置向量初始化方法，为预定义初始化方法名的字符串，或用于初始化偏置向量的初始化器。
- `kernel_regularizer`：运用到`kernel`权值矩阵的正则化函数。
- `bias_regularizer`：运用到偏置向量的的正则化函数。
- `activity_regularizer`：运用到层的输出的正则化函数。
- `kernel_constraint`：运用到`kernel`权值矩阵的约束函数。
- `bias_constraint`：运用到偏置向量的约束函数。

&emsp;&emsp;输入尺寸：`nD`张量，尺寸为(`batch_size, ..., input_dim`)。最常见的情况是一个尺寸为(`batch_size, input_dim`)的`2D`输入。
&emsp;&emsp;输出尺寸：`nD`张量，尺寸为(`batch_size, ..., units`)。例如，对于尺寸为(`batch_size, input_dim`)的`2D`输入，输出的尺寸为(`batch_size, units`)。

### Activation

&emsp;&emsp;该函数将激活函数应用于输出：

``` python
keras.layers.Activation(activation)
```

参数`activation`是要使用的激活函数的名称，或者选择一个`Theano`或`TensorFlow`操作。
&emsp;&emsp;输入尺寸：任意尺寸。当使用此层作为模型中的第一层时，使用参数`input_shape`(整数元组，不包括样本数的轴)。
&emsp;&emsp;输出尺寸：与输入相同。

### Dropout

&emsp;&emsp;该函数将`Dropout`应用于输入：

``` python
keras.layers.Dropout(rate, noise_shape=None, seed=None)
```

`Dropout`将在训练过程中每次更新参数时，按一定概率(`rate`)随机断开输入神经元，用于防止过拟合。

- `rate`：在`0`和`1`之间浮动，控制需要断开的神经元的比例。
- `noise_shape`：`1D`整数张量，为将要应用在输入上的二值`Dropout mask`的`shape`，例如你的输入为(`batch_size, timesteps, features`)，并且你希望在各个时间步上的`Dropout mask`都相同，则可传入`noise_shape = (batch_size, 1, features)`。
- `seed`：一个作为随机种子的`Python`整数。

### Flatten

&emsp;&emsp;`Flatten`层用来将输入`压平`，也就是把多维的输入一维化，常用在从卷积层到全连接层的过渡，不影响`batch`的大小：

``` python
keras.layers.Flatten()
```

使用示例：

``` python
model = Sequential()
# 现在“model.output_shape == (None, 64, 32, 32)”
model.add(Conv2D(64, 3, 3, border_mode='same', input_shape=(3, 32, 32)))
model.add(Flatten())  # 现在“model.output_shape == (None, 65536)”
```

### Reshape

&emsp;&emsp;该函数将输入重新调整为特定的尺寸：

``` python
keras.layers.Reshape(target_shape)
```

参数`target_shape`是目标尺寸，为整数元组，不包含样本数目的维度(即`batch`大小)。
&emsp;&emsp;输入尺寸：任意，尽管输入尺寸中的所有维度必须是固定的。当使用此层作为模型中的第一层时，使用参数`input_shape`(整数元组，不包括样本数的轴)。
&emsp;&emsp;输出尺寸：`(batch_size,) + target_shape`。

``` python
model = Sequential()
# 现在“model.output_shape == (None, 3, 4)”，None是批表示的维度
model.add(Reshape((3, 4), input_shape=(12,)))
model.add(Reshape((6, 2)))  # 现在“model.output_shape == (None, 6, 2)”
# 还支持使用“-1”表示维度的尺寸推断，现在“model.output_shape == (None, 3, 2, 2)”
model.add(Reshape((-1, 2, 2)))
```

### Permute

&emsp;&emsp;`Permute`层将输入的维度按照给定模式进行重排，例如当需要将`RNN`和`CNN`网络连接时，可能会用到该层：

``` python
keras.layers.Permute(dims)
```

参数`dims`是整数元组，指定重排的模式，不包含样本数的维度。重排模式的下标从`1`开始，例如(`2, 1`)代表将输入的第二个维度重排到输出的第一个维度，而将输入的第一个维度重排到第二个维度。
&emsp;&emsp;输入尺寸：任意。当使用此层作为模型中的第一层时，使用参数`input_shape`(整数元组，不包括样本数的轴)。
&emsp;&emsp;输出尺寸：与输入尺寸相同，但是维度根据指定的模式重新排列。

``` python
model = Sequential()
# 现在“model.output_shape == (None, 64, 10)”，None是批表示的维度
model.add(Permute((2, 1), input_shape=(10, 64)))
```

### RepeatVector

&emsp;&emsp;该函数将输入重复`n`次：

``` python
keras.layers.RepeatVector(n)
```

参数`n`是整数，即重复次数。
&emsp;&emsp;输入尺寸：`2D`张量，尺寸为(`num_samples, features`)。
&emsp;&emsp;输出尺寸：`3D`张量，尺寸为(`num_samples, n, features`)。

``` python
model = Sequential()
# 现在“model.output_shape == (None, 32)”，None是批表示的维度
model.add(Dense(32, input_dim=32))
model.add(RepeatVector(3))  # 现在“model.output_shape == (None, 3, 32)”
```

### Lambda

&emsp;&emsp;该函数将上一层的输出封装为`Layer`对象：

``` python
keras.layers.Lambda(function, output_shape=None, mask=None, arguments=None)
```

- `function`：需要封装的函数，将输入张量作为第一个参数，即上一层的输出。
- `output_shape`：预期的函数输出尺寸，只在使用`Theano`时有意义，可以是一个`tuple`，也可以是一个根据输入`shape`计算输出`shape`的函数。
- `arguments`：需要传递给函数的关键字参数。

``` python
model.add(Lambda(lambda x: x ** 2))  # 添加一个“x -> x^2”层

def antirectifier(x):  # 添加一个网络层，返回输入的正数部分与负数部分的反面的连接
    x -= K.mean(x, axis=1, keepdims=True)
    x = K.l2_normalize(x, axis=1)
    pos = K.relu(x)
    neg = K.relu(-x)
    return K.concatenate([pos, neg], axis=1)

def antirectifier_output_shape(input_shape):
    shape = list(input_shape)
    assert len(shape) == 2  # only valid for 2D tensors
    shape[-1] *= 2
    return tuple(shape)

model.add(Lambda(antirectifier, output_shape=antirectifier_output_shape))
```

&emsp;&emsp;输入尺寸：任意，当使用此层作为模型中的第一层时，使用参数`input_shape`(整数元组，不包括样本数的轴)。
&emsp;&emsp;输出尺寸：由`output_shape`参数指定(或者在使用`TensorFlow`时，自动推理得到)。

### ActivityRegularization

&emsp;&emsp;经过本层的数据不会有任何变化，但会基于其激活值更新损失函数值：

``` python
keras.layers.ActivityRegularization(l1=0.0, l2=0.0)
```

参数`l1`是`L1`范数正则化因子(正数浮点型)；`l2`是`L2`范数正则化因子(正数浮点型)。
&emsp;&emsp;输入尺寸：任意，当使用此层作为模型中的第一层时，使用参数`input_shape`(整数元组，不包括样本数的轴)。
&emsp;&emsp;输出尺寸：与输入相同。

### Masking

&emsp;&emsp;使用给定的值对输入的序列信号进行`屏蔽`，用以定位需要跳过的时间步：

``` python
keras.layers.Masking(mask_value=0.0)
```

对于输入张量的时间步，即输入张量的第`1`维度(维度从`0`开始算)，如果输入张量在该时间步上都等于`mask_value`，则该时间步将在模型接下来的所有层(只要支持`masking`)被跳过(即`屏蔽`)。
&emsp;&emsp;考虑将要喂入一个`LSTM`层的`Numpy`矩阵`x`，尺寸为(`samples, timesteps, features`)，现将其送入`LSTM`层。因为你缺少时间步为`3`和`5`的信号，所以你希望将其掩盖，这时候应该：

- 设置`x[:, 3, :] = 0`以及`x[:, 5, :] = 0`。
- 在`LSTM`层之前，插入一个`mask_value = 0`的`Masking`层：

``` python
model = Sequential()
model.add(Masking(mask_value=0., input_shape=(timesteps, features)))
model.add(LSTM(32))
```