---
title: Keras之损失和评价函数
categories: 深度学习
abbrlink: 2f875a36
date: 2019-01-01 19:48:08
---
### 损失函数

&emsp;&emsp;损失函数(或称`目标函数`、`优化评分函数`)是编译模型时所需的两个参数之一：<!--more-->

``` python
model.compile(loss='mean_squared_error', optimizer='sgd')
# 或者
from keras import losses
model.compile(loss=losses.mean_squared_error, optimizer='sgd')
```

你可以传递一个现有的损失函数名，或者一个`TensorFlow/Theano`符号函数。该符号函数为每个数据点返回一个标量，有以下两个参数：

- `y_true`：真实的数据标签，`TensorFlow/Theano`张量。
- `y_pred`：预测值，`TensorFlow/Theano`张量，其`shape`与`y_true`相同。

实际的优化目标是所有数据点的输出数组的平均值。

### 可用损失函数

#### mean_squared_error

&emsp;&emsp;函数原型如下：

``` python
mean_squared_error(y_true, y_pred)
```

#### mean_absolute_error

&emsp;&emsp;函数原型如下：

``` python
mean_absolute_error(y_true, y_pred)
```

#### mean_absolute_percentage_error

&emsp;&emsp;函数原型如下：

``` python
mean_absolute_percentage_error(y_true, y_pred)
```

#### mean_squared_logarithmic_error

&emsp;&emsp;函数原型如下：

``` python
mean_squared_logarithmic_error(y_true, y_pred)
```

#### squared_hinge

&emsp;&emsp;函数原型如下：

``` python
squared_hinge(y_true, y_pred)
```

#### hinge

&emsp;&emsp;函数原型如下：

``` python
hinge(y_true, y_pred)
```

#### categorical_hinge

&emsp;&emsp;函数原型如下：

``` python
categorical_hinge(y_true, y_pred)
```

#### logcosh

&emsp;&emsp;函数原型如下：

``` python
logcosh(y_true, y_pred)
```

该函数用于预测误差的双曲余弦的对数。参数`y_true`是目标真实值的张量，`y_pred`是目标预测值的张量。
&emsp;&emsp;对于小的`x`，`log(cosh(x))`近似等于`(x ** 2) / 2`；对于大的`x`，近似等于`abs(x) - log(2)`。这表示`logcosh`与均方误差大致相同，但是不会受到偶尔疯狂的错误预测的强烈影响。

#### categorical_crossentropy

&emsp;&emsp;函数原型如下：

``` python
categorical_crossentropy(y_true, y_pred)
```

也称作`多类的对数损失`，注意使用该目标函数时，需要将标签转化为形如`(nb_samples, nb_classes)`的二值序列。
&emsp;&emsp;当使用`categorical_crossentropy`计算损失时，你的目标值应该是分类格式(即如果你有`10`个类，每个样本的目标值应该是一个`10`维的向量，这个向量除了表示类别的那个索引为`1`，其他均为`0`)。为了将整数目标值转换为分类目标值，你可以使用`Keras`实用函数`to_categorical`：

``` python
from keras.utils.np_utils import to_categorical
categorical_labels = to_categorical(int_labels, num_classes=None)
```

#### sparse_categorical_crossentropy

&emsp;&emsp;函数原型如下：

``` python
sparse_categorical_crossentropy(y_true, y_pred)
```

#### binary_crossentropy

&emsp;&emsp;函数原型如下：

``` python
binary_crossentropy(y_true, y_pred)
```

#### kullback_leibler_divergence

&emsp;&emsp;函数原型如下：

``` python
kullback_leibler_divergence(y_true, y_pred)
```

从预测值概率分布`Q`到真值概率分布`P`的信息增益，用以度量两个分布的差异。

#### poisson

&emsp;&emsp;函数原型如下：

``` python
poisson(y_true, y_pred)
```

#### cosine_proximity

&emsp;&emsp;函数原型如下：

``` python
cosine_proximity(y_true, y_pred)
```

预测值与真实标签的余弦距离平均值的相反数。

### Keras自定义loss函数

&emsp;&emsp;在自定义`loss`函数之前，可以看看`Keras`官方是如何定义`loss`函数的。进入文件`keras/keras/losses.py`，可以看到很多`Keras`自带`loss`的实现代码，比如最简单的均方误差损失函数：

``` python
def mean_squared_error(y_true, y_pred):
    return K.mean(K.square(y_pred - y_true), axis=-1)
```

其中`y_true`为网络给出的预测值，`y_true`即是标签，两者均为`tensor`。在`loss`中直接操作这两个变量即可实现自己想要的`loss`函数，例如将其改为四次方的平均值来作为新的`loss`：

``` python
def mean_squared_error2(y_true, y_pred):
    return K.mean(K.square(K.square(y_pred - y_true)), axis=-1)
```

在`model`编译阶段将`loss`指定为自定义的函数：

``` PYTHON
model.compile(optimizer='rmsprop', loss=mean_squared_error2)
```

---

### 评价函数

&emsp;&emsp;评价函数用于评估当前训练模型的性能。当模型编译后，评价函数应该作为`metrics`的参数来输入：

``` python
model.compile(loss='mean_squared_error', optimizer='sgd', metrics=['mae', 'acc'])​
# 或者
from keras import metrics
model.compile(
    loss='mean_squared_error', optimizer='sgd',
    metrics=[metrics.mae, metrics.categorical_accuracy])
```

评价函数和损失函数相似，只不过评价函数的结果不会用于训练过程中。我们可以传递已有的评价函数名称，或者传递一个自定义的`Theano/TensorFlow`函数来使用：

- `y_true`：真实标签，`Theano/Tensorflow`张量。
- `y_pred`：预测值，和`y_true`相同尺寸的`Theano/TensorFlow`张量。

返回一个表示全部数据点平均值的张量。

### 可使用的评价函数

#### binary_accuracy

&emsp;&emsp;函数原型如下：

``` python
binary_accuracy(y_true, y_pred)
```

对二分类问题，计算在所有预测值上的平均正确率。

#### categorical_accuracy

&emsp;&emsp;函数原型如下：

``` python
categorical_accuracy(y_true, y_pred)
```

对多分类问题，计算在所有预测值上的平均正确率。

#### sparse_categorical_accuracy

&emsp;&emsp;函数原型如下：

``` python
sparse_categorical_accuracy(y_true, y_pred)
```

与`categorical_accuracy`相同，在对稀疏的目标值预测时有用。

#### top_k_categorical_accuracy

&emsp;&emsp;函数原型如下：

``` python
top_k_categorical_accuracy(y_true, y_pred, k=5)
```

计算`top-k`正确率，当预测值的前`k`个值中存在目标类别即认为预测正确。

#### sparse_top_k_categorical_accuracy

&emsp;&emsp;函数原型如下：

``` python
sparse_top_k_categorical_accuracy(y_true, y_pred, k=5)
```

与`top_k_categorical_accracy`作用相同，但适用于稀疏情况。

### 自定义评价函数

&emsp;&emsp;自定义评价函数应该在编译时传递进去，该函数需要以`(y_true, y_pred)`作为输入参数，并返回一个张量：

``` python
import keras.backend as K
​
def mean_pred(y_true, y_pred):
    return K.mean(y_pred)
​
model.compile(optimizer='rmsprop', loss='binary_crossentropy', metrics=['accuracy', mean_pred])
```