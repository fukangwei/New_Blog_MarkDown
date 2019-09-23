---
title: 深度学习实现XOR
date: 2019-01-01 21:17:59
categories: 深度学习
---
&emsp;&emsp;单层感知器不能解决异或问题是神经网络的一个常识，而简单的两层神经网络却能将其轻易解决。
&emsp;&emsp;`import`需要的模块：

``` python
import numpy as np
import tensorflow as tf
```

&emsp;&emsp;输入数据的`placeholder`：包含一个`data`(训练数据)和一个`label`(训练标签)：

``` python
data = tf.placeholder(tf.float32, shape=(4, 2))
label = tf.placeholder(tf.float32, shape=(4, 1))
```

由于异或只有`4`种输入和对应的`4`个输出，所以定义固定的`shape`。
&emsp;&emsp;基于输入数据的`placeholder`构建`model`：异或需要两层神经网络，每层分别需要一个`weight`和一个`bias`：

``` python
with tf.variable_scope('layer1') as scope:
    weight = tf.get_variable(name='weight', shape=(2, 2))
    bias = tf.get_variable(name='bias', shape=(2,))
    x = tf.nn.sigmoid(tf.matmul(data, weight) + bias)

with tf.variable_scope('layer2') as scope:
    weight = tf.get_variable(name='weight', shape=(2, 1))
    bias = tf.get_variable(name='bias', shape=(1,))
    x = tf.matmul(x, weight) + bias
```

因为后面的`loss`要使用`sigmoid_cross_entropy_with_logits`函数，所以第二层网络的输出没有使用`sigmoid`函数。
&emsp;&emsp;定义`loss`：其实这里可以灵活地选用各种`loss`函数，例如`MSE`。这里还是选用了在`CNN`中广泛使用的`cross entropy`：

``` python
preds = tf.nn.sigmoid(x)
loss = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(labels=label, logits=x))
```

&emsp;&emsp;定义`Optimizer`：

``` python
learning_rate = tf.placeholder(tf.float32)
optimizer = tf.train.GradientDescentOptimizer(learning_rate).minimize(loss)
```

这里为`learning rate`定义了一个`placeholder`，当然也可以直接使用常量定义`learning rate`。
&emsp;&emsp;输入数据并开始训练：

``` python
train_data = np.array([[0, 0], [0, 1], [1, 0], [1, 1]])
train_label = np.array([[0], [1], [1], [0]])
​
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())

    for step in range(10000):
        if step < 3000:
            lr = 1
        elif step < 6000:
            lr = 0.1
        else:
            lr = 0.01

        _, l, pred = sess.run([optimizer, loss, preds],
                                feed_dict={
                                    data: train_data,
                                    label: train_label,
                                    learning_rate: lr})

        if step % 500:
            print('Step: {} -> Loss: {} -> Predictions: {}'.format(step, l, pred))
```

&emsp;&emsp;**补充说明**：对于`sess.run`函数，其第一个参数是你期望看到的输出信息，函数执行完以后，将这些信息对应地赋值给等号右边的变量；对于`feed_dict`，就是向自定义的神经网络中的变量赋值，例如：

``` python
_, l, pred = sess.run([optimizer, loss, preds],
                        feed_dict={
                            data: train_data,
                            label: train_label,
                            learning_rate: lr})
```

---

&emsp;&emsp;`Keras`实现`XOR`的代码如下：

``` python
from keras.models import Sequential
from keras.layers.core import Dense, Dropout, Activation
from keras.optimizers import SGD
import numpy as np
​
X = np.array([[0, 0], [0, 1], [1, 0], [1, 1]])
y = np.array([[0], [1], [1], [0]])
​
model = Sequential()
model.add(Dense(8, input_dim=2))
model.add(Activation('tanh'))
model.add(Dense(1))
model.add(Activation('sigmoid'))
​
sgd = SGD(lr=0.1)
model.compile(loss='binary_crossentropy', optimizer=sgd)
​
model.fit(X, y, batch_size=1, nb_epoch=1000)
print(model.predict_proba(X))
```

执行结果：

``` python
[[0.00254393]
 [0.9953328 ]
 [0.9948784 ]
 [0.00588351]]
```

---

&emsp;&emsp;`Pytorch`实现`XOR`的代码如下：

``` python
import torch
from torch.autograd import Variable
import torch.nn as nn
import torch.optim as optim
import numpy as np
import matplotlib.pyplot as plt
​
X = torch.Tensor([[0, 0], [0, 1], [1, 0], [1, 1]])
Y = torch.Tensor([0, 1, 1, 0]).view(-1, 1)
​
class XOR(nn.Module):
    def __init__(self, input_dim=2, output_dim=1):
        super(XOR, self).__init__()
        self.lin1 = nn.Linear(input_dim, 2)
        self.lin2 = nn.Linear(2, output_dim)
​
    def forward(self, x):
        x = self.lin1(x)
        x = torch.sigmoid(x)
        x = self.lin2(x)
        return x
​
model = XOR()
loss_func = nn.MSELoss()
optimizer = optim.SGD(model.parameters(), lr=0.02, momentum=0.9)
​
epochs = 2001
steps = X.size(0)

for i in range(epochs):
    for j in range(steps):
        data_point = np.random.randint(X.size(0))
        x_var = Variable(X[data_point], requires_grad=False)
        y_var = Variable(Y[data_point], requires_grad=False)
        optimizer.zero_grad()
        y_hat = model(x_var)
        loss = loss_func.forward(y_hat, y_var)
        loss.backward()
        optimizer.step()
​
    if i % 500 == 0:
        print("Epoch: {0}, Loss: {1}, ".format(i, loss.item()))
```