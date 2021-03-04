---
title: Keras之预训练模型
categories: 深度学习
date: 2019-01-15 11:39:13
---
&emsp;&emsp;`Keras`的`Application`模块提供了带有预训练权重的`Keras`模型，这些模型可以用来进行预测、特征提取和`finetune`。模型的预训练权重将下载到`~/.keras/models/`，并在载入模型时自动载入。<!--more-->
&emsp;&emsp;在`ImageNet`上预训练过的用于图像分类的模型主要有：`Xception`、`VGG16`、`VGG19`、`ResNet50`、`InceptionV3`、`InceptionResNetV2`、`MobileNet`、`DenseNet`和`NASNet`。所有的这些模型(除了`Xception`和`MobileNet`)都兼容`Theano`和`Tensorflow`，并会基于`~/.keras/keras.json`自动设置`Keras`的图像维度。例如，如果你设置`data_format="channel_last"`，则加载的模型将按照`TensorFlow`的维度顺序来构造，即`[Width, Height, Depth]`的顺序
&emsp;&emsp;`Xception`模型仅在`TensorFlow`下可用，因为它依赖的`SeparableConvolution`层仅在`TensorFlow`可用。`MobileNet`仅在`TensorFlow`下可用，因为它依赖的`DepethwiseConvolution`层仅在`TensorFlow`下可用。模型信息如下：

模型               | 大小     | Top1准确率 | Top5准确率 | 参数数目       | 深度
-------------------|---------|------------|-----------|---------------|-----
`Xception`         | `88MB`  | `0.790`    | `0.945`   | `22,910,480`  | `126`
`VGG16`            | `528MB` | `0.715`    | `0.901`   | `138,357,544` | `23`
`VGG19`            | `549MB` | `0.727`    | `0.910`   | `143,667,240` | `26`
`ResNet50`         | `99MB`  | `0.759`    | `0.929`   | `25,636,712`  | `168`
`InceptionV3`      | `92MB`  | `0.788`    | `0.944`   | `23,851,784`  | `159`
`IncetionResNetV2` | `215MB` | `0.804`    | `0.953`   | `55,873,736`  | `572`
`MobileNet`        | `17MB`  | `0.665`    | `0.871`   | `4,253,864`   | `88`

### Xception模型

&emsp;&emsp;函数原型如下：

``` python
keras.applications.xception.Xception(
    include_top=True, weights='imagenet', input_tensor=None,
    input_shape=None, pooling=None, classes=1000)
```

这是在`ImageNet`上预训练的`Xception V1`模型。注意，该模型目前仅能以`TensorFlow`为后端使用，因为它依赖于`SeparableConvolution`层。目前该模型只支持`channels_last`的维度顺序(`width, height, channels`)。默认输入图片大小为`299 * 299`。

- `include_top`：是否保留顶层的全连接网络。
- `weights`：`None`代表随机初始化，即不加载预训练权重；`imagenet`代表加载预训练权重。
- `input_tensor`：可填入`Keras tensor`作为模型的输入(比如`layers.Input`输出的`tensor`)。
- `input_shape`：可选，仅当`include_top = False`有效，不然输入形状必须是(`299, 299, 3`)，因为预训练模型是以这个大小训练的。输入尺寸必须是三个数字，且宽高必须不小于`71`，比如(`150, 150, 3`)是一个合法的输入尺寸。
- `pooling`：当`include_top = False`时，该参数指定了特征提取时的池化方式：

1. `None`：代表不池化，直接输出最后一层卷积层的输出，该输出是一个四维张量。
2. `avg`：代表全局平均池化(`GLobalAveragePool2D`)，相当于在最后一层卷积层后面再加一层全局平均池化层，输出是一个二维张量。
3. `max`：代表全局最大池化。

- `classes`：可选，图片分类的类别数，仅当`include_top = True`并且不加载预训练权重时可用。

### VGG16模型

&emsp;&emsp;函数原型如下：

``` python
keras.applications.vgg16.VGG16(
    include_top=True, weights='imagenet', input_tensor=None,
    input_shape=None, pooling=None, classes=1000)
```

`VGG16`模型，权重由`ImageNet`训练而来。该模型在`Theano`和`TensorFlow`后端均可使用，并接受`channels_first`和`channels_last`两种输入维度顺序。模型的默认输入尺寸是`224 * 224`。

- `input_shape`：可选，仅当`include_top = False`有效，不然输入形状必须是(`224, 224, 3`)，因为预训练模型是以这个大小训练的。输入尺寸必须是三个数字，且宽高必须不小于`48`，比如(`200, 200, 3`)是一个合法的输入尺寸。

### VGG19模型

&emsp;&emsp;函数原型如下：

