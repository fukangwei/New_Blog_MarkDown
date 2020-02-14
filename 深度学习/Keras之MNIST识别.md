---
title: Keras之MNIST识别
categories: 深度学习
abbrlink: 171fee44
date: 2019-01-15 14:38:35
---
### 数据预处理

&emsp;&emsp;`Keras`自身就有`MNIST`这个数据包，再分成训练集和测试集。`x`是一张张图片，`y`是每张图片对应的标签，即它是哪个数字。<!--more-->
&emsp;&emsp;输入的`x`变成(`60000 * 784`)的数据(即训练集有`6`万张图片，每张图片的大小是`28 * 28`)，然后除以`255`进行标准化，因为每个像素都是在`0`到`255`之间，标准化之后就变成了`0`到`1`之间。
&emsp;&emsp;对于`y`，要用到`Keras`改造的`numpy`的一个函数`np_utils.to_categorical`，把y变成了`one-hot`的形式，即之前`y`是一个数值(在`0`至`9`之间)，现在是一个大小为`10`的向量，它属于哪个数字，就在哪个位置为`1`，其他位置都是`0`：

``` python
from keras.datasets import mnist
from keras.utils import np_utils
from keras.models import Sequential
from keras.layers import Dense, Activation
from keras.optimizers import RMSprop
​
(X_train, y_train), (X_test, y_test) = mnist.load_data()
# data pre-processing
X_train = X_train.reshape(X_train.shape[0], -1) / 255.  # normalize
X_test = X_test.reshape(X_test.shape[0], -1) / 255.  # normalize
y_train = np_utils.to_categorical(y_train, num_classes=10)
y_test = np_utils.to_categorical(y_test, num_classes=10)
​
print(X_train[1].shape)
print(y_train[:3])
```

### 建立模型

&emsp;&emsp;模型的第一段就是加入`Dense`神经层，`32`是输出的维度，`784`是输入的维度。第一层传出的数据有`32`个`feature`，传给激励单元，激励函数用到的是`relu`函数。经过激励函数之后，就变成了非线性的数据。然后再把这个数据传给下一个神经层，对于这个`Dense`，我们定义它有`10`个输出的`feature`。同样的，此处不需要再定义输入的维度，因为它接收的是上一层的输出。接下来再输入给下面的`softmax`函数，用来分类：

``` python
model = Sequential([
    Dense(32, input_dim=784),
    Activation('relu'),
    Dense(10),
    Activation('softmax'),
])
```

接下来用`RMSprop`作为优化器，它的参数包括学习率等，可以通过修改这些参数来看一下模型的效果：

``` python
rmsprop = RMSprop(lr=0.001, rho=0.9, epsilon=1e-08, decay=0.0)
```

### 激活模型

&emsp;&emsp;接下来用`model.compile`激励神经网络。优化器可以选择默认的，也可以是我们在上一步定义的。对于损失函数，分类和回归问题的不一样，用的是交叉熵。`metrics`里面可以放入需要计算的`cost`、`accuracy`、`score`等：

``` python
# We add metrics to get more results you want to see
model.compile(optimizer=rmsprop, loss='categorical_crossentropy', metrics=['accuracy'])
```

### 训练网络

&emsp;&emsp;这里用到的是`fit`函数，`nb_epoch`表示把整个数据训练多少次，`batch_size`显示每批处理`32`个：

``` python
print('Training ------------')
model.fit(X_train, y_train, epochs=2, batch_size=32)
```

### 测试模型

&emsp;&emsp;接下来就是用测试集来检验一下模型，方法和回归网络中是一样的。运行代码之后，可以输出`accuracy`和`loss`：

``` python
print('\nTesting ------------')
# Evaluate the model with the metrics we defined earlier
loss, accuracy = model.evaluate(X_test, y_test)
print('test loss: ', loss)
print('test accuracy: ', accuracy)
```

---

### CNN的MNIST实验

&emsp;&emsp;代码如下：

``` python
from keras.datasets import mnist
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Convolution2D, MaxPooling2D
from keras.utils import np_utils
from keras import backend as K
​
batch_size = 128
nb_classes = 10
epochs = 12
img_rows, img_cols = 28, 28  # input image dimensions
nb_filters = 32  # number of convolutional filters to use
pool_size = (2, 2)  # size of pooling area for max pooling
kernel_size = (3, 3)  # convolution kernel size
​
# the data, shuffled and split between train and test sets
(X_train, y_train), (X_test, y_test) = mnist.load_data()
​
if K.image_dim_ordering() == 'th':  # 根据不同的backend，决定不同的格式
    X_train = X_train.reshape(X_train.shape[0], 1, img_rows, img_cols)
    X_test = X_test.reshape(X_test.shape[0], 1, img_rows, img_cols)
    input_shape = (1, img_rows, img_cols)
else:
    X_train = X_train.reshape(X_train.shape[0], img_rows, img_cols, 1)
    X_test = X_test.reshape(X_test.shape[0], img_rows, img_cols, 1)
    input_shape = (img_rows, img_cols, 1)
​
X_train = X_train.astype('float32')
X_test = X_test.astype('float32')
X_train /= 255
X_test /= 255
print('X_train shape:', X_train.shape)
print(X_train.shape[0], 'train samples')
print(X_test.shape[0], 'test samples')
​
# 转换为one_hot类型  
Y_train = np_utils.to_categorical(y_train, nb_classes)
Y_test = np_utils.to_categorical(y_test, nb_classes)
​
model = Sequential()  # 构建模型
model.add(  # 卷积层1
    Convolution2D(nb_filters, (kernel_size[0], kernel_size[1]),
                  padding='same', input_shape=input_shape)
)
​
model.add(Activation('relu'))  # 激活层
model.add(Convolution2D(nb_filters, (kernel_size[0], kernel_size[1])))  # 卷积层2
model.add(Activation('relu'))  # 激活层
model.add(MaxPooling2D(pool_size=pool_size))  # 池化层
model.add(Dropout(0.25))  # 神经元随机失活
model.add(Flatten())  # 拉成一维数据
model.add(Dense(128))  # 全连接层1
model.add(Activation('relu'))  # 激活层
model.add(Dropout(0.5))  # 随机失活
model.add(Dense(nb_classes))  # 全连接层2
model.add(Activation('softmax'))  # Softmax评分
​
model.compile(loss='categorical_crossentropy',
              optimizer='adadelta', metrics=['accuracy'])
model.fit(X_train, Y_train, batch_size=batch_size, epochs=epochs,
          verbose=1, validation_data=(X_test, Y_test))
score = model.evaluate(X_test, Y_test, verbose=0)  # 评估模型
print('Test score:', score[0])
print('Test accuracy:', score[1])
```