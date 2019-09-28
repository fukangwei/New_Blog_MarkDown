---
title: Keras之函数式模型
date: 2019-01-16 18:34:38
categories: 深度学习
---
&emsp;&emsp;`Keras`函数式`API`是定义复杂模型(如多输出模型、有向无环图，或具有共享层的模型)的方法。只要你的模型不是类似`VGG`一条路走到黑的模型，或者你的模型需要多于一个的输出，那么你总应该选择函数式模型。函数式模型是最广泛的一类模型，序贯模型(`Sequential`)只是它的一种特殊情况。这部分的文档假设你已经对`Sequential`模型已经比较熟悉

### 全连接网络

&emsp;&emsp;`Sequential`模型可能是实现这种网络的一个更好选择，但这个例子能够帮助我们进行一些简单的理解。在开始前，有几个概念需要说明：

- 网络层的实例是可调用的，它以张量为参数，并且返回一个张量。
- 输入和输出均为张量，它们都可以用来定义一个模型。
- 这样的模型同`Keras`的`Sequential`模型一样，都可以被训练。

``` python
from keras.layers import Input, Dense
from keras.models import Model
​
inputs = Input(shape=(784,))  # 这部分返回一个张量
​
# 层的实例是可调用的，它以张量为参数，并且返回一个张量
x = Dense(64, activation='relu')(inputs)
x = Dense(64, activation='relu')(x)
predictions = Dense(10, activation='softmax')(x)
​
# 这部分创建了一个包含输入层和三个全连接层的模型
model = Model(inputs=inputs, outputs=predictions)
model.compile(optimizer='rmsprop', loss='categorical_crossentropy', metrics=['accuracy'])
model.fit(data, labels)  # 开始训练
```

&emsp;&emsp;利用函数式`API`，可以轻松地重用训练好的模型：可以将任何模型看作是一个层，然后通过传递一个张量来调用它。注意，在调用模型时，您不仅重用模型的结构，还重用了它的权重：

``` python
x = Input(shape=(784,))
y = model(x)  # 这是可行的，并且返回上面定义的“10-way softmax”。
```

这种方式能允许我们快速创建可以处理序列输入的模型。只需一行代码，你就将图像分类模型转换为视频分类模型：

``` python
from keras.layers import TimeDistributed
​
# 输入张量是20个时间步的序列，每一个时间为一个784维的向量
input_sequences = Input(shape=(20, 784))
​
# 这部分将我们之前定义的模型应用于输入序列中的每个时间步。之前定义的模型的输出
# 是一个“10-way softmax”，因而下面的层的输出将是维度为10的20个向量的序列
processed_sequences = TimeDistributed(model)(input_sequences)
```

### 层“节点”的概念

&emsp;&emsp;每当你在某个输入上调用一个层时，都将创建一个新的张量(层的输出)，并且为该层添加一个`节点`，将输入张量连接到输出张量。当多次调用同一个图层时，该图层将拥有多个节点索引(`0, 1, 2...`)。
&emsp;&emsp;在之前版本的`Keras`中，可以通过`layer.get_output`来获得层实例的输出张量，或者通过`layer.output_shape`来获取其输出形状。现在你依然可以这么做(除了`get_output`已经被`output`属性替代)。但是如果一个层与多个输入连接呢？只要一个层只连接到一个输入，就不会有困惑，`output`会返回层的唯一输出：

``` python
a = Input(shape=(140, 256))
​
lstm = LSTM(32)
encoded_a = lstm(a)
​
assert lstm.output == encoded_a
```

但是如果该层有多个输入，那就会出现问题：

``` python
a = Input(shape=(140, 256))
b = Input(shape=(140, 256))

lstm = LSTM(32)
encoded_a = lstm(a)
encoded_b = lstm(b)

lstm.output

>> AttributeError: Layer lstm_1 has multiple inbound nodes,
hence the notion of "layer output" is ill-defined.
Use `get_output_at(node_index)` instead.
```

好吧，通过下面的方法可以解决：

``` python
assert lstm.get_output_at(0) == encoded_a
assert lstm.get_output_at(1) == encoded_b
```

&emsp;&emsp;`input_shape`和`output_shape`这两个属性也是如此：只要该层只有一个节点，或者只要所有节点具有相同的输入/输出尺寸，那么`input_shape`和`output_shape`都是没有歧义的，并且将由`layer.output_shape/layer.input_shape`返回。但如果将一个`Conv2D`层先应用于尺寸为(`32, 32, 3`)的输入，再应用于尺寸为(`64, 64, 3`)的输入，那么这个层就会有多个输入/输出尺寸，你将不得不通过指定它们所属节点的索引来获取它们：