``` python
keras.applications.vgg19.VGG19(
    include_top=True, weights='imagenet', input_tensor=None,
    input_shape=None, pooling=None, classes=1000)
```

`VGG19`模型，权重由`ImageNet`训练而来。该模型在`Theano`和`TensorFlow`后端均可使用，并接受`channels_first`和`channels_last`两种输入维度顺序。模型的默认输入尺寸是`224 * 224`。

### ResNet50模型

&emsp;&emsp;函数原型如下：

``` python
keras.applications.resnet50.ResNet50(
    include_top=True, weights='imagenet', input_tensor=None,
    input_shape=None, pooling=None, classes=1000)
```

`50`层残差网络模型，权重训练自`ImageNet`。该模型在`Theano`和`TensorFlow`后端均可使用，并接受`channels_first`和`channels_last`两种输入维度顺序。模型的默认输入尺寸是`224 * 224`。

### InceptionV3模型

&emsp;&emsp;函数原型如下：

``` python
keras.applications.inception_v3.InceptionV3(
    include_top=True, weights='imagenet', input_tensor=None,
    input_shape=None, pooling=None, classes=1000)
```

`InceptionV3`网络，权重训练自`ImageNet`。该模型在`Theano`和`TensorFlow`后端均可使用，并接受`channels_first`和`channels_last`两种输入维度顺序。模型的默认输入尺寸是`299 * 299`。

### InceptionResNetV2模型

&emsp;&emsp;函数原型如下：

``` python
keras.applications.inception_resnet_v2.InceptionResNetV2(
    include_top=True, weights='imagenet', input_tensor=None,
    input_shape=None, pooling=None, classes=1000)
```

`InceptionResNetV2`网络，权重训练自`ImageNet`。该模型在`Theano`、`TensorFlow`和`CNTK`后端均可使用，并接受`channels_first`和`channels_last`两种输入维度顺序。模型的默认输入尺寸是`299 * 299`。

### MobileNet模型

&emsp;&emsp;函数原型如下：

``` python
keras.applications.mobilenet.MobileNet(
    input_shape=None, alpha=1.0, depth_multiplier=1, dropout=1e-3, include_top=True,
    weights='imagenet', input_tensor=None, pooling=None, classes=1000)
```

`MobileNet`网络，权重训练自`ImageNet`。该模型仅在`TensorFlow`后端均可使用，因此仅`channels_last`维度顺序可用。当需要以`load_model`加载`MobileNet`时，需要在`custom_object`中传入`relu6`和`DepthwiseConv2D`：

``` python
model = load_model(
    'mobilenet.h5',
    custom_objects={'relu6': mobilenet.relu6,
                    'DepthwiseConv2D': mobilenet.DepthwiseConv2D})
```

模型的默认输入尺寸是`224 * 224`。

- `input_shape`：可选，仅当`include_top = False`有效，不然输入形状必须是(`224, 224, 3`)，因为预训练模型是以这个大小训练的。输入尺寸必须是三个数字，且宽高必须不小于`32`，比如(`200, 200, 3`)是一个合法的输入尺寸。
- `alpha`：控制网络的宽度：

1. 如果`alpha < 1`，则同比例的减少每层的滤波器个数。
2. 如果`alpha > 1`，则同比例增加每层的滤波器个数。
3. 如果`alpha = 1`，使用默认的滤波器个数。

- `depth_multiplier`：`depthwise`卷积的深度乘子，也称为分辨率乘子。
- `dropout`：`dropout`比例。

---

### 如何“冻结”网络的层？

&emsp;&emsp;`冻结`一个层指的是该层将不参加网络训练，即该层的权重永不会更新，在进行`fine-tune`时经常会需要这项操作。在使用固定的`embedding`层处理文本输入时，也需要这个技术。
&emsp;&emsp;可以通过向层的构造函数传递`trainable`参数来指定一个层是不是可训练的：

``` python
frozen_layer = Dense(32, trainable=False)
```

此外，也可以通过将层对象的`trainable`属性设为`True`或`False`来为已经搭建好的模型设置要冻结的层。在设置完后，需要运行`compile`来使设置生效：

``` python
x = Input(shape=(32,))
layer = Dense(32)
layer.trainable = False
y = layer(x)

frozen_model = Model(x, y)
# in the model below, the weights of layer will not be updated during training
frozen_model.compile(optimizer='rmsprop', loss='mse')

layer.trainable = True
trainable_model = Model(x, y)
# with this model the weights of the layer will be updated during training
# (which will also affect the above model since it uses the same layer instance)
trainable_model.compile(optimizer='rmsprop', loss='mse')

frozen_model.fit(data, labels)  # this does NOT update the weights of layer
trainable_model.fit(data, labels)  # this updates the weights of layer
```