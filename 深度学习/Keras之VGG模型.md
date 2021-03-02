---
title: Keras之VGG模型
categories: 深度学习
date: 2018-12-03 19:10:57
---
&emsp;&emsp;查看`VGG16`的模型结构：<!--more-->

``` python
from keras.applications.vgg16 import VGG16

model = VGG16(include_top=True, weights='imagenet', input_tensor=None, input_shape=None)
print(model.summary())
```

执行结果：

``` bash
Layer (type)                 Output Shape              Param
=================================================================
input_1 (InputLayer)         (None, 224, 224, 3)       0
block1_conv1 (Conv2D)        (None, 224, 224, 64)      1792
block1_conv2 (Conv2D)        (None, 224, 224, 64)      36928
block1_pool (MaxPooling2D)   (None, 112, 112, 64)      0
block2_conv1 (Conv2D)        (None, 112, 112, 128)     73856
block2_conv2 (Conv2D)        (None, 112, 112, 128)     147584
block2_pool (MaxPooling2D)   (None, 56, 56, 128)       0
block3_conv1 (Conv2D)        (None, 56, 56, 256)       295168
block3_conv2 (Conv2D)        (None, 56, 56, 256)       590080
block3_conv3 (Conv2D)        (None, 56, 56, 256)       590080
block3_pool (MaxPooling2D)   (None, 28, 28, 256)       0
block4_conv1 (Conv2D)        (None, 28, 28, 512)       1180160
block4_conv2 (Conv2D)        (None, 28, 28, 512)       2359808
block4_conv3 (Conv2D)        (None, 28, 28, 512)       2359808
block4_pool (MaxPooling2D)   (None, 14, 14, 512)       0
block5_conv1 (Conv2D)        (None, 14, 14, 512)       2359808
block5_conv2 (Conv2D)        (None, 14, 14, 512)       2359808
block5_conv3 (Conv2D)        (None, 14, 14, 512)       2359808
block5_pool (MaxPooling2D)   (None, 7, 7, 512)         0
flatten (Flatten)            (None, 25088)             0
fc1 (Dense)                  (None, 4096)              102764544
fc2 (Dense)                  (None, 4096)              16781312
predictions (Dense)          (None, 1000)              4097000
=================================================================
Total params: 138,357,544
Trainable params: 138,357,544
Non-trainable params: 0
-----------------------------------------------------------------
None
```

&emsp;&emsp;还可以将神经网络结构保存为图片：

``` python
from keras.applications.vgg16 import VGG16
from keras.utils import plot_model

model = VGG16(weights='imagenet', include_top=False)
print(model.summary())
plot_model(model, to_file='a simple convnet.png')
```

&emsp;&emsp;使用`VGG16`模型对图片进行预测：

``` python
from keras.applications.vgg16 import VGG16, preprocess_input, decode_predictions
from keras.preprocessing import image
import numpy as np
import sys

filename = "cat.jpg"

model = VGG16(weights='imagenet')
img = image.load_img(filename, target_size=(224, 224))
x = image.img_to_array(img)

# 将3维张量(rows, cols, channels)转换为4维张量(samples, rows, cols, channels)
# samples等于1，是因为只有一个输入图像
x = np.expand_dims(x, axis=0)
preds = model.predict(preprocess_input(x))
results = decode_predictions(preds, top=5)[0]

for result in results:
    print(result)
```

执行结果：

``` bash
('n02124075', 'Egyptian_cat', 0.40413904)
('n02123159', 'tiger_cat', 0.286398)
('n02123045', 'tabby', 0.17900038)
('n02127052', 'lynx', 0.027170168)
('n02971356', 'carton', 0.011312529)
```

&emsp;&emsp;从`VGG16`的任意中间层中抽取特征：

``` python
from keras.applications.vgg16 import VGG16
from keras.preprocessing import image
from keras.applications.vgg16 import preprocess_input
from keras.models import Model
import numpy as np

base_model = VGG16(weights='imagenet')
model = Model(inputs=base_model.input, outputs=base_model.get_layer('block4_pool').output)
img_path = 'cat.jpg'
img = image.load_img(img_path, target_size=(224, 224))
x = image.img_to_array(img)
x = np.expand_dims(x, axis=0)
x = preprocess_input(x)
block4_pool_features = model.predict(x)
print(block4_pool_features.shape)  # 输出“(1, 14, 14, 512)”
```

&emsp;&emsp;**补充说明**：`preprocess_input`函数完成数据预处理的工作，它能够提高算法的运行效果，常用的预处理包括数据归一化和白化(`whitening`)。