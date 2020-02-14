---
title: Keras之融合层
categories: 深度学习
abbrlink: 17bb1dc4
date: 2019-01-01 15:23:32
---
&emsp;&emsp;`Merge`层提供了一系列用于融合两个层或两个张量的层对象和方法。以大写首字母开头的是`Layer`类，以小写字母开头的是张量的函数。小写字母开头的张量函数在内部实际上是调用了大写字母开头的层。<!--more-->

### Add

&emsp;&emsp;该函数计算一个列表的输入张量的和：

``` python
keras.layers.Add()
```

相加层接受一个列表的张量，所有的张量必须有相同的输入尺寸，然后返回一个张量(和输入张量尺寸相同)。

``` python
import keras
​
input1 = keras.layers.Input(shape=(16,))
x1 = keras.layers.Dense(8, activation='relu')(input1)
input2 = keras.layers.Input(shape=(32,))
x2 = keras.layers.Dense(8, activation='relu')(input2)
# 相当于“added = keras.layers.add([x1, x2])”
added = keras.layers.Add()([x1, x2])
out = keras.layers.Dense(4)(added)
model = keras.models.Model(inputs=[input1, input2], outputs=out)
```

### Subtract

&emsp;&emsp;该函数计算两个输入张量的差：

``` python
keras.layers.Subtract()
```

相减层接受一个长度为`2`的张量列表，两个张量必须有相同的尺寸，然后返回一个值为(`inputs[0] - inputs[1]`)的张量，输出张量和输入张量尺寸相同。

``` python
import keras
​
input1 = keras.layers.Input(shape=(16,))
x1 = keras.layers.Dense(8, activation='relu')(input1)
input2 = keras.layers.Input(shape=(32,))
x2 = keras.layers.Dense(8, activation='relu')(input2)
# 相当于“subtracted = keras.layers.subtract([x1, x2])”
subtracted = keras.layers.Subtract()([x1, x2])
out = keras.layers.Dense(4)(subtracted)
model = keras.models.Model(inputs=[input1, input2], outputs=out)
```

### Multiply

&emsp;&emsp;该函数计算一个列表的输入张量的(逐元素间的)乘积：

``` python
keras.layers.Multiply()
```

相乘层接受一个列表的张量，所有的张量必须有相同的输入尺寸，然后返回一个张量(和输入张量尺寸相同)。

### Average

&emsp;&emsp;该函数计算一个列表的输入张量的平均值：

``` python
keras.layers.Average()
```

平均层接受一个列表的张量，所有的张量必须有相同的输入尺寸，然后返回一个张量(和输入张量尺寸相同)。

### Maximum

&emsp;&emsp;该函数计算一个列表的输入张量的(逐元素间的)最大值：

``` python
keras.layers.Maximum()
```

最大层接受一个列表的张量，所有的张量必须有相同的输入尺寸，然后返回一个张量(和输入张量尺寸相同)。

### Concatenate

&emsp;&emsp;该函数串联一个列表的输入张量：

``` python
keras.layers.Concatenate(axis=-1)
```

串联层接受一个列表的张量(除了串联轴之外，其他的尺寸都必须相同)，然后返回一个由所有输入张量串联起来的输出张量。参数`axis`是串联的轴。

### Dot

&emsp;&emsp;该函数计算两个张量之间样本的点积：

``` python
keras.layers.Dot(axes, normalize=False)
```

例如，如果作用于输入尺寸为`(batch_size, n)`的两个张量`a`和`b`，那么输出结果就会是尺寸为`(batch_size, 1)`的一个张量。结果张量每个`batch`的数据都是`a[i,:]`和`b[i,:]`的矩阵(向量)点积。

- `axes`：整数或者整数元组，一个或者几个进行点积的轴。
- `normalize`：是否在点积之前对即将进行点积的轴进行`L2`标准化。如果设置成`True`，那么输出两个样本之间的余弦相似值。

### add

&emsp;&emsp;该函数`Add`层的函数式接口：

``` python
keras.layers.add(inputs)
```

参数`inputs`是一个列表的输入张量(列表大小至少为`2`)。该函数返回一个张量，即所有输入张量的和。

``` python
import keras
​
input1 = keras.layers.Input(shape=(16,))
x1 = keras.layers.Dense(8, activation='relu')(input1)
input2 = keras.layers.Input(shape=(32,))
x2 = keras.layers.Dense(8, activation='relu')(input2)
added = keras.layers.add([x1, x2])
out = keras.layers.Dense(4)(added)
model = keras.models.Model(inputs=[input1, input2], outputs=out)
```

### subtract

&emsp;&emsp;该函数是`Subtract`层的函数式接口：

``` python
keras.layers.subtract(inputs)
```

参数`inputs`是一个列表的输入张量(列表大小准确为`2`)。该函数返回一个张量，即两个输入张量的差。

``` python
import keras
​
input1 = keras.layers.Input(shape=(16,))
x1 = keras.layers.Dense(8, activation='relu')(input1)
input2 = keras.layers.Input(shape=(32,))
x2 = keras.layers.Dense(8, activation='relu')(input2)
subtracted = keras.layers.subtract([x1, x2])
out = keras.layers.Dense(4)(subtracted)
model = keras.models.Model(inputs=[input1, input2], outputs=out)
```

### multiply

&emsp;&emsp;该函数是`Multiply`层的函数式接口：

``` python
keras.layers.multiply(inputs)
```

参数`inputs`是一个列表的输入张量(列表大小至少为`2`)。该函数返回一个张量，即所有输入张量的逐元素乘积。

### average

&emsp;&emsp;该函数是`Average`层的函数式接口：

``` python
keras.layers.average(inputs)
```

参数`inputs`是一个列表的输入张量(列表大小至少为`2`)。该函数返回一个张量，即所有输入张量的平均值。

### maximum

&emsp;&emsp;该函数是`Maximum`层的函数式接口：

``` python
keras.layers.maximum(inputs)
```

参数`inputs`是一个列表的输入张量(列表大小至少为`2`)。该函数返回一个张量，所有张量的逐元素的最大值。

### concatenate

&emsp;&emsp;该函数是`Concatenate`层的函数式接口：

``` python
keras.layers.concatenate(inputs, axis=-1)
```

参数`inputs`是一个列表的输入张量(列表大小至少为`2`)，`axis`是串联的轴。该函数返回一个张量，即所有输入张量通过`axis`轴串联起来的输出张量。

### dot

&emsp;&emsp;该函数是`Dot`层的函数式接口：

``` python
keras.layers.dot(inputs, axes, normalize=False)
```

- `inputs`：一个列表的输入张量(列表大小至少为`2`)。
- `axes`：整数或者整数元组，一个或者几个进行点积的轴。
- `normalize`：是否在点积之前对即将进行点积的轴进行`L2`标准化。如果设置成`True`，那么输出两个样本之间的余弦相似值。

该函数返回一个张量，即所有输入张量样本之间的点积。