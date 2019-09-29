---
title: Keras之回调函数
categories: 深度学习
abbrlink: f76632d
date: 2019-01-16 10:34:19
---
&emsp;&emsp;回调函数是一个函数的合集，会在训练的阶段中所使用。你可以使用回调函数来查看训练模型的内在状态和统计。你可以传递一个列表的回调函数(作为`callbacks`关键字参数)到`Sequential`或`Model`类型的`fit`方法。在训练时，相应的回调函数的方法就会被在各自的阶段被调用。
&emsp;&emsp;虽然我们称之为`回调函数`，但事实上`Keras`的回调函数是一个类，回调函数只是习惯性称呼。

### Callback

&emsp;&emsp;该函数用来组建新的回调函数的抽象基类：

``` python
keras.callbacks.Callback()
```

类属性如下：

- `params`：字典，训练参数(例如`verbosity`、`batch size`、`number of epochs`等)。
- `model`：`keras.models.Model`的实例，它是正在训练的模型的引用。

被回调函数作为参数的`logs`字典，它会包含了一系列与当前`batch`或`epoch`相关的信息。目前，`Sequentia`模型类的`fit`方法会在传入到回调函数的`logs`里面包含以下的数据：

- 在每个`epoch`的结尾处(`on_epoch_end`)：`logs`将包含训练的正确率和误差(`acc`和`loss`)，如果指定了验证集，还会包含验证集正确率和误差(`val_acc`和`val_loss`)，`val_acc`还额外需要在`compile`中启用`metrics = ['accuracy']`。
- 在每个`batch`的开始处(`on_batch_begin`)：`logs`包含`size`，即当前`batch`的样本数。
- 在每个`batch`的结尾处(`on_batch_end`)：`logs`包含`loss`，若启用`accuracy`，则还包含`acc`。

### BaseLogger

&emsp;&emsp;该回调函数用来对每个`epoch`累加`metrics`指定的监视指标的`epoch`平均值：

``` python
keras.callbacks.BaseLogger()
```

这个回调函数被自动应用到每一个`Keras`模型上面。

### TerminateOnNaN

&emsp;&emsp;该函数是当遇到`NaN`损失会停止训练的回调函数：

``` python
keras.callbacks.TerminateOnNaN()
```

### History

&emsp;&emsp;该函数是把所有事件都记录到`History`对象的回调函数：

``` python
keras.callbacks.History()
```

该回调函数在`Keras`模型上会被自动调用，`History`对象即为`fit`方法的返回值。

### ModelCheckpoint

&emsp;&emsp;该函数在每个训练期之后保存模型：

``` python
keras.callbacks.ModelCheckpoint(
    filepath, monitor='val_loss', verbose=0, save_best_only=False,
    save_weights_only=False, mode='auto', period=1)
```

`filepath`可以是格式化的字符串，里面的占位符将会被`epoch`值和传入`on_epoch_end`的`logs`关键字所填入。例如，如果`filepath`是`weights.{epoch:02d}-{val_loss:.2f}.hdf5`，那么会生成对应`epoch`和验证集`loss`的多个文件。

- `filepath`：字符串，保存模型的路径。
- `monitor`：被监测的数据。
- `verbose`：详细信息模式，`0`或者`1`。
- `save_best_only`：当设置为`True`时，将只保存在验证集上性能最好的模型。
- `mode`：`auto`、`min`和`max`其中之一。在`save_best_only=True`时决定性能最佳模型的评判准则，例如当监测值为`val_acc`时，模式应为`max`；当检测值为`val_loss`时，模式应为`min`。在`auto`模式下，评价准则由被监测值的名字自动推断。
- `save_weights_only`：如果为`True`，那么只有模型的权重会被保存(`model.save_weights(filepath)`)；否则的话，整个模型会被保存(`model.save(filepath)`)。
- `period`：每个检查点之间的间隔(训练轮数)。

``` python
from keras.callbacks import ModelCheckpoint
​
model = Sequential()
model.add(Dense(10, input_dim=784, kernel_initializer='uniform'))
model.add(Activation('softmax'))
model.compile(loss='categorical_crossentropy', optimizer='rmsprop')
""" 如果验证损失下降，那么在每个训练轮之后保存模型 """
checkpointer = ModelCheckpoint(filepath='/tmp/weights.hdf5',
                               verbose=1, save_best_only=True)
model.fit(x_train, y_train, batch_size=128, epochs=20, verbose=0,
          validation_data=(X_test, Y_test), callbacks=[checkpointer])
```

### EarlyStopping

&emsp;&emsp;当监测值不再改善时，该回调函数将中止训练：

``` python
keras.callbacks.EarlyStopping(monitor='val_loss', min_delta=0, patience=0, verbose=0, mode='auto')
```

- `min_delta`：在被监测的数据中被认为是提升的最小变化，例如小于`min_delta`的绝对变化会被认为没有提升。
- `patience`：当`early stop`被激活(例如发现`loss`相比上一个`epoch`训练没有下降)，则经过`patience`个`epoch`后停止训练。
- `mode`：`auto`、`min`和`max`其中之一。在`min`模式中，当被监测的数据停止下降，训练就会停止；在`max`模式中，当被监测的数据停止上升，训练就会停止；在`auto`模式中，方向会自动从被监测的数据的名字中判断出来。

