---
title: TensorFlow的运行方式
categories: 深度学习
abbrlink: 20514fba
date: 2019-01-15 14:05:11
---
&emsp;&emsp;`TensorFlow`的运行方式分如下`4`步：<!--more-->

- 加载数据以及定义超参数。
- 构建网络。
- 训练模型。
- 评估模型和进行预测。

下面以一个神经网络为例，讲解`TensorFlow`的运行方式。在这个例子中，我们构造一个满足一元二次函数`y = ax^2 + b`的原始数据，然后构造一个最简单的神经网络(仅包含一个输入层、一个隐藏层和一个输出层)。通过`TensorFlow`将隐藏层和输出层的`weights`和`biases`的值学习出来，看看随着训练的增加，损失值是不是在不断地减小。

### 生成以及加载数据

&emsp;&emsp;首先来生成输入数据，假设最后要学习的方程为`y = x^2 - 0.5`，我们来构造满足这个方程的一堆`x`和`y`，同时加上一些不满足方程的噪声点：

``` python
import tensorflow as tf
import numpy as np

# 为了使点更密集一些，我们构建了300个点，分布在“-1”到1区间，直接采用np生成
# 等差数列的方法，并将结果为300个点的一维数组，转换为“300 * 1”的二维数组
x_data = np.linspace(-1, 1, 300)[:, np.newaxis]
# 加入一些噪声点，使它与x_data的维度一致，并且均为均值为0，方差为0.05的正态分布
noise = np.random.normal(0, 0.05, x_data.shape)
y_data = np.square(x_data) - 0.5 + noise  # y = x^2 - 0.5 + 噪声
```

接下来定义`x`和`y`的占位符来作为将要输入神经网络的变量：

``` python
xs = tf.placeholder(tf.float32, [None, 1])
ys = tf.placeholder(tf.float32, [None, 1])
```

### 构建网络模型

&emsp;&emsp;我们这里需要构建一个隐藏层和一个输出层。作为神经网络中的层，输入的参数应该有`4`个变量：输入数据、输入数据的维度、输出数据的维度和激活函数。每一层经过向量化(`y = weights * x + biases`)的处理，并且经过激活函数的非线性处理后，最终得到输出数据。
&emsp;&emsp;接下来定义隐藏层和输出层：

``` python
def add_layer(inputs, in_size, out_size, activation_function=None):
    # 构建权重：“in_size * out_size”大小的矩阵
    weights = tf.Variable(tf.random_normal([in_size, out_size]))
    # 构建偏置：“1 * out_size”的矩阵
    biases = tf.Variable(tf.zeros([1, out_size]) + 0.1)
    Wx_plus_b = tf.matmul(inputs, weights) + biases  # 矩阵相乘
​
    if activation_function is None:
        out_puts = Wx_plus_b
    else:
        out_puts = activation_function(Wx_plus_b)
​
    return out_puts  # 输出得到的数据
​
# 构建隐藏层，假设隐藏层有20个神经元
h1 = add_layer(xs, 1, 20, activation_function=tf.nn.relu)
# 构建输出层，假设输出层和输入层一样，有一个神经元
prediction = add_layer(h1, 20, 1, activation_function=None)
```

&emsp;&emsp;接下来需要构建损失函数：计算输出层的预测值和真实值间的误差，对二者差的平方求和，然后再取平均，得到损失函数。运用梯度下降法，以`0.1`的效率最小化损失：

``` python
 # 计算预测值和真实值间的误差
loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys - prediction), reduction_indices=[1]))
train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)
```

### 训练模型

&emsp;&emsp;我们让`TensorFlow`训练`1000`次，每`50`次输出训练的损失值：

``` python
init = tf.global_variables_initializer()
sess = tf.Session()
sess.run(init)
​
for i in range(1000):
    sess.run(train_step, feed_dict={xs: x_data, ys: y_data})

    if i % 50 == 0:  # 每50次打印出一次损失值
        print(sess.run(loss, feed_dict={xs: x_data, ys: y_data}))
```

&emsp;&emsp;以上就是最简单的利用`TensorFlow`的神经网络训练一个模型的过程，目标就是要训练出权重值来使模型拟合`y = x^2 - 0.5`的系数`1`和`-0.5`。通过损失值越来越小的现象，可以看出训练的参数越来越逼近目标结果。