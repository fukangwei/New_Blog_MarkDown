---
title: Keras之常用数据集
date: 2019-01-01 15:49:45
categories: 深度学习
---
### CIFAR10小图像分类数据集

&emsp;&emsp;`50000`张`32*32`彩色训练图像数据，以及`10000`张测试图像数据，总共分为`10`个类别：

``` python
from keras.datasets import cifar10
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
```

该函数返回`2`个元组：

- `(x_train, x_test)`：`uint8`数组表示的`RGB`图像数据，尺寸为`(num_samples, 3, 32, 32)`。
- `(y_train, y_test)`：`uint8`数组表示的类别标签(范围在`0`至`9`之间的整数)，尺寸为`(num_samples,)`。

### CIFAR100小图像分类数据集

&emsp;&emsp;该数据库具有`50000`个`32*32`的彩色图片作为训练集，`10000`个图片作为测试集。图片一共有`100`个类别，每个类别有`600`张图片。这`100`个类别又分为`20`个大类：

``` python
from keras.datasets import cifar100
(x_train, y_train), (x_test, y_test) = cifar100.load_data(label_mode='fine')
```

该函数返回`2`个元组：

- `(x_train, x_test)`：`uint8`数组表示的`RGB`图像数据，尺寸为`(num_samples, 3, 32, 32)`。
- `(y_train, y_test)`：`uint8`数组表示的类别标签(范围在`0`至`99`之间的整数)，尺寸为`(num_samples,)`。

参数`label_mode`为`fine`或`coarse`之一，用于控制标签的精细度。`fine`获得的标签是`100`个小类的标签，`coarse`获得的标签是`20`个大类的标签。

### IMDB电影评论情感分类数据集

&emsp;&emsp;数据集来自`IMDB`的`25000`条电影评论，以情绪(正面/负面)标记。每一条评论已经过预处理，并编码为词索引(整数)的序列表示。为了方便起见，将词按数据集中出现的频率进行索引，例如整数`3`编码数据中第三个最频繁的词。这允许快速筛选操作，例如只考虑前`10000`个最常用的词，但排除前`20`个最常见的词。
&emsp;&emsp;作为惯例，`0`不代表特定的单词，而是被用于编码任何未知单词：

``` python
from keras.datasets import imdb
(x_train, y_train), (x_test, y_test) = imdb.load_data(
    path="imdb.npz", num_words=None, skip_top=0, maxlen=None,
    seed=113, start_char=1, oov_char=2, index_from=3)
```

该函数返回`2`个元组：

- `(x_train, x_test)`：序列的列表，即词索引的列表。如果指定了`num_words`参数，则可能的最大索引值是`(num_words - 1)`；如果指定了`maxlen`参数，则可能的最大序列长度为`maxlen`。
- `(y_train, y_test)`：序列的标签，是一个二值`list`。

参数如下：

- `path`：如果你在本机上已有此数据集(位于`~/.keras/datasets/ + path`)，则载入；否则数据将下载到该目录下。
- `num_words`：整数或`None`，要考虑的最常用的词语。任何不太频繁的词将在序列数据中显示为`oov_char`值。
- `skip_top`：整数，忽略最常出现的若干单词，这些单词将会被编码为`oov_char`的值。
- `maxlen`：整数，最大序列长度，任何更长的序列都将被截断。
- `seed`：整数，用于数据重排的随机数种子。
- `start_char`：整数，序列的开始将用这个字符标记，默认设置为`1`，因为`0`通常作为填充字符。
- `oov_char`：整数，由于`num_words`或`skip_top`限制而被删除的单词将被替换为此字符。
- `index_from`：整数，真实的单词(而不是类似于`start_char`的特殊占位符)将从这个下标开始。

### 路透社新闻主题分类

&emsp;&emsp;数据集来源于路透社的`11228`条新闻文本，总共分为`46`个主题。与`IMDB`数据集一样，每条新闻被编码为一个词下标的序列：

``` python
from keras.datasets import reuters
(x_train, y_train), (x_test, y_test) = reuters.load_data(
    path="reuters.npz", num_words=None, skip_top=0, maxlen=None,
    test_split=0.2, seed=113, start_char=1, oov_char=2, index_from=3)
```

参数的含义与`IMDB`同名参数相同，唯一多的参数是`test_split`，用于指定从原数据中分割出作为测试集的比例。该数据库支持获取用于编码序列的词下标：

``` python
word_index = reuters.get_word_index(path="reuters_word_index.json")
```

对于参数`path`，如果你在本机上已有此数据集(位于`~/.keras/datasets/ + path`)，则载入；否则数据将下载到该目录下。上面代码的返回值是一个以单词为关键字，以其下标为值的字典。例如，`word_index['giraffe']`的值可能为`1234`。

### MNIST手写字符数据集

&emsp;&emsp;训练集为`60000`张`28*28`像素灰度图像，测试集为`10000`张同规格图像，总共`10`类数字标签：

``` python
from keras.datasets import mnist
(x_train, y_train), (x_test, y_test) = mnist.load_data()
```

该函数返回两个`Tuple`：

- `(x_train, x_test)`：形状是`(nb_samples, 28, 28)`的灰度图片数据，数据类型是无符号8位整形(`uint8`)。
- `(y_train, y_test)`：形状是`(nb_samples,)`的标签数据，标签的范围是`0`至`9`。

### Fashion-MNIST时尚物品数据集

&emsp;&emsp;训练集为`60000`张`28*28`像素灰度图像，测试集为`10000`同规格图像，总共`10`类时尚物品标签。该数据集可以用作`MNIST`的直接替代品。类别标签如下：

类别 | 描述          | 中文
----|---------------|----
`0` | `T-shirt/top` | `T恤/上衣`
`1` | `Trouser`     | `裤子`
`2` | `Pullover`    | `套头衫`
`3` | `Dress`       | `连衣裙`
`4` | `Coat`        | `外套`
`5` | `Sandal`      | `凉鞋`
`6` | `Shirt`       | `衬衫`
`7` | `Sneaker`     | `运动鞋`
`8` | `Bag`         | `背包`
`9` | `Ankle boot`  | `短靴`

用法如下：

``` python
from keras.datasets import fashion_mnist
(x_train, y_train), (x_test, y_test) = fashion_mnist.load_data()
```

该函数返回`2`个元组：

- `(x_train, x_test)`：`uint8`数组表示的灰度图像，尺寸为`(num_samples, 28, 28)`。
- `(y_train, y_test)`：`uint8`数组表示的数字标签(范围在`0`至`9`之间的整数)，尺寸为`(num_samples,)`。

### Boston房价回归数据集

&emsp;&emsp;数据集来自卡内基梅隆大学维护的`StatLib`库。样本包含`1970`年代的在波士顿郊区不同位置的房屋信息，总共有`13`种房屋属性。目标值是一个位置的房屋的中值(单位是`k$`)：

``` python
from keras.datasets import boston_housing
(x_train, y_train), (x_test, y_test) = boston_housing.load_data()
```