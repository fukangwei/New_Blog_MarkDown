---
title: sklearn的数据预处理
categories: 机器学习
abbrlink: 54f839b6
date: 2019-02-12 12:04:27
---
&emsp;&emsp;`klearn.preprocessing`提供了各种公共函数，用于将`raw feature vector`转换成另外一种更适合评估器工作的格式。

### 标准化(Standardization)、平均移除法(mean removal)和方差归一化(variance scaling)

&emsp;&emsp;`scale`函数提供了一个快速而简单的方式：

``` python
>>> from sklearn import preprocessing
>>> import numpy as np
>>> X = np.array([[ 1., -1.,  2.],
...               [ 2.,  0.,  0.],
...               [ 0.,  1., -1.]])
>>> X_scaled = preprocessing.scale(X)
>>> X_scaled
array([[ 0.  ..., -1.22...,  1.33...],
       [ 1.22...,  0.  ..., -0.26...],
       [-1.22...,  1.22..., -1.06...]])
```

归一化后的数据其均值为`0`，方差为`1`：

``` python
>>> X_scaled.mean(axis=0)
array([ 0.,  0.,  0.])
>>> X_scaled.std(axis=0)
array([ 1.,  1.,  1.])
```

一般会把`train`和`test`集放在一起做标准化，或者在`train`集上做标准化后，用同样的标准化器去标准化`test`集，此时可以用`StandardScaler`：

``` python
>>> scaler = preprocessing.StandardScaler().fit(X)
>>> scaler
StandardScaler(copy=True, with_mean=True, with_std=True)
>>> scaler.mean_
array([ 1. ...,  0. ...,  0.33...])
>>> scaler.scale_
array([ 0.81...,  0.81...,  1.24...])
>>> scaler.transform(X)
array([[ 0.  ..., -1.22...,  1.33...],
       [ 1.22...,  0.  ..., -0.26...],
       [-1.22...,  1.22..., -1.06...]])
```

通过在`StandardScaler`的构造函数中设置`with_mean=False`或者`with_std=False`，可以禁止均值中心化(`centering`)和归一化(`scaling`)。

### 将feature归一化到一个范围内

&emsp;&emsp;另一种标准化方式是将`feature`归一化到给定的范围内(比如`[0, 1]`之间)，可以使用`MinMaxScaler`或者`MaxAbsScaler`函数。
&emsp;&emsp;归一化至`[0, 1]`的代码如下：

``` python
>>> X_train = np.array([[ 1., -1.,  2.],
...                     [ 2.,  0.,  0.],
...                     [ 0.,  1., -1.]])
>>> min_max_scaler = preprocessing.MinMaxScaler()
>>> X_train_minmax = min_max_scaler.fit_transform(X_train)
>>> X_train_minmax
array([[ 0.5       ,  0.        ,  1.        ],
       [ 1.        ,  0.5       ,  0.33333333],
       [ 0.        ,  1.        ,  0.        ]])
```

如果`MinMaxScaler`给出了显式的范围，例如`feature_range=(min, max)`，那么对应的公式为：

``` python
X_std = (X - X.min(axis=0)) / (X.max(axis=0) - X.min(axis=0))
X_scaled = X_std / (max - min) + min
```

`MaxAbsScaler`以类似的方式工作，它的归一化范围在`[-1, 1]`之间：

``` python
>>> X_train = np.array([[ 1., -1.,  2.],
...                     [ 2.,  0.,  0.],
...                     [ 0.,  1., -1.]])
>>> max_abs_scaler = preprocessing.MaxAbsScaler()
>>> X_train_maxabs = max_abs_scaler.fit_transform(X_train)
>>> X_train_maxabs
array([[ 0.5, -1. ,  1. ],
       [ 1. ,  0. ,  0. ],
       [ 0. ,  1. , -0.5]])
>>> X_test = np.array([[ -3., -1.,  4.]])
>>> X_test_maxabs = max_abs_scaler.transform(X_test)
>>> X_test_maxabs
array([[-1.5, -1. ,  2. ]])
>>> max_abs_scaler.scale_
array([ 2.,  1.,  2.])
```

