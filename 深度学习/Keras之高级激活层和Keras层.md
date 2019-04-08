---
title: Keras之高级激活层和Keras层
date: 2019-01-01 09:28:54
categories: 深度学习
---
### 高级激活层

#### LeakyReLU

&emsp;&emsp;`LeakyRelU`是修正线性单元(`Rectified Linear Unit`，`ReLU`)的特殊版本，当不激活时，`LeakyReLU`仍然会有非零输出值，从而获得一个小梯度，避免`ReLU`可能出现的`神经元死亡`现象：

``` python
keras.layers.LeakyReLU(alpha=0.3)
```

参数`alpha`(`float`型，并且`≥ 0`)是负斜率系数。当神经元未激活时，它仍可以赋予其一个很小的梯度：

``` python
f(x) = alpha * x for x < 0
f(x) = x for x >= 0
```

&emsp;&emsp;输入尺寸：可以是任意的。如果将该层作为模型的第一层，则需要指定`input_shape`参数(整数元组，不包含样本数量的维度)。
&emsp;&emsp;输出尺寸：与输入相同。

#### PReLU

&emsp;&emsp;该函数是参数化的修正线性单元：

``` python
keras.layers.PReLU(alpha_initializer='zeros', alpha_regularizer=None, alpha_constraint=None, shared_axes=None)
```

- `alpha_initializer`：权重的初始化函数。
- `alpha_regularizer`：权重的正则化方法。
- `alpha_constraint`：权重的约束。
- `shared_axes`：激活函数共享可学习参数的轴。例如假如输入特征图是从`2D`卷积过来的，具有形如`(batch, height, width, channels)`这样的`shape`；或许你会希望在空域共享参数，这样每个`filter`就只有一组参数，设定`shared_axes = [1, 2]`可完成该目标。

&emsp;&emsp;形式如下所示：

``` python
f(x) = alpha * x for x < 0
f(x) = x for x >= 0
```

其中`alpha`是一个可学习的数组，尺寸与`x`相同。
&emsp;&emsp;输入尺寸：可以是任意的。如果将这一层作为模型的第一层，则需要指定`input_shape`参数(整数元组，不包含样本数量的维度)。
&emsp;&emsp;输出尺寸：与输入相同。

#### ELU

&emsp;&emsp;该函数是指数线性单元：

``` python
keras.layers.ELU(alpha=1.0)
```

参数`alpha`是负因子的尺度。形式如下所示：

``` python
f(x) = alpha * (exp(x) - 1) for x < 0
f(x) = x for x >= 0
```

&emsp;&emsp;输入尺寸：可以是任意的。如果将这一层作为模型的第一层，则需要指定`input_shape`参数(整数元组，不包含样本数量的维度)。
&emsp;&emsp;输出尺寸：与输入相同。

#### ThresholdedReLU

&emsp;&emsp;该函数是带阈值的修正线性单元：

``` python
keras.layers.ThresholdedReLU(theta=1.0)
```

该函数`theta`(float型，并且`≥ 0`)是激活的阈值位。形式如下所示：

``` python
f(x) = x for x > theta, f(x) = 0 otherwise.
```

&emsp;&emsp;输入尺寸：可以是任意的。如果将这一层作为模型的第一层，则需要指定`input_shape`参数(整数元组，不包含样本数量的维度)。
&emsp;&emsp;输出尺寸：与输入相同。

#### Softmax

&emsp;&emsp;该函数是`Softmax`激活函数：

``` python
keras.layers.Softmax(axis=-1)
```

参数`axis`是整数，即应用`softmax`标准化的轴。
&emsp;&emsp;输入尺寸：可以是任意的。如果将这一层作为模型的第一层，则需要指定`input_shape`参数(整数元组，不包含样本数量的维度)。
&emsp;&emsp;输出尺寸：与输入相同。

### Keras层

&emsp;&emsp;所有`Keras`层都有很多共同的函数：

- `layer.get_weights()`：以`Numpy`矩阵的形式返回层的权重。
- `layer.set_weights(weights)`：从`Numpy`矩阵中设置层的权重(与`get_weights`的输出形状相同)。
- `layer.get_config()`：返回包含层配置的字典。此图层可以通过以下方式重置：

``` python
layer = Dense(32)
config = layer.get_config()
reconstructed_layer = Dense.from_config(config)
```

或者：

``` python
from keras import layers

config = layer.get_config()
layer = layers.deserialize({'class_name': layer.__class__.__name__, 'config': config})
```

如果一个层具有单个节点(例如如果它不是共享层)，你可以得到它的输入张量、输出张量、输入尺寸和输出尺寸：

- `layer.input`
- `layer.output`
- `layer.input_shape`
- `layer.output_shape`

如果层有多个节点，您可以使用以下函数：

- `layer.get_input_at(node_index)`
- `layer.get_output_at(node_index)`
- `layer.get_input_shape_at(node_index)`
- `layer.get_output_shape_at(node_index)`