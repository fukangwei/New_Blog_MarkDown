---
title: Keras之初始化方法
date: 2019-01-01 13:03:30
categories: 深度学习
---
&emsp;&emsp;初始化方法定义了对`Keras`层设置初始化权重的方法。不同的层可能使用不同的关键字来传递初始化方法，一般来说，指定初始化方法的关键字是`kernel_initializer`和`bias_initializer`：

``` python
model.add(Dense(64, kernel_initializer='random_uniform', bias_initializer='zeros'))
```

一个初始化器可以由字符串指定(必须是下面的预定义初始化器之一)，或一个`callable`的函数：

``` python
from keras import initializers
​
model.add(Dense(64, kernel_initializer=initializers.random_normal(stddev=0.01)))
# also works, it will use the default parameters.
model.add(Dense(64, kernel_initializer='random_normal'))
```

### Initializer

&emsp;&emsp;`Initializer`是所有初始化方法的父类，不能直接使用，如果想要定义自己的初始化方法，请继承此类。

### 预定义初始化方法

#### Zeros

&emsp;&emsp;全`0`初始化：

``` python
keras.initializers.Zeros()
```

#### Ones

&emsp;&emsp;全`1`初始化：

``` python
keras.initializers.Ones()
```

#### Constant

&emsp;&emsp;初始化为固定值`value`：

``` python
keras.initializers.Constant(value=0)
```

#### RandomNormal

&emsp;&emsp;正态分布初始化：

``` python
keras.initializers.RandomNormal(mean=0.0, stddev=0.05, seed=None))
```

参数`mean`是均值，`stddev`是标准差，`seed`是随机数种子。

#### RandomUniform

&emsp;&emsp;均匀分布初始化：

``` python
keras.initializers.RandomUniform(minval=-0.05, maxval=0.05, seed=None)
```

参数`minval`是均匀分布下边界，`maxval`是均匀分布上边界，`seed`是随机数种子。

#### TruncatedNormal

&emsp;&emsp;截尾高斯分布初始化，该初始化方法与`RandomNormal`类似，但位于均值两个标准差以外的数据将会被丢弃并重新生成，形成截尾分布。该分布是神经网络权重和滤波器的推荐初始化方法。

``` python
keras.initializers.TruncatedNormal(mean=0.0, stddev=0.05, seed=None)
```

参数`mean`是均值，`stddev`是标准差，`seed`是随机数种子。

#### VarianceScaling

&emsp;&emsp;该初始化方法能够自适应目标张量的`shape`：

``` python
keras.initializers.VarianceScaling(scale=1.0, mode='fan_in', distribution='normal', seed=None)
```

- `scale`：放缩因子，正浮点数。
- `mode`：字符串，`fan_in`、`fan_out`或`fan_avg`。
- `distribution`：字符串，`normal`或`uniform`。
- `seed`：随机数种子。

当`distribution = "normal"`时，样本从`0`均值，标准差为`sqrt(scale/n)`的截尾正态分布中产生。其中：

- `mode = fan_in`：权重张量的输入单元数。
- `mode = fan_out`：权重张量的输出单元数。
- `mode = fan_avg`：权重张量的输入输出单元数的均值。

当`distribution = "uniform"`时，权重从`[-limit, limit]`范围内均匀采样，其中`limit = limit = sqrt(3 * scale / n)`。

#### Orthogonal

&emsp;&emsp;用随机正交矩阵初始化：

``` python
keras.initializers.Orthogonal(gain=1.0, seed=None)
```

参数`gain`是正交矩阵的乘性系数，`seed`是随机数种子。

#### Identiy

&emsp;&emsp;使用单位矩阵初始化，仅适用于`2D`方阵：

``` python
keras.initializers.Identity(gain=1.0)
```

参数`gain`是单位矩阵的乘性系数。

#### lecun_uniform

&emsp;&emsp;`LeCun`均匀分布初始化方法，参数由`[-limit, limit]`的区间中均匀采样获得，其中`limit = sqrt(3 / fan_in)`，`fin_in`是权重向量的输入单元数：

``` python
lecun_uniform(seed=None)
```

参数`seed`是随机数种子。

#### lecun_normal

&emsp;&emsp;`LeCun`正态分布初始化方法，参数由`0`均值，标准差为`stddev = sqrt(1 / fan_in)`的正态分布产生，`fin_in`是权重向量的输入单元数：

``` python
lecun_normal(seed=None)
```

参数`seed`是随机数种子。

#### glorot_normal

&emsp;&emsp;`Glorot`正态分布初始化方法，也称作`Xavier`正态分布初始化，参数由`0`均值，标准差为`sqrt(2 / (fan_in + fan_out))`的正态分布产生，其中`fan_in`和`fan_out`是权重张量的输入和输出单元数目：

``` python
glorot_normal(seed=None)
```

参数`seed`是随机数种子。

#### glorot_uniform

&emsp;&emsp;`Glorot`均匀分布初始化方法，又称为`Xavier`均匀初始化，参数从`[-limit, limit]`的均匀分布产生，其中`limit = sqrt(6 / (fan_in + fan_out))`，`fan_in`为权值张量的输入单元数，`fan_out`是权重张量的输出单元数：

``` python
glorot_uniform(seed=None)
```

参数`seed`是随机数种子。

#### he_normal

&emsp;&emsp;`He`正态分布初始化方法，参数由`0`均值，标准差为`sqrt(2 / fan_in)`的正态分布产生，其中`fan_in`是权重张量的输入单元数：

``` python
he_normal(seed=None)
```

参数`seed`是随机数种子。

#### he_uniform

&emsp;&emsp;`He`均匀分布初始化方法，参数由`[-limit, limit]`的区间中均匀采样获得，其中`limit = sqrt(6 / fan_in)`，`fin_in`是权重向量的输入单元数：

``` python
he_normal(seed=None)
```

参数`seed`是随机数种子。

#### 自定义初始化器

&emsp;&emsp;如果需要传递自定义的初始化器，则该初始化器必须是`callable`的，并且接收`shape`(将被初始化的张量`shape`)和`dtype`(数据类型)两个参数，并返回符合`shape`和`dtype`的张量：

``` python
from keras import backend as K
​
def my_init(shape, dtype=None):
    return K.random_normal(shape, dtype=dtype)
​
model.add(Dense(64, init=my_init))
```