---
title: Keras之优化器
categories: 深度学习
abbrlink: ec0a05c1
date: 2019-01-01 10:04:12
---
&emsp;&emsp;优化器(`optimizer`)是编译`Keras`模型的所需的两个参数之一：<!--more-->

``` python
from keras import optimizers
​
model = Sequential()
model.add(Dense(64, kernel_initializer='uniform', input_shape=(10,)))
model.add(Activation('tanh'))
model.add(Activation('softmax'))
​
sgd = optimizers.SGD(lr=0.01, decay=1e-6, momentum=0.9, nesterov=True)
model.compile(loss='mean_squared_error', optimizer=sgd)
```

你可以先实例化一个优化器对象，然后将它传入`model.compile`，像上述示例中一样；或者你可以通过名称来调用优化器。在后一种情况下，将使用优化器的默认参数：

``` python
# 传入优化器名称，默认参数将被采用
model.compile(loss='mean_squared_error', optimizer='sgd')
```

### Keras优化器的公共参数

&emsp;&emsp;参数`clipnorm`和`clipvalue`能在所有的优化器中使用，用于控制梯度裁剪(`Gradient Clipping`)：

``` python
from keras import optimizers
# 所有参数梯度将被裁剪，让其l2范数最大为1：“g * 1 / max(1, l2_norm)”
sgd = optimizers.SGD(lr=0.01, clipnorm=1.)
# --------------------------
from keras import optimizers
# 所有参数d梯度将被裁剪到数值范围内：最大值0.5，最小值“-0.5”
sgd = optimizers.SGD(lr=0.01, clipvalue=0.5)
```

### SGD

&emsp;&emsp;该函数是随机梯度下降优化器：

``` python
keras.optimizers.SGD(lr=0.01, momentum=0.0, decay=0.0, nesterov=False)
```

包含扩展功能的支持：动量(`momentum`)优化、学习率衰减(每次参数更新后)和`Nestrov`动量(`NAG`)优化。

- `lr`：`float >= 0`，学习率。
- `momentum`：`float >= 0`，用于加速`SGD`在相关方向上前进，并抑制震荡。
- `decay`：`float >= 0`，每次参数更新后学习率衰减值。
- `nesterov`：`boolean`型，是否使用`Nesterov`动量。

### RMSprop

&emsp;&emsp;该函数是`RMSProp`优化器：

``` python
keras.optimizers.RMSprop(lr=0.001, rho=0.9, epsilon=None, decay=0.0)
```

建议使用优化器的默认参数(除了学习率`lr`，它可以被自由调节)，这个优化器通常是训练循环神经网络`RNN`的不错选择。

- `lr`：`float >= 0`，学习率。
- `rho`：`float >= 0`，`RMSProp`梯度平方的移动均值的衰减率。
- `epsilon`：`float >= 0`，模糊因子。若为`None`，默认为`K.epsilon`。
- `decay`：`float >= 0`，每次参数更新后学习率衰减值。

### Adagrad

&emsp;&emsp;该函数是`Adagrad`优化器：

``` python
keras.optimizers.Adagrad(lr=0.01, epsilon=None, decay=0.0)
```

- `lr`：`float >= 0`，学习率。
- `epsilon`：`float >= 0`，若为`None`，默认为`K.epsilon`。
- `decay`：`float >= 0`，每次参数更新后学习率衰减值。

### Adadelta

&emsp;&emsp;该函数是`Adagrad`优化器：

``` python
keras.optimizers.Adadelta(lr=1.0, rho=0.95, epsilon=None, decay=0.0)
```

- `lr`：`float >= 0`，学习率，建议保留默认值。
- `rho`：`float >= 0`，`Adadelta`梯度平方移动均值的衰减率。
- `epsilon`：`float >= 0`，模糊因子。若为`None`，默认为`K.epsilon`。
- `decay`：`float >= 0`，每次参数更新后学习率衰减值。

### Adam

&emsp;&emsp;该函数是`Adam`优化器：

``` python
keras.optimizers.Adam(lr=0.001, beta_1=0.9, beta_2=0.999, epsilon=None, decay=0.0, amsgrad=False)
```

- `lr`：`float >= 0`，学习率。
- `beta_1`：`float`型，`0 < beta < 1`，通常接近于`1`。
- `beta_2`：`float`型，`0 < beta < 1`，通常接近于`1`。
- `epsilon`：`float >= 0`，模糊因子。若为`None`，默认为`K.epsilon`。
- `decay`：`float >= 0`，每次参数更新后学习率衰减值。
- `amsgrad`：`boolean`型，是否应用此算法的`AMSGrad`变种，来自论文`On the Convergence of Adam and Beyond`。

### Adamax

&emsp;&emsp;该函数是`Adamax`优化器，来自`Adam`论文(`Adam - A Method for Stochastic Optimization`)的第七小节：

``` python
keras.optimizers.Adamax(lr=0.002, beta_1=0.9, beta_2=0.999, epsilon=None, decay=0.0)
```

它是`Adam`算法基于无穷范数(`infinity norm`)的变种。

- `lr`：`float >= 0`，学习率。
- `beta_1/beta_2`：`float`型，`0 < beta < 1`，通常接近于1。
- `epsilon`：`float >= 0`，模糊因子。若为`None`，默认为`K.epsilon`。
- `decay`：`float >= 0`，每次参数更新后学习率衰减值。

### Nadam

&emsp;&emsp;该函数是`Nesterov`版本`Adam`优化器：

``` python
keras.optimizers.Nadam(lr=0.002, beta_1=0.9, beta_2=0.999, epsilon=None, schedule_decay=0.004)
```

正像`Adam`本质上是`RMSProp`与动量`momentum`的结合，`Nadam`是采用`Nesterov momentum`版本的`Adam`优化器。

- `lr`：`float >= 0`，学习率。
- `beta_1/beta_2`：`float`型，`0 < beta < 1`，通常接近于`1`。
- `epsilon`：`float >= 0`，模糊因子。若为`None`，默认为`K.epsilon`。

### TFOptimizer

&emsp;&emsp;该函数是原生`TensorFlow`优化器的包装类(`wrapper class`)：

``` python
keras.optimizers.TFOptimizer(optimizer)
```