``` python
a = Input(shape=(32, 32, 3))
b = Input(shape=(64, 64, 3))

conv = Conv2D(16, (3, 3), padding='same')
conved_a = conv(a)

# 到目前为止只有一个输入，以下可行：
assert conv.input_shape == (None, 32, 32, 3)

conved_b = conv(b)
# 现在input_shape属性不可行，但是这样可以：
assert conv.get_input_shape_at(0) == (None, 32, 32, 3)
assert conv.get_input_shape_at(1) == (None, 64, 64, 3)
```

### Inception模型

&emsp;&emsp;有关`Inception`结构的更多信息，请参阅`Going Deeper with Convolutions`：

``` python
from keras.layers import Conv2D, MaxPooling2D, Input
​
input_img = Input(shape=(256, 256, 3))
​
tower_1 = Conv2D(64, (1, 1), padding='same', activation='relu')(input_img)
tower_1 = Conv2D(64, (3, 3), padding='same', activation='relu')(tower_1)
tower_2 = Conv2D(64, (1, 1), padding='same', activation='relu')(input_img)
tower_2 = Conv2D(64, (5, 5), padding='same', activation='relu')(tower_2)
tower_3 = MaxPooling2D((3, 3), strides=(1, 1), padding='same')(input_img)
tower_3 = Conv2D(64, (1, 1), padding='same', activation='relu')(tower_3)
​
output = keras.layers.concatenate([tower_1, tower_2, tower_3], axis=1)
```

### 卷积层上的残差连接

&emsp;&emsp;有关残差网络(`Residual Network`)的更多信息，请参阅`Deep Residual Learning for Image Recognition`：

``` python
from keras.layers import Conv2D, Input
​
x = Input(shape=(256, 256, 3))  # 输入张量为3通道“256 * 256”图像
# 3输出通道(与输入通道相同)的“3 * 3”卷积核
y = Conv2D(3, (3, 3), padding='same')(x)
z = keras.layers.add([x, y])  # 返回“x + y”
```

---

### Model类API

&emsp;&emsp;在函数式`API`中，给定一些输入张量和输出张量，可以通过以下方式实例化一个`Model`：

``` python
from keras.models import Model
from keras.layers import Input, Dense

a = Input(shape=(32,))
b = Dense(32)(a)
model = Model(inputs=a, outputs=b)
```

这个模型将包含从`a`到`b`的计算的所有网络层。在多输入或多输出模型的情况下，你也可以使用列表：

``` python
model = Model(inputs=[a1, a2], outputs=[b1, b3, b3])
```

&emsp;&emsp;`Model`的实用属性：`model.layers`是组成模型图的各个层；`model.inputs`是输入张量的列表；`model.outputs`是输出张量的列表。

### Model类模型方法

#### compile

&emsp;&emsp;该函数用于配置训练模型：

``` python
compile(
    self, optimizer, loss, metrics=None, loss_weights=None,
    sample_weight_mode=None, weighted_metrics=None, target_tensors=None)
```

- `optimizer`：字符串(预定义优化器名)或者优化器对象。
- `loss`：字符串(预定义损失函数名)或目标函数。如果模型具有多个输出，则可以通过传递损失函数的字典或列表，在每个输出上使用不同的损失。模型将最小化的损失值将是所有单个损失的总和。
- `metrics`：在训练和测试期间的模型评估标准，通常你会使用`metrics = ['accuracy']`。要为多输出模型的不同输出指定不同的评估标准，还可以传递一个字典，如`metrics = {'output_a': 'accuracy'}`。
- `loss_weights`：可选的指定标量系数(`Python`浮点数)的列表或字典，用以衡量损失函数对不同的模型输出的贡献。模型将最小化的误差值是由`loss_weights`系数加权的加权总和误差。如果是列表，那么它应该是与模型输出相对应的`1:1`映射。如果是张量，那么应该把输出的名称(字符串)映到标量系数。
- `sample_weight_mode`：如果你需要执行按时间步采样权重(`2D`权重)，请将其设置为`temporal`。默认为`None`，为采样权重(`1D`)。如果模型有多个输出，则可以通过传递`mode`的字典或列表，以在每个输出上使用不同的`sample_weight_mode`。
- `weighted_metrics`：在训练和测试期间，这些`metrics`将由`sample_weight`或`clss_weight`计算并赋权。
- `target_tensors`：默认情况下，`Keras`将为模型的目标创建一个占位符，该占位符在训练过程中将被目标数据代替。相反，如果你想使用自己的目标张量(反过来说，`Keras`在训练期间不会载入这些目标张量的外部`Numpy`数据)，您可以通过`target_tensors`参数指定它们。它可以是单个张量(单输出模型)、张量列表、或一个映射输出名称到目标张量的字典。
- `**kwargs`：当使用`Theano/CNTK`后端时，这些参数被传入`K.function`。当使用`TensorFlow`后端时，这些参数被传递到`tf.Session.run`。

