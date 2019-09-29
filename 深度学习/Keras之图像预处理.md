---
title: Keras之图像预处理
categories: 深度学习
abbrlink: 68f5fea8
date: 2019-01-15 18:04:55
---
### ImageDataGenerator类

&emsp;&emsp;函数原型如下：

``` python
keras.preprocessing.image.ImageDataGenerator(
    featurewise_center=False, samplewise_center=False, featurewise_std_normalization=False,
    samplewise_std_normalization=False, zca_whitening=False, zca_epsilon=1e-06, rotation_range=0.0,
    width_shift_range=0.0, height_shift_range=0.0, brightness_range=None, shear_range=0.0,
    zoom_range=0.0, channel_shift_range=0.0, fill_mode='nearest', cval=0.0, horizontal_flip=False,
    vertical_flip=False, rescale=None, preprocessing_function=None, data_format=None, validation_split=0.0
)
```

通过实时数据增强生成张量图像数据批次，数据将不断循环。

- `featurewise_center`：布尔值，将输入数据的均值设置为`0`，逐特征进行。
- `samplewise_center`：布尔值，将每个样本的均值设置为`0`。
- `featurewise_std_normalization`：布尔值，将输入除以数据标准差，逐特征进行。
- `samplewise_std_normalization`：布尔值，将每个输入除以其标准差。
- `zca_epsilon`：`ZCA`白化的`epsilon`值，默认为`1e-6`。
- `zca_whitening`：布尔值，是否应用`ZCA`白化。
- `rotation_range`：整数，随机旋转的度数范围。
- `width_shift_range`：浮点数、一维数组或整数：

1. `float`：如果小于`1`，则是除以总宽度的值；如果大于等于`1`，则为像素值。
2. `一维数组`：数组中的随机元素。
3. `int`：来自间隔(`-width_shift_range, +width_shift_range`)之间的整数个像素。

`width_shift_range`为`2`时，可能值是整数`[-1, 0, +1]`，与`width_shift_range = [-1, 0, +1]`相同；而`width_shift_range`为`1.0`时，可能值是`[-1.0, +1.0)`之间的浮点数。

- `height_shift_range`：浮点数、一维数组或整数：

1. `float`：如果小于`1`，则是除以总宽度的值；如果大于等于`1`，则为像素值。
2. `一维数组`：数组中的随机元素。
3. `int`：来自间隔`(-height_shift_range, +height_shift_range)`之间的整数个像素。

`height_shift_range`为`2`时，可能值是整数`[-1, 0, +1]`，与`height_shift_range = [-1, 0, +1]`相同；而`height_shift_range`为`1.0`时，可能值是`[-1.0, +1.0)`之间的浮点数。

- `shear_range`：浮点数，剪切强度(以弧度逆时针方向剪切角度)。
- `zoom_range`：浮点数或`[lower, upper]`，随机缩放范围。如果是浮点数，`[lower, upper] = [1 - zoom_range, 1 + zoom_range]`。
- `channel_shift_range`：浮点数，随机通道转换的范围。
- `fill_mode`：`constant`、`nearest`、`reflect`和`wrap`之一。输入边界以外的点根据给定的模式填充：

选项       | 说明
-----------|-----
`constant` | <code>kkkkkkkk&#124;abcd&#124;kkkkkkkk(cval = k)</code>
`nearest`  | <code>aaaaaaaa&#124;abcd&#124;dddddddd</code>
`reflect`  | <code>abcddcba&#124;abcd&#124;dcbaabcd</code>
`wrap`     | <code>abcdabcd&#124;abcd&#124;abcdabcd</code>

- `cval`：浮点数或整数，用于边界之外的点的值。
- `horizontal_flip`：布尔值，随机水平翻转。
- `vertical_flip`：布尔值，随机垂直翻转。
- `rescale`：重缩放因子，默认为`None`。如果是`None`或`0`，则不进行缩放，否则将数据乘以所提供的值(在应用任何其他转换之前)。
- `preprocessing_function`：应用于每个输入的函数，这个函数会在任何其他改变之前运行。这个函数需要一个参数：一张图像(秩为`3`的`Numpy`张量)，并且应该输出一个同尺寸的`Numpy`张量。
- `data_format`：图像数据格式，`channels_first`或`channels_last`。`channels_last`模式表示图像输入尺寸应该为(`samples, height, width, channels`)；`channels_first`模式表示输入尺寸应该为(`samples, channels, height, width`)。默认为在`Keras`配置文件`~/.keras/keras.json`中的`image_data_format`值。如果你从未设置它，那它就是`channels_last`。
- `validation_split`：浮点数，保留用于验证的图像的比例(严格在`0`和`1`之间)。

