---
title: Network in Network模型
date: 2019-02-15 20:55:52
categories: 深度学习
---
&emsp;&emsp;经典`CNN`中的卷积层其实就是用线性滤波器对图像进行内积运算，在每个局部输出后面跟着一个非线性的激活函数，最终得到特征图。而这种卷积滤波器是一种广义线性模型(`Generalized linear model`，`GLM`)，所以用`CNN`进行特征提取时，其实就隐含地假设了特征是线性可分的，可实际问题往往是难以线性可分的。
&emsp;&emsp;`GLM`的抽象能力是比较低的，我们自然地想到用一种抽象能力更强的模型去替换它，从而提升传统`CNN`的表达能力，如下图所示。实际上就是将传统`CNN`的线性卷积层替换为了多层感知机(多层全连接层和非线性函数的组合)。

<img src="./Network in Network模型/1.png" height="206" width="436">

&emsp;&emsp;其次就是最后的全连接层被替换为了全局池化层(`global average pooling`)，这样做的好处就是减少这一层的参数，避免过拟合：

<img src="./Network in Network模型/2.png" height="191" width="653">

<img src="./Network in Network模型/3.png" height="168" width="534">

&emsp;&emsp;`global pooling`就是`pooling`的滑窗`size`和整张`feature map`的`size`一样大，于是每个`W * H * C`的`feature map`输入就会被转化为`1 * 1 * C`输出，其实也等同于每个位置权重都为`1 / (W * H)`的`FC`层操作。`global pooling`在滑窗内的具体`pooling`方法可以是任意的，所以就会被细分为`global avg pooling`、`global max pooling`等。
&emsp;&emsp;`Keras`实现代码如下：

``` python
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D, GlobalAveragePooling2D, AveragePooling2D
from keras.layers.normalization import BatchNormalization
from keras import optimizers
from keras.callbacks import LearningRateScheduler, TensorBoard
from keras import backend as K
​
batch_size = 128
epochs = 20
iterations = 391
num_classes = 10
dropout = 0.5
weight_decay = 0.0001
log_filepath = './nin_bn'
​
if ('tensorflow' == K.backend()):
    import tensorflow as tf
​
    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    sess = tf.Session(config=config)
​
def color_preprocessing(x_train, x_test):
    x_train = x_train.astype('float32')
    x_test = x_test.astype('float32')
    mean = [125.307, 122.95, 113.865]
    std = [62.9932, 62.0887, 66.7048]

    for i in range(3):
        x_train[:, :, :, i] = (x_train[:, :, :, i] - mean[i]) / std[i]
        x_test[:, :, :, i] = (x_test[:, :, :, i] - mean[i]) / std[i]
​
    return x_train, x_test
​
def scheduler(epoch):
    if epoch <= 60:
        return 0.05

    if epoch <= 120:
        return 0.01

    if epoch <= 160:
        return 0.002

    return 0.0004
​
def build_model():
    model = Sequential()
​
    model.add(Conv2D(192, (5, 5), padding='same', kernel_regularizer=keras.regularizers.l2(weight_decay), \
                     kernel_initializer="he_normal", input_shape=x_train.shape[1:]))
    model.add(BatchNormalization())
    model.add(Activation('relu'))
    model.add(Conv2D(160, (1, 1), padding='same', kernel_regularizer=keras.regularizers.l2(weight_decay), \
                     kernel_initializer="he_normal"))
    model.add(BatchNormalization())
    model.add(Activation('relu'))
    model.add(Conv2D(96, (1, 1), padding='same', kernel_regularizer=keras.regularizers.l2(weight_decay), \
                     kernel_initializer="he_normal"))
    model.add(BatchNormalization())
    model.add(Activation('relu'))
    model.add(MaxPooling2D(pool_size=(3, 3), strides=(2, 2), padding='same'))
​
    model.add(Dropout(dropout))
​
    model.add(Conv2D(192, (5, 5), padding='same', kernel_regularizer=keras.regularizers.l2(weight_decay), \
                     kernel_initializer="he_normal"))
    model.add(BatchNormalization())
    model.add(Activation('relu'))
    model.add(Conv2D(192, (1, 1), padding='same', kernel_regularizer=keras.regularizers.l2(weight_decay), \
                     kernel_initializer="he_normal"))
    model.add(BatchNormalization())
    model.add(Activation('relu'))
    model.add(Conv2D(192, (1, 1), padding='same', kernel_regularizer=keras.regularizers.l2(weight_decay), \
                     kernel_initializer="he_normal"))
    model.add(BatchNormalization())
    model.add(Activation('relu'))
    model.add(MaxPooling2D(pool_size=(3, 3), strides=(2, 2), padding='same'))
​
    model.add(Dropout(dropout))
​
    model.add(Conv2D(192, (3, 3), padding='same', kernel_regularizer=keras.regularizers.l2(weight_decay), \
                     kernel_initializer="he_normal"))
    model.add(BatchNormalization())
    model.add(Activation('relu'))
    model.add(Conv2D(192, (1, 1), padding='same', kernel_regularizer=keras.regularizers.l2(weight_decay), \
                     kernel_initializer="he_normal"))
    model.add(BatchNormalization())
    model.add(Activation('relu'))
    model.add(Conv2D(10, (1, 1), padding='same', kernel_regularizer=keras.regularizers.l2(weight_decay), \
                     kernel_initializer="he_normal"))
    model.add(BatchNormalization())
    model.add(Activation('relu'))
​
    model.add(GlobalAveragePooling2D())
    model.add(Activation('softmax'))
​
    sgd = optimizers.SGD(lr=.1, momentum=0.9, nesterov=True)
    model.compile(loss='categorical_crossentropy', optimizer=sgd, metrics=['accuracy'])
    return model
​
if __name__ == '__main__':
    (x_train, y_train), (x_test, y_test) = cifar10.load_data()
    y_train = keras.utils.to_categorical(y_train, num_classes)
    y_test = keras.utils.to_categorical(y_test, num_classes)
​
    x_train, x_test = color_preprocessing(x_train, x_test)
    model = build_model()
    print(model.summary())
​
    tb_cb = TensorBoard(log_dir=log_filepath, histogram_freq=0)
    change_lr = LearningRateScheduler(scheduler)
    cbks = [change_lr, tb_cb]
​
    print('Using real-time data augmentation.')
    datagen = ImageDataGenerator(
                horizontal_flip=True, width_shift_range=0.125,
                height_shift_range=0.125, fill_mode='constant', cval=0.)
    datagen.fit(x_train)
​
    model.fit_generator(
        datagen.flow(x_train, y_train, batch_size=batch_size),
        steps_per_epoch=iterations, epochs=epochs,
        callbacks=cbks, validation_data=(x_test, y_test))
    model.save('nin_bn.h5')
```