### LearningRateScheduler

&emsp;&emsp;学习速率定时器：

``` python
keras.callbacks.LearningRateScheduler(schedule, verbose=0)
```

参数`schedule`是一个函数，该函数以`epoch`号为参数(从`0`算起的整数)，返回一个新学习率(浮点数)。

### TensorBoard

&emsp;&emsp;该函数用于`Tensorboard`基本可视化。`TensorBoard`是由`Tensorflow`提供的一个可视化工具。这个回调函数为`Tensorboard`编写一个日志，使得你可以动态地观察训练和测试指标的图像以及不同层的激活值直方图。

``` python
keras.callbacks.TensorBoard(
    log_dir='./logs', histogram_freq=0, batch_size=32, write_graph=True,
    write_grads=False, write_images=False, embeddings_freq=0,
    embeddings_layer_names=None, embeddings_metadata=None)
```

- `log_dir`：用来保存被`TensorBoard`分析的日志文件的文件名。
- `histogram_freq`：计算各个层激活值直方图的频率(每多少个`epoch`计算一次)，如果设置为`0`则不计算。
- `write_graph`：是否在`TensorBoard`中可视化图像。如果`write_graph`被设置为`True`，日志文件会变得非常大。
- `write_grads`：是否在`TensorBoard`中可视化梯度值直方图。`histogram_freq`必须要大于`0`。
- `batch_size`：用以直方图计算的传入神经元网络输入批的大小。
- `write_images`：是否将模型权重以图片的形式可视化。
- `embeddings_freq`：依据该频率(以`epoch`为单位)筛选保存的`embedding`层。
- `embeddings_layer_names`：要观察的层名称的列表，若设置为`None`或空列表，则所有`embedding`层都将被观察。
- `embeddings_metadata`：一个字典，将层名称映射为包含该`embedding`层元数据的文件名。如果所有的`embedding`层都使用相同的元数据文件，则可传递字符串。

### ReduceLROnPlateau

&emsp;&emsp;当评价指标不再提升时，减少学习率：

``` python
keras.callbacks.ReduceLROnPlateau(
    monitor='val_loss', factor=0.1, patience=10, verbose=0,
    mode='auto', epsilon=0.0001, cooldown=0, min_lr=0)
```

- `monitor`：被监测的数据。
- `factor`：每次减少学习率的因子，学习率将以`lr = lr * factor`的形式被减少。
- `patience`：当经历了`patience`个`epoch`，而模型性能不提升时，学习率减少的动作会被触发。
- `mode`：`auto`、`min`或`max`其中之一。如果是`min`模式，如果被监测的数据已经停止下降，学习速率会被降低；在`max`模式，如果被监测的数据已经停止上升，学习速率会被降低；在`auto`模式，方向会被从被监测的数据中自动推断出来。
- `epsilon`：阈值，用来确定是否进入检测值的`平原区`。
- `cooldown`：学习率减少后，会经过`cooldown`个`epoch`才重新进行正常操作。
- `min_lr`：学习率的下限。

当学习停滞时，减少`2`倍或`10`倍的学习率常常能获得较好的效果。该回调函数检测指标的情况，如果在`patience`个`epoch`中看不到模型性能提升，则减少学习率：

``` python
reduce_lr = ReduceLROnPlateau(monitor='val_loss', factor=0.2, patience=5, min_lr=0.001)
model.fit(X_train, Y_train, callbacks=[reduce_lr])
```

### CSVLogger

&emsp;&emsp;函数原型如下：

``` python
keras.callbacks.CSVLogger(filename, separator=',', append=False)
```

- `filename`：`csv`文件的文件名。
- `separator`：用来隔离`csv`文件中元素的字符串。
- `append`：如果为`True`，则表示如果文件存在则增加(可以被用于继续训练)；如果为`False`，表示覆盖存在的文件。

将`epoch`的训练结果保存在`csv`文件中，支持所有可被转换为`string`的值，包括`1D`的可迭代数值(例如`np.ndarray`)。

``` python
csv_logger = CSVLogger('training.log')
model.fit(X_train, Y_train, callbacks=[csv_logger])
```

### 编写自己的回调函数

&emsp;&emsp;我们可以通过继承`keras.callbacks.Callback`编写自己的回调函数。如下示例可以保存每个`batch`的`loss`：

``` python
class LossHistory(keras.callbacks.Callback):
    def on_train_begin(self, logs={}):
        self.losses = []
​
    def on_batch_end(self, batch, logs={}):
        self.losses.append(logs.get('loss'))
​
model = Sequential()
model.add(Dense(10, input_dim=784, kernel_initializer='uniform'))
model.add(Activation('softmax'))
model.compile(loss='categorical_crossentropy', optimizer='rmsprop')
​
history = LossHistory()
model.fit(x_train, y_train, batch_size=128, epochs=20, verbose=0, callbacks=[history])
print(history.losses)
```