&emsp;&emsp;使用`flow`的示例：

``` python
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
y_train = np_utils.to_categorical(y_train, num_classes)
y_test = np_utils.to_categorical(y_test, num_classes)
datagen = ImageDataGenerator(
    featurewise_center=True, featurewise_std_normalization=True, rotation_range=20,
    width_shift_range=0.2, height_shift_range=0.2, horizontal_flip=True)
datagen.fit(x_train)
​
# 使用实时数据增益的批数据对模型进行拟合
model.fit_generator(datagen.flow(x_train, y_train, batch_size=32),
                    steps_per_epoch=len(x_train) / 32, epochs=epochs)
​
for e in range(epochs):  # 这里有一个更“手动”的例子
    print('Epoch', e)
    batches = 0

    for x_batch, y_batch in datagen.flow(x_train, y_train, batch_size=32):
        model.fit(x_batch, y_batch)
        batches += 1

        if batches >= len(x_train) / 32:
            break  # 我们需要手动打破循环，因为生成器会无限循环
```

&emsp;&emsp;使用`flow_from_directory`的示例：

``` python
train_datagen = ImageDataGenerator(rescale=1. / 255, shear_range=0.2,
                                   zoom_range=0.2, horizontal_flip=True)
test_datagen = ImageDataGenerator(rescale=1. / 255)
train_generator = train_datagen.flow_from_directory(
    'data/train', target_size=(150, 150), batch_size=32, class_mode='binary')
validation_generator = test_datagen.flow_from_directory(
    'data/validation', target_size=(150, 150), batch_size=32, class_mode='binary')
model.fit_generator(train_generator, steps_per_epoch=2000, epochs=50,
                    validation_data=validation_generator, validation_steps=800)
```

&emsp;&emsp;同时转换图像和蒙版(`mask`)的示例：

``` python
data_gen_args = dict(featurewise_center=True, featurewise_std_normalization=True,
                     rotation_range=90., width_shift_range=0.1,
                     height_shift_range=0.1, zoom_range=0.2)
# 创建两个相同参数的实例
image_datagen = ImageDataGenerator(**data_gen_args)
mask_datagen = ImageDataGenerator(**data_gen_args)
​
# 为fit和flow函数提供相同的种子和关键字参数
seed = 1
image_datagen.fit(images, augment=True, seed=seed)
mask_datagen.fit(masks, augment=True, seed=seed)
​
image_generator = image_datagen.flow_from_directory('data/images', class_mode=None, seed=seed)
mask_generator = mask_datagen.flow_from_directory('data/masks', class_mode=None, seed=seed)
​
# 将生成器组合成一个产生图像和蒙版(mask)的生成器
train_generator = zip(image_generator, mask_generator)
model.fit_generator(train_generator, steps_per_epoch=2000, epochs=50)
```

### fit

&emsp;&emsp;将数据生成器用于某些示例数据：

``` python
keras.preprocessing.image.fit(x, augment=False, rounds=1, seed=None)
```

它基于一组样本数据，计算与数据转换相关的内部数据统计。当且仅当`featurewise_center`、`featurewise_std_normalization`或`zca_whitening`设置为`True`时才需要。

- `x`：样本数据，秩应该为`4`。对于灰度数据，通道轴的值应该为`1`；对于`RGB`数据，值应该为`3`。
- `augment`：布尔值，是否使用随机样本扩张。
- `rounds`：整数。如果使用数据增强(`augment=True`)，表明在数据上进行多少次增强。
- `seed`：整数，随机种子。

### flow

&emsp;&emsp;采集数据和标签数组，生成批量增强数据：

``` python
keras.preprocessing.image.flow(
    x, y=None, batch_size=32, shuffle=True, sample_weight=None, seed=None,
    save_to_dir=None, save_prefix='', save_format='png', subset=None)
```

- `x`：输入数据，秩为`4`的`Numpy`矩阵或元组。如果是元组，第一个元素应该包含图像，第二个元素是另一个`Numpy`数组或一列`Numpy`数组，它们不经过任何修改就传递给输出。可用于将模型杂项数据与图像一起输入。对于灰度数据，图像数组的通道轴的值应该为`1`，而对于`RGB`数据，其值应该为`3`。
- `y`：标签。
- `batch_size`：整数。
- `shuffle`：布尔值。
- `sample_weight`：样本权重。
- `seed`：整数。
- `save_to_dir`：`None`或字符串。这使您可以选择指定要保存的正在生成的增强图片的目录(用于可视化您正在执行的操作)。
- `save_prefix`：字符串，保存图片的文件名前缀(仅当`save_to_dir`设置时可用)。
- `save_format`：`png`和`jpeg`之一(仅当`save_to_dir`设置时可用)。
- `subset`：数据子集(`training`或`validation`)，如果在`ImageDataGenerator`中设置了`validation_split`。