&emsp;&emsp;如果你只是载入模型并利用其`predict`，可以不用进行`compile`。在`Keras`中，`compile`主要完成损失函数和优化器的一些配置，是为训练服务的。`predict`会在内部进行符号函数的编译工作(通过调用`_make_predict_function`生成函数)。

#### fit

&emsp;&emsp;该函数的作用是以固定数量的轮次(数据集上的迭代)训练模型：

``` python
fit(
    self, x=None, y=None, batch_size=None, epochs=1, verbose=1, callbacks=None,
    validation_split=0.0, validation_data=None, shuffle=True, class_weight=None,
    sample_weight=None, initial_epoch=0, steps_per_epoch=None, validation_steps=None)
```

- `x`：训练数据的`Numpy`数组(如果模型只有一个输入)，或者是`Numpy`数组的列表(如果模型有多个输入)。如果模型中的输入层被命名，你也可以传递一个字典，将输入层名称映射到`Numpy`数组。如果从本地框架张量馈送(例如`TensorFlow`数据张量)数据，`x`可以是`None`(默认)。
- `y`：目标(标签)数据的`Numpy`数组(如果模型只有一个输出)，或者是`Numpy`数组的列表(如果模型有多个输出)。如果模型中的输出层被命名，你也可以传递一个字典，将输出层名称映射到`Numpy`数组。如果从本地框架张量馈送(例如`TensorFlow`数据张量)数据，`y`可以是`None`(默认)。
- `batch_size`：整数或`None`，每次梯度更新的样本数。
- `epochs`：整数，训练模型迭代轮次。一个轮次是在整个`x`或`y`上的一轮迭代。请注意，与`initial_epoch`一起，`epochs`被理解为`最终轮次`。模型并不是训练了`epochs`轮，而是到第`epochs`轮停止训练。
- `verbose`：日志显示模式，`0`为不在标准输出流输出日志信息，`1`为输出进度条记录，`2`为每个`epoch`输出一行记录。
- `callbacks`：一系列的`keras.callbacks.Callback`实例，一系列可以在训练时使用的回调函数。
- `validation_split`：在`0`和`1`之间浮动，用来指定训练集的一定比例数据作为验证集。验证集将不参与训练，并在每个`epoch`结束后测试的模型的指标，如损失函数、精确度等。注意，`validation_split`的划分在`shuffle`之后，因此如果你的数据本身是有序的，需要先手工打乱再指定`validation_split`，否则可能会出现验证集样本不均匀。
- `validation_data`：元组(`x_val, y_val`)或元组(`x_val, y_val, val_sample_weights`)，是指定的验证集。这个参数会覆盖`validation_split`。
- `shuffle`：布尔值(是否在每轮迭代之前混洗数据)或者字符串(`batch`)。`batch`是处理`HDF5`数据限制的特殊选项，它对一个`batch`内部的数据进行混洗。当`steps_per_epoch`非`None`时，这个参数无效。
- `class_weight`：可选的字典，将不同的类别映射为不同的权值，该参数用来在训练过程中调整损失函数(只能用于训练)。该参数在处理非平衡的训练数据(某些类的训练样本数很少)时，可以使得损失函数对样本数不足的数据更加关注。
- `sample_weight`：训练样本的可选`Numpy`权重数组，用于在训练时调整损失函数(仅用于训练)。可以传递一个`1D`的与样本等长的向量用于对样本进行`1`对`1`的加权，或者在面对时序数据时，传递一个的形式为(`samples, sequence_length`)的矩阵来为每个时间步上的样本赋不同的权。这种情况下请确定在编译模型时添加了`sample_weight_mode = 'temporal'`。
- `initial_epoch`：整数，从该参数指定的`epoch`开始训练，有助于恢复之前的训练。
- `steps_per_epoch`：整数或`None`，在声明一个轮次完成并开始下一个轮次之前的总步数(样品批次数)。使用`TensorFlow`数据张量等输入张量进行训练时，默认值`None`等于数据集中样本的数量除以`batch`的大小。
- `validation_steps`：只有在指定了`steps_per_epoch`时才有用，在验证集上的`step`总数。

