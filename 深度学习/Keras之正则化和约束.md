---
title: Keras之正则化和约束
categories: 深度学习
date: 2018-12-30 21:04:31
---
### 正则化

&emsp;&emsp;正则项在优化过程中对层的参数或层的激活值添加惩罚项，这些惩罚项将与损失函数一起作为网络的最终优化目标。惩罚项基于层进行惩罚，目前惩罚项的接口与层有关，但`Dense`、`Conv1D`、`Conv2D`和`Conv3D`具有共同的接口。<!--more-->
&emsp;&emsp;这些层有三个关键字参数以施加正则项：

- `kernel_regularizer`：施加在权重上的正则项，为`keras.regularizer.Regularizer`对象。
- `bias_regularizer`：施加在偏置向量上的正则项，为`keras.regularizer.Regularizer`对象。
- `activity_regularizer`：施加在输出上的正则项，为`keras.regularizer.Regularizer`对象。

``` python
from keras import regularizers
model.add(
    Dense(
        64, input_dim=64, kernel_regularizer=regularizers.l2(0.01),
        activity_regularizer=regularizers.l1(0.01)
    )
)
```

&emsp;&emsp;可用正则项：

- `keras.regularizers.l1(0.)`
- `keras.regularizers.l2(0.)`
- `keras.regularizers.l1_l2(0.)`

#### 开发新的正则项

&emsp;&emsp;任何以权重矩阵作为输入并返回单个数值的函数均可以作为正则项：

``` python
from keras import backend as K

def l1_reg(weight_matrix):
    return 0.01 * K.sum(K.abs(weight_matrix))

model.add(Dense(64, input_dim=64, kernel_regularizer=l1_reg)
```

另外，你也可以用面向对象的方式来写你的正则化器，例子见`https://github.com/keras-team/keras/blob/master/keras/regularizers.py`模块。

---

### 约束

&emsp;&emsp;来自`constraints`模块的函数在优化过程中为网络的参数施加约束(例如非负)。惩罚项基于层进行惩罚，目前惩罚项的接口与层有关，但`Dense`、`Conv1D`、`Conv2D`和`Conv3D`具有共同的接口。这些层通过以下关键字施加约束项：

- `kernel_constraint`：对主权重矩阵进行约束。
- `bias_constraint`：对偏置向量进行约束。

``` python
from keras.constraints import maxnorm
model.add(Dense(64, kernel_constraint=max_norm(2.)))
```

&emsp;&emsp;预定义约束项：

- `max_norm(m=2)`：最大范数约束。
- `non_neg()`：非负性约束。
- `unit_norm()`：单位范数约束，强制矩阵沿最后一个轴拥有单位范数。
- `min_max_norm(min_value=0.0, max_value=1.0, rate=1.0, axis=0)`：最小/最大范数约束。