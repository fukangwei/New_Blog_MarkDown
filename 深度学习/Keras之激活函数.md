---
title: Keras之激活函数
categories: 深度学习
abbrlink: a0cec28e
date: 2018-12-30 20:52:21
---
&emsp;&emsp;激活函数可以通过设置单独的激活层实现，也可以在构造层对象时通过传递`activation`参数实现：<!--more-->

``` python
from keras.layers import Activation, Dense

model.add(Dense(64))
model.add(Activation('tanh'))
```

等价于：

``` python
model.add(Dense(64, activation='tanh'))
```

你也可以通过传递一个逐元素运算的`Theano/TensorFlow/CNTK`函数来作为激活函数：

``` python
from keras import backend as K

model.add(Dense(64, activation=K.tanh))
model.add(Activation(K.tanh))
```

### 预定义激活函数

#### softmax

&emsp;&emsp;该函数是`Softmax`激活函数：

``` python
softmax(x, axis=-1)
```

参数`x`是张量，`axis`是整数，代表`softmax`所作用的维度。该函数返回`softmax`变换后的张量。

#### elu

&emsp;&emsp;函数原型如下：

``` python
elu(x, alpha=1.0)
```

#### selu

&emsp;&emsp;该函数是可伸缩的指数线性单元：

``` python
selu(x)
```

参数`x`是一个用来用于计算激活函数的张量或变量。该函数返回与`x`具有相同类型及形状的张量。
&emsp;&emsp;**Note**：与`lecun_normal`初始化方法一起使用；与`dropout`的变种`AlphaDropout`一起使用。

#### softplus

&emsp;&emsp;函数原型如下：

``` python
softplus(x)
```

#### softsign

&emsp;&emsp;函数原型如下：

``` python
softsign(x)
```

#### relu

&emsp;&emsp;函数原型如下：

``` python
relu(x, alpha=0.0, max_value=None)
```

#### tanh

&emsp;&emsp;函数原型如下：

``` python
tanh(x)
```

#### sigmoid

&emsp;&emsp;函数原型如下：

``` python
sigmoid(x)
```

#### hard_sigmoid

&emsp;&emsp;函数原型如下：

``` python
hard_sigmoid(x)
```

#### linear

&emsp;&emsp;函数原型如下：

``` python
linear(x)
```

### 高级激活函数

&emsp;&emsp;对于`Theano/TensorFlow/CNTK`不能表达的复杂激活函数，例如含有可学习参数的激活函数，可通过高级激活函数实现，例如`PReLU`、`LeakyReLU`。