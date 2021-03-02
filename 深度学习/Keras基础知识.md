---
title: Keras基础知识
categories: 深度学习
date: 2019-01-01 20:30:49
---
&emsp;&emsp;`Keras`是一个用`Python`编写的高级神经网络`API`，它能够以`TensorFlow`、`CNTK`或者`Theano`作为后端运行。`Keras`的开发重点是支持快速的实验，能够以最小的时间把你的想法转换为实验结果，是做好研究的关键。<!--more-->
&emsp;&emsp;无论是`Theano`还是`TensorFlow`，都是一个符号式的库，这也使得`Keras`的编程与传统的`Python`代码有所差别。笼统的说，符号主义的计算首先定义各种变量，然后建立一个计算图，该计算图规定了各个变量之间的计算关系。建立好的计算图需要编译以确定其内部细节，然而此时的计算图还是一个空壳子，里面没有任何实际的数据，只有当你把需要运算的输入放进去后，才能在整个模型中形成数据流，从而形成输出值。就像用管道搭建供水系统，当你在拼水管的时候，里面是没有水的。只有所有的管子都接完了，才能送水。
&emsp;&emsp;`Keras`的模型搭建形式就是这种方法，在你搭建`Keras`模型完毕后，你的模型就是一个空壳子，只有实际生成可调用的函数(`K.function`)，并输入数据，才会形成真正的数据流。
&emsp;&emsp;如果你在以下情况下需要深度学习库，请使用`Keras`：

- 允许简单而快速的原型设计(用户友好、高度模块化、可扩展性强)。
- 同时支持卷积神经网络和循环神经网络，以及两者的组合。
- 在`CPU`和`GPU`上无缝运行。

### 指导原则

&emsp;&emsp;**用户友好**。`Keras`是为人类而不是为机器设计的`API`，它把用户体验放在首要和中心位置。`Keras`提供一致且简单的`API`，将常见用例所需的用户操作数量降至最低，并且在用户错误时提供清晰和可操作的反馈。
&emsp;&emsp;**模块化**。模型被理解为由独立的、完全可配置的模块构成的序列或图，这些模块可以以尽可能少的限制组装在一起。特别是神经网络层、损失函数、优化器、初始化方法、激活函数、正则化方法，它们都是可以结合起来构建新模型的模块。
&emsp;&emsp;**易扩展性**。新模块是很容易添加的(作为新的类和函数)，现有的模块已经提供了充足的示例。由于能够轻松地创建可以提高表现力的新模块，`Keras`更加适合高级研究。
&emsp;&emsp;**基于`Python`实现**。`Keras`没有特定格式的单独配置文件。模型定义在`Python`代码中，这些代码紧凑，易于调试，并且易于扩展。
&emsp;&emsp;**无缝集成**。因为`Keras`与底层深度学习语言(特别是`TensorFlow`)集成在一起，所以它可以让你实现任何你可以用基础语言编写的东西。特别的，`tf.keras`作为`Keras API`可以与`TensorFlow`工作流无缝集成。

### 快速开始：30秒上手Keras

&emsp;&emsp;`Keras`的核心数据结构是`model`，一种组织网络层的方式。最简单的模型是`Sequential`顺序模型，它是由多个网络层线性堆叠的栈。对于更复杂的结构，你应该使用`Keras`函数式`API`，它允许构建任意的神经网络图。
&emsp;&emsp;`Sequential`顺序模型如下：

``` python
from keras.models import Sequential
model = Sequential()
```

可以简单地使用`add`函数来堆叠模型：

``` python
from keras.layers import Dense
model.add(Dense(units=64, activation='relu', input_dim=100))
model.add(Dense(units=10, activation='softmax'))
```

在完成了模型的构建后，可以使用`compile`函数来配置学习过程：

``` python
model.compile(loss='categorical_crossentropy', optimizer='sgd', metrics=['accuracy'])
```

如果需要的话，你还可以进一步地配置你的优化器。`Keras`的核心原则是使事情变得相当简单，同时又允许用户在需要的时候能够进行完全的控制(终极的控制是源代码的易扩展性)：

``` python
model.compile(
    loss=keras.losses.categorical_crossentropy,
    optimizer=keras.optimizers.SGD(lr=0.01, momentum=0.9, nesterov=True))
```

现在你可以批量地在训练数据上进行迭代了：

``` python
model.fit(x_train, y_train, epochs=5, batch_size=32)
```

或者你可以手动地将批次的数据提供给模型：

``` python
model.train_on_batch(x_batch, y_batch)
```

只需一行代码就能评估模型性能：

``` python
loss_and_metrics = model.evaluate(x_test, y_test, batch_size=128)
```

或者对新的数据生成预测：

``` python
classes = model.predict(x_test, batch_size=128)
```

构建一个问答系统，一个图像分类模型，一个神经图灵机，或者其他的任何模型，就是这么的快。

### data_format

&emsp;&emsp;这是一个无可奈何的问题，在如何表示一组彩色图片的问题上，`Theano`和`TensorFlow`发生了分歧。`th`模式(即`Theano`模式)会把`100`张`RGB`三通道的`16*32`(高为`16`宽为`32`)彩色图表示为`(100, 3, 16, 32)`，`Caffe`采取的也是这种方式。第`0`个维度是样本维，代表样本的数目；第`1`个维度是通道维，代表颜色通道数；后面两个就是高和宽了。这种`theano`风格的数据组织方法，称为`channels_first`，即通道维靠前。
&emsp;&emsp;`TensorFlow`的表达形式是`(100, 16, 32, 3)`，也就是把通道维放在了最后，这种数据组织方式称为`channels_last`。
&emsp;&emsp;`Keras`默认的数据组织形式在`~/.keras/keras.json`中规定，可查看该文件的`image_data_format`，也可在代码中通过`K.image_data_format`函数返回，请在网络的训练和测试中保持维度顺序一致。

### 梯度下降

&emsp;&emsp;深度学习的优化算法，说白了就是梯度下降。每次的参数更新有两种方式：

- 遍历全部数据集计算一次损失函数，然后计算函数对各个参数的梯度，最后更新梯度。这种方法每更新一次参数都要把数据集里的所有样本都看一遍，计算量开销大，计算速度慢，不支持在线学习，这称为`Batch gradient descent`，即`批梯度下降`。
- 每看一个数据就算一下损失函数，然后求梯度更新参数，称为`随机梯度下降`(`stochastic gradient descent`)。这个方法速度比较快，但是收敛性能不太好，可能在最优点附近晃来晃去，`hit`不到最优点。两次参数的更新也有可能互相抵消掉，造成目标函数震荡的比较剧烈。

&emsp;&emsp;为了克服两种方法的缺点，现在一般采用的是一种折中手段`mini-batch gradient decent`。这种方法把数据分为若干个批，按批来更新参数，这样一个批中的一组数据共同决定了本次梯度的方向，下降起来就不容易跑偏，减少了随机性。另一方面，因为批的样本数与整个数据集相比小了很多，计算量也不是很大。
&emsp;&emsp;基本上现在的梯度下降都是基于`mini-batch`的，所以`Keras`的模块中经常会出现`batch_size`，就是指批的大小。顺便说一句，`Keras`中用的优化器`SGD`是`stochastic gradient descent`的缩写，但不代表是一个样本就更新一回，它还是基于`mini-batch`的。