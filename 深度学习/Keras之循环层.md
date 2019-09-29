---
title: Keras之循环层
categories: 深度学习
abbrlink: ba6580b8
date: 2019-01-15 17:19:11
---
### RNN

&emsp;&emsp;该函数是循环神经网络层基类，请使用它的子类`LSTM`、`GRU`或`SimpleRNN`：

``` python
keras.layers.recurrent.Recurrent(
    return_sequences=False, go_backwards=False,
    stateful=False, unroll=False, implementation=0)
```

所有的循环层(`LSTM`、`GRU`和`SimpleRNN`)都继承本层，因此下面的参数可以在任何循环层中使用：

- `return_sequences`：布尔值，决定是返回输出序列中的最后一个输出，还是全部序列(`True`)。
- `go_backwards`：布尔值，如果为`True`，则逆向处理输入序列，并返回逆序后的序列。
- `stateful`：布尔值，如果为`True`，则一个`batch`中下标为`i`的样本的最终状态将会用作下一个`batch`同样下标的样本的初始状态。
- `unroll`：布尔值，如果为`True`，则网络将展开，否则就使用符号化的循环。当使用`TensorFlow`为后端时，循环网络本来就是展开的，因此该层不做任何事情。层展开会占用更多的内存，但会加速`RNN`的运算。层展开只适用于短序列。
- `implementation`：`0`、`1`或`2`。若为`0`，则`RNN`将以更少但是更大的矩阵乘法实现，因此在`CPU`上运行更快，但消耗更多的内存。如果设为`1`，则`RNN`将以更多但更小的矩阵乘法实现，因此在`CPU`上运行更慢，在`GPU`上运行更快，并且消耗更少的内存。如果设为`2`(仅`LSTM`和`GRU`可以设为`2`)，则`RNN`将把输入门、遗忘门和输出门合并为单个矩阵，以获得更加在`GPU`上更加高效的实现。注意，`RNN dropout`必须在所有门上共享，并导致正则效果性能微弱降低。
- `input_dim`：输入的维度(整数)，将此层用作模型中的第一层时，此参数(或者等价的指定`input_shape`)是必需的。
- `input_length`：当输入序列的长度固定时，该参数为输入序列的长度。当需要在该层后连接`Flatten`层，然后又要连接`Dense`层时，需要指定该参数，否则全连接的输出无法计算出来。注意，如果循环层不是网络的第一层，你需要在网络的第一层中指定序列的长度(通过`input_shape`指定)。

&emsp;&emsp;输入尺寸：`3D`张量，尺寸为(`batch_size, timesteps, input_dim`)。
&emsp;&emsp;输出尺寸：如果`return_state`为`True`，则返回张量列表。第一个张量为输出，剩余的张量为最后的状态，每个张量的尺寸为(`batch_size, units`)；否则返回尺寸为(`batch_size, units`)的`2D`张量。

``` python
model = Sequential()
# now model.output_shape == (None, 32)
model.add(LSTM(32, input_shape=(10, 64)))
​
# the following is identical
model = Sequential()
model.add(LSTM(32, input_dim=64, input_length=10))
# for subsequent layers, no need to specify the input size
model.add(LSTM(16))
# to stack recurrent layers, you must use return_sequences=True
# on any recurrent layer that feeds into another recurrent layer.
# note that you only need to specify the input size on the first layer.
model = Sequential()
model.add(LSTM(64, input_dim=64, input_length=10, return_sequences=True))
model.add(LSTM(32, return_sequences=True))
model.add(LSTM(10))
```

#### 屏蔽覆盖

&emsp;&emsp;循环层支持通过时间步变量对输入数据进行`Masking`，如果想将输入数据的一部分屏蔽掉，请使用`Embedding`层并将参数`mask_zero`设为`True`。

#### 关于在RNN中使用状态的注意事项

&emsp;&emsp;你可以将`RNN`层设置为`stateful`(有状态的)，这意味着针对一批中的样本计算的状态将被重新用作下一批样品的初始状态。这假定在不同连续批次的样品之间有一对一的映射。
&emsp;&emsp;为了启用状态`RNN`，请在实例化层对象时指定参数`stateful = True`，并在`Sequential`模型使用固定大小的`batch`：通过在模型的第一层传入`batch_size = (...)`和`input_shape`来实现。在函数式模型中，对所有的输入都要指定相同的`batch_size`。
&emsp;&emsp;如果要将循环层的状态重置，请调用`reset_states`。对模型调用将重置模型中所有状态`RNN`的状态；对单个层调用则只重置该层的状态。

#### 关于指定RNN初始状态的注意事项

&emsp;&emsp;可以通过设置`initial_state`用符号式的方式指定`RNN`层的初始状态，`initial_stat`的值应该为一个`tensor`或一个`tensor`列表，代表`RNN`层的初始状态。
&emsp;&emsp;也可以通过设置`reset_states`参数用数值的方法设置`RNN`的初始状态，状态的值应该为`numpy`数组或`numpy`数组的列表，代表`RNN`层的初始状态。