该函数返回一个生成元组(`x, y`)的`Iterator`，其中`x`是图像数据的`Numpy`数组(在单张图像输入时)，或`Numpy`数组列表(在多张图像输入时)，`y`是对应标签的`Numpy`数组。如果`sample_weight`不是`None`，生成的元组形式为(`x, y, sample_weight`)。如果`y`是`None`，只有`Numpy`数组`x`被返回。

### flow_from_directory

&emsp;&emsp;函数原型如下：

``` python
keras.preprocessing.image.flow_from_directory(
    directory, target_size=(256, 256), color_mode='rgb', classes=None,
    class_mode='categorical', batch_size=32, shuffle=True, seed=None,
    save_to_dir=None, save_prefix='', save_format='png',
    follow_links=False, subset=None, interpolation='nearest')
```

- `directory`：目标目录的路径，每个类应该包含一个子目录。任何在子目录树下的`PNG`、`JPG`、`BMP`、`PPM`或`TIF`图像，都将被包含在生成器中。
- `target_size`：整数元组(`height, width`)，所有的图像将被调整到的尺寸。
- `color_mode`：`grayscale`和`rbg`之一，图像是否被转换成`1`或`3`个颜色通道。
- `classes`：可选的类的子目录列表(例如`['dogs', 'cats']`)。如果未提供，类的列表将自动从`directory`下的子目录名称或结构中推断出来，其中每个子目录都将被作为不同的类(类名将按字典序映射到标签的索引)。包含从类名到类索引的映射的字典可以通过`class_indices`属性获得。
- `class_mode`：`categorical`、`binary`、`sparse`、`input`或`None`之一，决定返回的标签数组的类型：`categorical`是`2`维`one-hot`编码标签，`binary`是一维二进制标签，`sparse`是一维整数标签，`input`是与输入图像相同的图像(主要用于自动编码器)。如果为`None`，则不返回标签(生成器将只产生批量的图像数据，对于`model.predict_generator`、`model.evaluate_generator`等很有用)。请注意，如果`class_mode`为`None`，那么数据仍然需要驻留在`directory`的子目录中才能正常工作。
- `batch_size`：一批数据的大小。
- `shuffle`：是否混洗数据。
- `seed`：随机种子，用于混洗和转换。
- `save_to_dir`：`None`或字符串。这使你可以最佳地指定正在生成的增强图片要保存的目录(用于可视化你在做什么)。
- `save_prefix`：字符串，保存图片的文件名前缀(仅当`save_to_dir`设置时可用)。
- `save_format`：`png`和`jpeg`之一(仅当`save_to_dir`设置时可用)。
- `follow_links`：是否跟踪类子目录中的符号链接(默认为`False`)。
- `subset`：数据子集(`training`或`validation`)，如果在`ImageDataGenerator`中设置了`validation_split`。
- `interpolation`：如果目标尺寸与加载图像的尺寸不同，则使用插值方法重新采样图像。支持的方法有`nearest`、`bilinear`和`bicubic`。如果安装了`1.1.3`以上版本的`PIL`，还支持`lanczos`。如果安装了`3.4.0`以上版本的`PIL`，还支持`box`和`hamming`。

该函数返回一个生成(`x, y`)元组的`DirectoryIterator`，其中`x`是一个包含一批尺寸为(`batch_size, *target_size, channels`)的图像的`Numpy`数组，`y`是对应标签的`Numpy`数组。

### get_random_transform

&emsp;&emsp;为转换生成随机参数：

``` python
keras.preprocessing.image.get_random_transform(img_shape, seed=None)
```

参数`seed`是随机种子；`img_shape`是整数元组，被转换的图像的尺寸。

### random_transform

&emsp;&emsp;将随机变换应用于图像：

``` python
keras.preprocessing.image.random_transform(x, seed=None)
```

参数`x`是`3D`张量，即单张图像；参数`seed`是随机种子。

### standardize

&emsp;&emsp;将标准化配置应用于一批输入：

``` python
keras.preprocessing.image.standardize(x)
```

参数`x`是需要标准化的一批输入。