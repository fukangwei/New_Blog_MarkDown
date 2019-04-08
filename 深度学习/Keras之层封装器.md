---
title: Keras之层封装器
date: 2018-12-30 21:20:08
categories: 深度学习
---
### TimeDistributed

&emsp;&emsp;该包装器可以把一个层应用到输入的每一个时间步上：

``` python
keras.layers.TimeDistributed(layer)
```

参数`layer`是一个网络层实例。输入至少为`3D`，且第一个维度应该是时间所表示的维度。
&emsp;&emsp;考虑`32`个样本的一个`batch`，其中每个样本是`10`个`16`维向量的序列。那么这个`batch`的输入尺寸为`(32, 10, 16)`，而`input_shape`不包含样本数量的维度，为`(10, 16)`。你可以使用`TimeDistributed`来将`Dense`层独立地应用到这`10`个时间步的每一个：

``` python
model = Sequential()
# 现在“model.output_shape == (None, 10, 8)”
model.add(TimeDistributed(Dense(8), input_shape=(10, 16)))
```

输出的尺寸为`(32, 10, 8)`。在后续的层中，将不再需要`input_shape`：

``` python
# 现在“model.output_shape == (None, 10, 32)”
model.add(TimeDistributed(Dense(32)))
```

输出的尺寸为`(32, 10, 32)`。
&emsp;&emsp;使用`TimeDistributed`包装`Dense`严格等价于`layers.TimeDistribuedDense`。不同的是，包装器`TimeDistribued`还可以对别的层进行包装，如这里对`Convolution2D`包装：

``` python
model = Sequential()
model.add(TimeDistributed(Conv2D(64, (3, 3)), input_shape=(10, 299, 299, 3)))
```

### Bidirectional

&emsp;&emsp;该函数是`RNN`的双向封装器，对序列进行前向和后向计算：

``` python
keras.layers.Bidirectional(layer, merge_mode='concat', weights=None)
```

- `layer`：`Recurrent`实例。
- `merge_mode`：前向和后向`RNN`的输出的结合模式，为`sum`、`mul`、`concat`、`ave`、`None`其中之一。如果是`None`，输出不会被结合，而是作为一个列表被返回。

``` python
model = Sequential()
model.add(Bidirectional(LSTM(10, return_sequences=True), input_shape=(5, 10)))
model.add(Bidirectional(LSTM(10)))
model.add(Dense(5))
model.add(Activation('softmax'))
model.compile(loss='categorical_crossentropy', optimizer='rmsprop')
```