### SimpleRNN

&emsp;&emsp;该函数是完全连接的`RNN`，其输出将被反馈到输入：

``` python
keras.layers.SimpleRNN(
    units, activation='tanh', use_bias=True, kernel_initializer='glorot_uniform',
    recurrent_initializer='orthogonal', bias_initializer='zeros',
    kernel_regularizer=None, recurrent_regularizer=None, bias_regularizer=None,
    activity_regularizer=None, kernel_constraint=None, recurrent_constraint=None,
    bias_constraint=None, dropout=0.0, recurrent_dropout=0.0, return_sequences=False,
    return_state=False, go_backwards=False, stateful=False, unroll=False)
```

- `units`：正整数，输出空间的维度。
- `activation`：要使用的激活函数。如果传入`None`，则不使用激活函数(即线性激活`a(x) = x`)。
- `use_bias`：布尔值，该层是否使用偏置向量。
- `kernel_initializer`：`kernel`权值矩阵的初始化器，用于输入的线性转换。
- `recurrent_initializer`：`recurrent_kernel`权值矩阵的初始化器，用于循环层状态的线性转换。
- `bias_initializer`：偏置向量的初始化器。
- `kernel_regularizer`：运用到`kernel`权值矩阵的正则化函数。
- `recurrent_regularizer`：运用到`recurrent_kernel`权值矩阵的正则化函数。
- `bias_regularizer`：运用到偏置向量的正则化函数。
- `activity_regularizer`：运用到层输出的正则化函数。
- `kernel_constraint`：运用到`kernel`权值矩阵的约束函数。
- `recurrent_constraint`：运用到`recurrent_kernel`权值矩阵的约束函数。
- `bias_constraint`：运用到偏置向量的约束函数。
- `dropout`：在`0`和`1`之间的浮点数。单元的丢弃比例，用于输入的线性转换。
- `recurrent_dropout`：在`0`和`1`之间的浮点数，控制输入线性变换的神经元断开比例。

### GRU

&emsp;&emsp;该函数是门限循环单元网络：

``` python
keras.layers.GRU(
    units, activation='tanh', recurrent_activation='hard_sigmoid', use_bias=True,
    kernel_initializer='glorot_uniform', recurrent_initializer='orthogonal',
    bias_initializer='zeros', kernel_regularizer=None, recurrent_regularizer=None,
    bias_regularizer=None, activity_regularizer=None, kernel_constraint=None,
    recurrent_constraint=None, bias_constraint=None, dropout=0.0, recurrent_dropout=0.0,
    implementation=1, return_sequences=False, return_state=False,
    go_backwards=False, stateful=False, unroll=False)
```

### LSTM

&emsp;&emsp;该函数是长短期记忆网络层：

``` python
keras.layers.LSTM(
    units, activation='tanh', recurrent_activation='hard_sigmoid', use_bias=True,
    kernel_initializer='glorot_uniform', recurrent_initializer='orthogonal',
    bias_initializer='zeros', unit_forget_bias=True, kernel_regularizer=None,
    recurrent_regularizer=None, bias_regularizer=None, activity_regularizer=None,
    kernel_constraint=None, recurrent_constraint=None, bias_constraint=None,
    dropout=0.0, recurrent_dropout=0.0, implementation=1, return_sequences=False,
    return_state=False, go_backwards=False, stateful=False, unroll=False)
```

### ConvLSTM2D

&emsp;&emsp;该函数是卷积`LSTM`：

``` python
keras.layers.ConvLSTM2D(
    filters, kernel_size, strides=(1, 1), padding='valid', data_format=None,
    dilation_rate=(1, 1), activation='tanh', recurrent_activation='hard_sigmoid',
    use_bias=True, kernel_initializer='glorot_uniform', recurrent_initializer='orthogonal',
    bias_initializer='zeros', unit_forget_bias=True, kernel_regularizer=None,
    recurrent_regularizer=None, bias_regularizer=None, activity_regularizer=None,
    kernel_constraint=None, recurrent_constraint=None, bias_constraint=None,
    return_sequences=False, go_backwards=False, stateful=False,
    dropout=0.0, recurrent_dropout=0.0)
```

它类似于`LSTM`层，但输入变换和循环变换都是卷积的。

- `filters`：整数，输出空间的维度(即卷积中滤波器的输出数量)。
- `kernel_size`：一个整数，或者单个整数表示的元组或列表，指明`1D`卷积窗口的长度。
- `strides`：一个整数，或者单个整数表示的元组或列表，指明卷积的步长。当不等于`1`时，无法使用`dilation`功能，即`dialation_rate`必须为`1`。
- `padding`：`valid`或`same`之一。
- `data_format`：字符串，`channels_last`(默认)或`channels_first`之一，代表图像的通道维的位置。`channels_last`对应输入尺寸为(`batch, height, width, channels`)，`channels_first`对应输入尺寸为(`batch, channels, height, width`)。
- `dilation_rate`：一个整数，或`n`个整数的元组/列表，指定用于膨胀卷积的膨胀率。
- `recurrent_activation`：用在`recurrent`部分的激活函数，为预定义的激活函数名(参考激活函数)，或逐元素(`element-wise`)的`Theano`函数。如果不指定该参数，将不会使用任何激活函数(即使用线性激活函数`“a(x) = x”`)。
- `unit_forget_bias`：布尔值。如果为`True`，则初始化时，将忘记门的偏置加`1`。将其设置为`True`同时还会强制`bias_initializer="zeros"`。。

