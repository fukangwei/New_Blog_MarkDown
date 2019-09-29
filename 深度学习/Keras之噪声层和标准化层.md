---
title: Keras之噪声层和标准化层
categories: 深度学习
abbrlink: 92c5d8e6
date: 2018-12-31 09:30:48
---
### GaussianNoise

&emsp;&emsp;为数据施加`0`均值，标准差为`stddev`的加性高斯噪声。该层在克服过拟合时比较有用，你可以将它看作是随机的数据提升。高斯噪声是需要对输入数据进行破坏时的自然选择：

``` python
keras.layers.GaussianNoise(stddev)
```

参数`stddev`是`float`型，即噪声分布的标准差。由于它是一个正则化层，因此它只在训练时才被激活。
&emsp;&emsp;输入尺寸：可以是任意的。如果将该层作为模型的第一层，则需要指定`input_shape`参数(整数元组，不包含样本数量的维度)。
&emsp;&emsp;输出尺寸：与输入相同。

### GaussianDropout

&emsp;&emsp;为层的输入施加以`1`为均值，标准差为`sqrt(rate / (1 - rate)`的乘性高斯噪声：

``` python
keras.layers.GaussianDropout(rate)
```

参数`rate`是`float`型，丢弃概率(与`Dropout`相同)。由于它是一个正则化层，因此它只在训练时才被激活。
&emsp;&emsp;输入尺寸：可以是任意的。如果将该层作为模型的第一层，则需要指定`input_shape`参数(整数元组，不包含样本数量的维度)。
&emsp;&emsp;输出尺寸：与输入相同。

### AlphaDropout

&emsp;&emsp;该函数将`Alpha Dropout`应用到输入：

``` python
keras.layers.AlphaDropout(rate, noise_shape=None, seed=None)
```

- `rate`：`float`型，丢弃概率(与`Dropout`相同)。这个乘性噪声的标准差为`sqrt(rate / (1 - rate))`。
- `seed`：用作随机种子的整数。

`Alpha Dropout`是一种`Dropout`，它保持输入的平均值和方差与原来的值不变，作用是在`dropout`之后仍然保证数据的自规范性。通过随机将激活设置为负饱和值，`Alpha Dropout`非常适合按比例缩放的指数线性单元(`SELU`)。
&emsp;&emsp;输入尺寸：可以是任意的。如果将该层作为模型的第一层，则需要指定`input_shape`参数(整数元组，不包含样本数量的维度)。
&emsp;&emsp;输出尺寸：与输入相同。

### BatchNormalization

&emsp;&emsp;该函数是批量标准化层：

``` python
keras.layers.BatchNormalization(
    axis=-1, momentum=0.99, epsilon=0.001, center=True, scale=True, beta_initializer='zeros',
    gamma_initializer='ones', moving_mean_initializer='zeros', moving_variance_initializer='ones',
    beta_regularizer=None, gamma_regularizer=None, beta_constraint=None, gamma_constraint=None)
```

该层在每个`batch`上将前一层的激活值重新规范化，即使得其输出数据的均值接近`0`，其标准差接近`1`。

- `axis`：整数，需要标准化的轴(通常是特征轴)。例如在进行`data_format = 'channels_first'`的`2D`卷积后，一般会设`axis`为`1`。
- `momentum`：动态均值的动量。
- `epsilon`：大于`0`的小浮点数，用于防止除`0`错误。
- `center`：如果为`True`，把`beta`的偏移量加到标准化的张量上；如果为`False`，`beta`被忽略。
- `scale`：如果为`True`，乘以`gamma`；如果为`False`，`gamma`不使用。当下一层为线性层(或者`nn.relu`)，这可以被禁用，因为缩放将由下一层完成。
- `beta_initializer`：`beta`权重的初始化方法。
- `gamma_initializer`：`gamma`权重的初始化方法。
- `moving_mean_initializer`：动态均值的初始化方法。
- `moving_variance_initializer`：动态方差的初始化方法。
- `beta_regularizer`：可选的`beta`权重的正则化方法。
- `gamma_regularizer`：可选的`gamma`权重的正则化方法。
- `beta_constraint`：可选的`beta`权重的约束方法。
- `gamma_constraint`：可选的`gamma`权重的约束方法。

&emsp;&emsp;输入尺寸：可以是任意的。如果将这一层作为模型的第一层，则需要指定`input_shape`参数(整数元组，不包含样本数量的维度)。
&emsp;&emsp;输出尺寸：与输入相同。