该函数返回一个`History`的对象，其`History.history`属性记录了损失函数和其他指标的数值随`epoch`变化的情况，如果有验证集的话，也包含了验证集的这些指标变化情况。

#### evaluate

&emsp;&emsp;该函数的作用是在测试模式下返回模型的误差值和评估标准值(计算是分批进行的)：

``` python
evaluate(self, x=None, y=None, batch_size=None, verbose=1, sample_weight=None)
```

- `x`：测试数据的`Numpy`数组(如果模型只有一个输入)，或者是`Numpy`数组的列表(如果模型有多个输入)。如果模型中的输入层被命名，你也可以传递一个字典，将输入层名称映射到`Numpy`数组。如果从本地框架张量馈送(例如`TensorFlow`数据张量)数据，`x`可以是`None`(默认)。
- `y`：目标(标签)数据的`Numpy`数组，或`Numpy`数组的列表(如果模型具有多个输出)。如果模型中的输出层被命名，你也可以传递一个字典，将输出层名称映射到`Numpy`数组。如果从本地框架张量馈送(例如`TensorFlow`数据张量)数据，`y`可以是`None`(默认)。
- `batch_size`：整数或`None`，每次梯度更新的样本数。
- `verbose`：日志显示模式，`0`为不在标准输出流输出日志信息，`1`为输出进度条记录。
- `sample_weight`：训练样本的可选`Numpy`权重数组，用于在训练时调整损失函数(仅用于训练)。可以传递一个`1D`的与样本等长的向量用于对样本进行`1`对`1`的加权，或者在面对时序数据时，传递一个的形式为(`samples, sequence_length`)的矩阵来为每个时间步上的样本赋不同的权。这种情况下请确定在编译模型时添加了`sample_weight_mode = 'temporal'`。

该函数返回标量测试误差(如果模型只有一个输出且没有评估标准)或标量列表(如果模型具有多个输出和/或评估指标)。属性`model.metrics_names`将提供标量输出的显示标签。

#### predict

&emsp;&emsp;该函数的作用是为输入样本生成输出预测(计算是分批进行的)：

``` python
predict(self, x, batch_size=None, verbose=0)
```

该函数返回预测的`Numpy`数组(或数组列表)。

#### train_on_batch

&emsp;&emsp;该函数在一个`batch`的数据上进行一次参数更新：

``` python
train_on_batch(self, x, y, sample_weight=None, class_weight=None)
```

该函数返回标量训练误差(如果模型只有一个输入且没有评估标准)，或者标量的列表(如果模型有多个输出和/或评估标准)。属性`model.metrics_names`将提供标量输出的显示标签。

#### test_on_batch

&emsp;&emsp;该函数在一个`batch`的样本上对模型进行评估：

``` python
test_on_batch(self, x, y, sample_weight=None)
```

该函数返回标量测试误差(如果模型只有一个输入且没有评估标准)，或者标量的列表(如果模型有多个输出`和/或`评估标准)。属性`model.metrics_names`将提供标量输出的显示标签。

#### predict_on_batch

&emsp;&emsp;该函数返回一个`batch`的样本上的模型预测值：

``` python
predict_on_batch(self, x)
```

参数`x`是输入数据，`Numpy`数组。该函数返回预测值的`Numpy`数组(或数组列表)。

#### fit_generator

&emsp;&emsp;该函数利用`Python`的生成器，逐个生成数据的`batch`并进行训练：

``` python
fit_generator(
    self, generator, steps_per_epoch=None, epochs=1, verbose=1, callbacks=None,
    validation_data=None, validation_steps=None, class_weight=None, max_queue_size=10,
    workers=1, use_multiprocessing=False, shuffle=True, initial_epoch=0
)
```

生成器与模型并行运行，以提高效率。例如，该函数允许我们在`CPU`上进行实时的数据提升，同时在`GPU`上进行模型训练。该函数返回一个`History`对象。
&emsp;&emsp;`keras.utils.Sequence`的使用可以保证数据的顺序，以及当`use_multiprocessing = True`时，保证每个输入在每个`epoch`只使用一次。

- `generator`：一个生成器，或者一个`Sequence(keras.utils.Sequence)`对象的实例，为了在使用多进程时避免数据的重复。生成器的输出应该为以下之一：