&emsp;&emsp;输入尺寸：

- 如果`data_format = 'channels_first'`，返回`5D`张量，尺寸为(`samples, time, channels, rows, cols`)。
- 如果`data_format = 'channels_last'`，返回`5D`张量，尺寸为(`samples, time, rows, cols, channels`)。

&emsp;&emsp;输出尺寸：如果`return_sequences`为`True`：

- 如果`data_format = 'channels_first'`，返回`5D`张量，尺寸为(`samples, time, filters, output_row, output_col`)。
- 如果`data_format = 'channels_last'`，返回`5D`张量，尺寸为(`samples, time, output_row, output_col, filters`)。

否则：

- 如果`data_format = 'channels_first'`，返回`4D`张量，尺寸为(`samples, filters, output_row, output_col`)。
- 如果`data_format = 'channels_last'`，返回`4D`张量，尺寸为(`samples, output_row, output_col, filters`)。`o_row`和`o_col`依赖于过滤器的尺寸和填充。

### SimpleRNNCell

&emsp;&emsp;该函数是`SimpleRNN`的单元类：

``` python
keras.layers.SimpleRNNCell(
    units, activation='tanh', use_bias=True, kernel_initializer='glorot_uniform',
    recurrent_initializer='orthogonal', bias_initializer='zeros', kernel_regularizer=None,
    recurrent_regularizer=None, bias_regularizer=None, kernel_constraint=None,
    recurrent_constraint=None, bias_constraint=None, dropout=0.0, recurrent_dropout=0.0)
```

### GRUCell

&emsp;&emsp;该函数是`GRU`层的单元类：

``` python
keras.layers.GRUCell(
    units, activation='tanh', recurrent_activation='hard_sigmoid', use_bias=True,
    kernel_initializer='glorot_uniform', recurrent_initializer='orthogonal',
    bias_initializer='zeros', kernel_regularizer=None, recurrent_regularizer=None,
    bias_regularizer=None, kernel_constraint=None, recurrent_constraint=None,
    bias_constraint=None, dropout=0.0, recurrent_dropout=0.0, implementation=1)
```

### LSTMCell

&emsp;&emsp;该函数是`LSTM`层的单元类：

``` python
keras.layers.LSTMCell(
    units, activation='tanh', recurrent_activation='hard_sigmoid', use_bias=True,
    kernel_initializer='glorot_uniform', recurrent_initializer='orthogonal',
    bias_initializer='zeros', unit_forget_bias=True, kernel_regularizer=None,
    recurrent_regularizer=None, bias_regularizer=None, kernel_constraint=None,
    recurrent_constraint=None, bias_constraint=None, dropout=0.0,
    recurrent_dropout=0.0, implementation=1)
```

### StackedRNNCells

&emsp;&emsp;该函数允许将一堆`RNN`单元包装为一个单元的封装器，用于实现高效堆叠的`RNN`：

``` python
keras.layers.StackedRNNCells(cells)
```

参数`cells`是`RNN`单元实例的列表。

``` python
cells = [keras.layers.LSTMCell(output_dim),
         keras.layers.LSTMCell(output_dim),
         keras.layers.LSTMCell(output_dim),]
inputs = keras.Input((timesteps, input_dim))
x = keras.layers.RNN(cells)(inputs)
```

### CuDNNGRU

&emsp;&emsp;该函数是由`CuDNN`支持的快速`GRU`实现，只能以`TensorFlow`后端运行在`GPU`上：

``` python
keras.layers.CuDNNGRU(
    units, kernel_initializer='glorot_uniform', recurrent_initializer='orthogonal',
    bias_initializer='zeros', kernel_regularizer=None, recurrent_regularizer=None,
    bias_regularizer=None, activity_regularizer=None, kernel_constraint=None,
    recurrent_constraint=None, bias_constraint=None, return_sequences=False,
    return_state=False, stateful=False)
```

### CuDNNLSTM

&emsp;&emsp;该函数是由`CuDNN`支持的快速`LSTM`实现，只能以`TensorFlow`后端运行在`GPU`上：

``` python
keras.layers.CuDNNLSTM(
    units, kernel_initializer='glorot_uniform', recurrent_initializer='orthogonal',
    bias_initializer='zeros', unit_forget_bias=True, kernel_regularizer=None,
    recurrent_regularizer=None, bias_regularizer=None, activity_regularizer=None,
    kernel_constraint=None, recurrent_constraint=None, bias_constraint=None,
    return_sequences=False, return_state=False, stateful=False)
```