### 正态分布化(Normalization)

&emsp;&emsp;`Normalization`用于将各个样本归一化为正态分布，函数`normalize`提供了这一功能：

``` python
>>> X = [[ 1., -1.,  2.],
...      [ 2.,  0.,  0.],
...      [ 0.,  1., -1.]]
>>> X_normalized = preprocessing.normalize(X, norm='l2')
>>> X_normalized
array([[ 0.40..., -0.40...,  0.81...],
       [ 1.  ...,  0.  ...,  0.  ...],
       [ 0.  ...,  0.70..., -0.70...]])
```

`Normalizer`类也可以实现这一功能：

``` python
>>> normalizer = preprocessing.Normalizer().fit(X)
>>> normalizer
Normalizer(copy=True, norm='l2')
>>> normalizer.transform(X)
array([[ 0.40..., -0.40...,  0.81...],
       [ 1.  ...,  0.  ...,  0.  ...],
       [ 0.  ...,  0.70..., -0.70...]])
>>> normalizer.transform([[-1.,  1., 0.]])
array([[-0.70...,  0.70...,  0.  ...]])
```

### 二值化(Binarization)

&emsp;&emsp;二值化可以将数值形(`numerical`)的`feature`进行阀值化：

``` python
>>> X = [[ 1., -1.,  2.],
...      [ 2.,  0.,  0.],
...      [ 0.,  1., -1.]]
>>> binarizer = preprocessing.Binarizer().fit(X)
>>> binarizer
Binarizer(copy=True, threshold=0.0)  # 调整binarizer的threshold
>>> binarizer.transform(X)
array([[ 1.,  0.,  1.],
       [ 1.,  0.,  0.],
       [ 0.,  1.,  0.]])
```

### 补充缺失值

&emsp;&emsp;现实世界中有许多数据集中包含着缺失值(`missing values`)，经常被编码成空格、`NaN`或者其它占位符。这样的数据集对于`sklearn`来说是不兼容的，因为它的输入数据必须是全是数值型的。
&emsp;&emsp;一个基本策略是使用不完整的数据，即抛弃掉那些带缺失值的行。然而，缺失的数据中也可能包含有价值的信息。一个更好地策略是补充缺失值，比如从已知的数据中去模拟它们。
&emsp;&emsp;`Imputer`类提供了基本策略来补充缺失值，或者使用均值、中值，或者是行中或列中最常用的值：

``` python
>>> import numpy as np
>>> from sklearn.preprocessing import Imputer
>>> imp = Imputer(missing_values='NaN', strategy='mean', axis=0)
>>> imp.fit([[1, 2], [np.nan, 3], [7, 6]])
Imputer(axis=0, copy=True, missing_values='NaN', strategy='mean', verbose=0)
>>> X = [[np.nan, 2], [6, np.nan], [7, 6]]
>>> print(imp.transform(X))
[[ 4.          2.      ]
 [ 6.          3.666...]
 [ 7.          6.      ]]
```

### 多项式特征生成

&emsp;&emsp;很多情况下，考虑输入数据中的非线性特征来增加模型的复杂性是非常有效的。一个简单常用的方法就是使用多项式特征，它能捕捉到特征中高阶和相互作用的项。`PolynomialFeatures`类可以实现该功能：

``` python
>>> import numpy as np
>>> from sklearn.preprocessing import PolynomialFeatures
>>> X = np.arange(6).reshape(3, 2)
>>> X
array([[0, 1],
       [2, 3],
       [4, 5]])
>>> poly = PolynomialFeatures(2)
>>> poly.fit_transform(X)
array([[  1.,   0.,   1.,   0.,   0.,   1.],
       [  1.,   2.,   3.,   4.,   6.,   9.],
       [  1.,   4.,   5.,  16.,  20.,  25.]])
```

特征向量`X`从`(X1, X2)`被转换成`(1, X1, X2, X1^2, X1*X2, X2^2)`。
&emsp;&emsp;在一些情况下，我们只需要特征中的相互作用项(`interaction terms`)，它可以通过传入参数`interaction_only=True`获得：