1. 一个(`inputs, targets`)元组。
2. 一个(`inputs, targets, sample_weights`)元组。所有的返回值都应该包含相同数目的样本。生成器将无限在数据集上循环。每个`epoch`以经过模型的样本数达到`samples_per_epoch`时，记一个`epoch`结束。

- `steps_per_epoch`：在声明一个`epoch`完成并开始下一个`epoch`之前从`generator`产生的总步数(批次样本)。它通常应该等于你的数据集的样本数量除以批量大小。对于`Sequence`，它是可选的：如果未指定，将使用`len(generator)`作为步数。
- `epochs`：整数，数据的迭代总轮数。请注意，与`initial_epoch`一起，参数`epochs`应被理解为`最终轮数`。模型并不是训练了`epochs`轮，而是到第`epochs`轮停止训练。
- `verbose`：日志显示模式，`0`为不在标准输出流输出日志信息，`1`为输出进度条记录，`2`为每个`epoch`输出一行记录。
- `callbacks`：在训练时调用的一系列回调函数。
- `validation_data`：它可以是以下之一：

1. 验证数据的生成器。
2. 一个(`inputs, targets`)元组。
3. 一个(`inputs, targets, sample_weights`)元组。

- `validation_steps`：当`validation_data`为生成器时，本参数指定验证集的生成器返回次数。它通常应该等于你的数据集的样本数量除以批量大小。可选参数`Sequence`：如果未指定，将使用`len(generator)`作为步数。
- `class_weight`：将类别索引映射为权重的字典。
- `max_queue_size`：整数，生成器队列的最大尺寸。如未指定，`max_queue_size`将默认为`10`。
- `workers`：整数，使用的最大进程数量，如果使用基于进程的多线程。如未指定，`workers`将默认为`1`。如果为`0`，将在主线程上执行生成器。
- `use_multiprocessing`：布尔值。如果`True`，则使用基于进程的多线程。如未指定，`use_multiprocessing`将默认为`False`。请注意，由于此实现依赖于多进程，所以不应将不可传递的参数传递给生成器，因为它们不能被轻易地传递给子进程。
- `shuffle`：是否在每轮迭代之前打乱`batch`的顺序，只能与`Sequence(keras.utils.Sequence)`实例同用。
- `initial_epoch`：从该参数指定的`epoch`开始训练，有助于恢复之前的训练。

``` python
def generate_arrays_from_file(path):
    while 1:
        f = open(path)

        for line in f:
            # 从文件中的每一行生成输入数据和标签的numpy数组
            x1, x2, y = process_line(line)
            yield ({'input_1': x1, 'input_2': x2}, {'output': y})

        f.close()
​
model.fit_generator(generate_arrays_from_file('/my_file.txt'), steps_per_epoch=10000, epochs=10)
```

#### evaluate_generator

&emsp;&emsp;该函数在数据生成器上评估模型：

``` python
evaluate_generator(
    self, generator, steps=None, max_queue_size=10,
    workers=1, use_multiprocessing=False)
```

这个生成器应该返回与`test_on_batch`的输入数据相同类型的数据。

- `generator`：一个生成(`inputs, targets`)或(`inputs, targets, sample_weights`)的生成器，或一个`Sequence(keras.utils.Sequence)`对象的实例，为了避免在使用多进程时数据的重复。
- `steps`：在声明一个`epoch`完成并开始下一个`epoch`之前从`generator`产生的总步数(批次样本数)。它通常应该等于你的数据集的样本数量除以批量大小。对于`Sequence`，它是可选的：如果未指定，将使用`len(generator)`作为步数。

该函数返回标量测试误差(如果模型只有一个输入且没有评估标准)，或者标量的列表(如果模型有多个输出和/或评估标准)。属性`model.metrics_names`将提供标量输出的显示标签。

#### predict_generator

&emsp;&emsp;该函数为来自数据生成器的输入样本生成预测：

``` python
predict_generator(
    self, generator, steps=None, max_queue_size=10,
    workers=1, use_multiprocessing=False, verbose=0)
```

这个生成器应返回与`predict_on_batch`的输入数据相同类型的数据。该函数返回预测值的`Numpy`数组(或数组列表)。

#### get_layer

&emsp;&emsp;该函数根据名称(唯一)或索引值查找网络层：

``` python
get_layer(self, name=None, index=None)
```

根据网络层的名称(唯一)或其索引返回该层，索引是基于水平图遍历的顺序(自下而上)。参数`name`是字符串，即层的名字；参数`index`是整数，即层的索引。该函数返回一个层实例。