``` python
>>> X = np.arange(9).reshape(3, 3)
>>> X
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
>>> poly = PolynomialFeatures(degree=3, interaction_only=True)
>>> poly.fit_transform(X)
array([[   1.,    0.,    1.,    2.,    0.,    0.,    2.,    0.],
       [   1.,    3.,    4.,    5.,   12.,   15.,   20.,   60.],
       [   1.,    6.,    7.,    8.,   42.,   48.,   56.,  336.]])
```

特征向量`X`从`(X1, X2, X3)`被转换成`(1, X1, X2, X3, X1*X2, X1*X3, X2*X3, X1*X2*X3)`。

---

### one-hot编码

&emsp;&emsp;`One-Hot`编码又称为`一位有效编码`，主要是采用位状态寄存器来对个状态进行编码，每个状态都由他独立的寄存器位，并且在任意时候只有一位有效。
&emsp;&emsp;在实际的机器学习任务中，特征有时候并不总是连续值，有可能是一些分类值，例如性别可分为`male`和`female`。对于这样的特征，通常需要对其进行特征数字化：

- 性别：`[male, female]`
- 地区：`[Europe, US, Asia]`
- 浏览器：`[Firefox, Chrome, Safari, Internet Explorer]`

对于某一个样本，如`[male, US, Internet Explorer]`，我们需要将这个分类值的特征数字化，最直接的方法就是采用序列化的方式，即`[0, 1, 3]`。但是，这样的特征处理并不能直接放入机器学习算法中。
&emsp;&emsp;对于上述问题，`性别`属性是二维的，`地区`属性是三维的，而`浏览器`属性则是四维的。我们可以采用`One-Hot`编码的方式对上述的样本`[male, US, Internet Explorer]`进行编码，例如`male`对应`[1, 0]`，`US`对应`[0, 1, 0]`，`Internet Explorer`对应`[0, 0, 0, 1]`，则完整的特征数字化的结果为`[1, 0, 0, 1, 0, 0, 0, 0, 1]`。这样导致的一个结果就是数据会变得非常得稀疏。
&emsp;&emsp;可以这样理解：对于每一个特征，如果它有`m`个可能值，那么经过独热编码后，就变成了`m`个二元特征，并且这些特征互斥，每次只有一个激活，因此数据会变成稀疏的。这样做的好处主要有：解决了分类器不好处理属性数据的问题；一定程度上也起到了扩充特征的作用。
&emsp;&emsp;实际的`Python`代码如下：

``` python
from sklearn import preprocessing
​
enc = preprocessing.OneHotEncoder()
enc.fit([[0, 0, 3], [1, 1, 0], [0, 2, 1], [1, 0, 2]])
array = enc.transform([[0, 1, 3]]).toarray()
print(array)
```

执行结果：

``` python
[[1. 0. 0. 1. 0. 0. 0. 0. 1.]]
```

我们将矩阵排起来看：

``` python
[[ 0 0 3 ];
 [ 1 1 0 ];
 [ 0 2 1 ];
 [ 1 0 2 ]]
```

该矩阵的每一列代表一个特征：

- 第一列只有`0`或`1`出现，共有两种情况，所以`one-hot`编码前两维代表第一个特征，也恰好说明了是性别的分类。
- 第二列有`0`、`1`和`2`出现，共有三种情况，所以`one-hot`编码中间三维代表第二个特征，恰好证明了地区所对应的特征。
- 第三列有`0`、`1`、`2`和`3`出现，共有四种情况，所以`one-hot`编码最后四维代表第三个特征，也是最后一个特征，证明了浏览器的对应的特征。

这里也很好地解释了`一定程度上也起到了扩充特征的作用`这句话，其实就是将所有的特征都融入到一个向量里面构成`one-hot`意义下的特征，一下子变成了`9`维的向量。特征列如下：

``` python
[[male, female, Europe, US, Asia, Firefox, Chrome, Safari, Internet Explorer]]
```

输出结果如下：

``` python
[[ 1. 0. 0. 1. 0. 0. 0. 0. 1.]]
```