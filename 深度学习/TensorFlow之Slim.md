---
title: TensorFlow之Slim
categories: 深度学习
abbrlink: a8655ef5
date: 2019-02-13 12:16:46
---
&emsp;&emsp;`TF-Slim`是`Tensorflow`中一个轻量级的库，用于定义、训练和评估复杂的模型。`TF-Slim`中的组件可以与`Tensorflow`中原生的函数一起使用，与其他的框架(比如`tf.contrib.learn`)也可以一起使用。使用方法如下：<!--more-->

``` python
import tensorflow.contrib.slim as slim
```

&emsp;&emsp;`TF-Slim`可以使建立、训练和评估神经网络更加简单，具有如下特点：

- 允许用户通过减少模板代码使得模型更加简洁。这个可以通过使用`argument scoping`和大量的高层`layers`、`variables`来实现。
- 通过使用常用的正则化(`regularizers`)使得建立模型更加简单。
- 一些广泛使用的计算机视觉相关的模型(比如`VGG`、`AlexNet`)已经在`slim`中定义好了，用户可以很方便地使用。这些既可以当成黑盒使用，也可以被扩展使用，比如添加一些`multiple heads`到不同的内部的层。
- `Slim`使得扩展复杂模型变得容易，可以使用已经存在的模型的`checkpoints`来开始训练算法。

### What are the various components of TF-Slim?

&emsp;&emsp;`TF-Slim`由几个独立存在的组件组成：

- `arg_scope`：提供一个新的作用域(`scope`)，称为`arg_scope`。在该作用域(`scope`)中，用户可以定义一些默认的参数，用于特定的操作。
- `data`：包含`TF-Slim`的`dataset`定义、`data providers`、`parallel_reader`和`decoding utilities`。
- `evaluation`：包含用于模型评估的常规函数。
- `layers`：包含用于建立模型的高级`layers`。
- `learning`：包含一些用于训练模型的常规函数。
- `losses`：包含一些用于`loss function`的函数。
- `metrics`：包含一些热门的评价标准。
- `nets`：包含一些热门的网络定义，如`VGG`、`AlexNet`等模型。
- `queues`：提供一个内容管理者，使得可以很容易、很安全地启动和关闭`QueueRunners`。
- `regularizers`：包含权重正则化。
- `variables`：提供一个方便的封装，用于变量创建和使用。

#### Defining Models

&emsp;&emsp;使用`TF-Slim`，结合`variables`、`layers`和`scopes`，模型可以很简洁地被定义。

#### Variables

&emsp;&emsp;在原生的`Tensorflow`中，创建`Variable`需要一个预定义的值或者一种初始化机制(比如从一个高斯分布中随机采样)。此外，如果一个变量需要在一个特定的设备上(如`GPU`)创建，那么必须被明确说明。为了减少变量创建所需的代码，`TF-Slim`提供了一些封装函数(定义在`variables.py`中)，可以使得用户定义变量变得简单。
&emsp;&emsp;举个例子，定义一个权重(`weight`)变量，然后使用一个截断的正态分布来初始化，使用`l2 loss`正则化，并将该变量放置在`CPU`中，只需要声明如下：

``` python
weights = slim.variable(
    'weights', shape=[10, 10, 3, 3], initializer=tf.truncated_normal_initializer(stddev=0.1),
    regularizer=slim.l2_regularizer(0.05), device='/CPU:0')
```

&emsp;&emsp;在原生的`Tensorflow`中，有两种类型的`variables`：`regular variables`和`local (transient) variables`。绝大部分变量是`regular variables`，一旦被创建，可以使用`saver`来将这些变量保存到磁盘中；`Local variables`是那些仅仅存在于一个`session`内的变量，并不会被保存到磁盘中。
&emsp;&emsp;`TF-Slim`通过定义`model variables`来进一步区别变量，这些是表示一个模型参数的变量。`Model variables`在学习期间被训练或者`fine-tuned`，在评估或者推断期间可以从一个`checkpoint`中加载。模型变量包括使用`slim.fully_connected`或者`slim.conv2d`创建的变量等。非模型变量(`Non-model variables`)指的是那些在学习或者评估阶段使用，但是在实际的`inference`中不需要用到的变量。例如`global_step`在学习和评估阶段会用到的变量，但是实际上并不是模型的一部分。类似的，`moving average variables`也是非模型变量。
&emsp;&emsp;`model variables`和`regular variables`在`TF-Slim`中很容易地被创建和恢复：

``` python
# Model Variables
weights = slim.model_variable(
    'weights', shape=[10, 10, 3, 3], initializer=tf.truncated_normal_initializer(stddev=0.1),
    regularizer=slim.l2_regularizer(0.05), device='/CPU:0')
model_variables = slim.get_model_variables()
# Regular variables
my_var = slim.variable('my_var', shape=[20, 1], initializer=tf.zeros_initializer())
regular_variables_and_model_variables = slim.get_variables()
```

&emsp;&emsp;这是如何工作的呢？当你通过`TF-Slim`的`layer`或者直接通过`slim.model_variable`函数创建一个模型的变量时，`TF-Slim`将变量添加到`tf.GraphKeys.MODEL_VARIABLES`集合中。如果你想拥有自己定制化的`layers`或者`variables`创建机制，但是仍然想利用`TF-Slim`来管理你的变量，此时`TF-Slim`提供一个方便的函数，用于添加模型的变量到集合中：

``` python
my_model_variable = CreateViaCustomCode()
# Letting TF-Slim know about the additional variable
slim.add_model_variable(my_model_variable)
```

#### Layers

&emsp;&emsp;在原生的`Tensorflow`中，要定义一些层(比如说卷积层、全连接层、`BatchNorm`层等)是比较麻烦的。举个例子，神经网络中的卷积层由以下几个步骤组成：

- 创建权重和偏置变量。
- 将输入与权重做卷积运算。
- 将偏置加到第二步的卷积运算得到的结果中。
- 使用一个激活函数。

上面的步骤使用原始的`Tensorflow`代码实现如下：

``` python
input = ...

with tf.name_scope('conv1_1') as scope:
    kernel = tf.Variable(tf.truncated_normal([3, 3, 64, 128], dtype=tf.float32, stddev=1e-1), name='weights')
    conv = tf.nn.conv2d(input, kernel, [1, 1, 1, 1], padding='SAME')
    biases = tf.Variable(tf.constant(0.0, shape=[128], dtype=tf.float32), trainable=True, name='biases')
    bias = tf.nn.bias_add(conv, biases)
    conv1 = tf.nn.relu(bias, name=scope)
```

为了减少重复代码，`TF-Slim`提供了一些方便的、高级别的、更抽象的神经网络层。例如卷积层实现如下：

``` python
input = ...
net = slim.conv2d(input, 128, [3, 3], scope='conv1_1')
```

`TF-Slim`提供了大量的标准的实现，用于建立神经网络：

Layer                     | TF-Slim
--------------------------|--------
`BiasAdd`                 | `slim.bias_add`
`BatchNorm`               | `slim.batch_norm`
`Conv2d`                  | `slim.conv2d`
`Conv2dInPlane`           | `slim.conv2d_in_plane`
`Conv2dTranspose(Deconv)` | `slim.conv2d_transpose`
`FullyConnected`          | `slim.fully_connected`
`AvgPool2D`               | `slim.avg_pool2d`
`Dropout`                 | `slim.dropout`
`Flatten`                 | `slim.flatten`
`MaxPool2D`               | `slim.max_pool2d`
`OneHotEncoding`          | `slim.one_hot_encoding`
`SeparableConv2`          | `slim.separable_conv2d`
`UnitNorm`                | `slim.unit_norm`

&emsp;&emsp;`TF-Slim`也包含两个操作符，称为`repeat`和`stack`，允许用户重复执行相同的操作。例如下面几个卷积层加一个池化层是`VGG`网络的一部分：

``` python
net = ...
net = slim.conv2d(net, 256, [3, 3], scope='conv3_1')
net = slim.conv2d(net, 256, [3, 3], scope='conv3_2')
net = slim.conv2d(net, 256, [3, 3], scope='conv3_3')
net = slim.max_pool2d(net, [2, 2], scope='pool2')
```

减少重复代码的其中一种方法是利用`for`循环：

``` python
net = ...

for i in range(3):
    net = slim.conv2d(net, 256, [3, 3], scope='conv3_%d' % (i + 1))

net = slim.max_pool2d(net, [2, 2], scope='pool2')
```

另一种方式是使用`TF-Slim`中的`repeat`操作：

``` python
net = slim.repeat(net, 3, slim.conv2d, 256, [3, 3], scope='conv3')
net = slim.max_pool2d(net, [2, 2], scope='pool2')
```

在上面例子中，`slim.repeat`会自动给每一个卷积层的`scopes`命名为`conv3/conv3_1`、`conv3/conv3_2`和`conv3/conv3_3`。
&emsp;&emsp;另外，`TF-Slim`的`slim.stack`操作允许用户用不同的参数重复调用同一种操作。`slim.stack`也为每一个被创建的操作创建一个新的`tf.variable_scope`。例如下面是一种简单的方式来创建多层感知器：

``` python
# Verbose way:
x = slim.fully_connected(x, 32, scope='fc/fc_1')
x = slim.fully_connected(x, 64, scope='fc/fc_2')
x = slim.fully_connected(x, 128, scope='fc/fc_3')
# Equivalent, TF-Slim way using slim.stack:
slim.stack(x, slim.fully_connected, [32, 64, 128], scope='fc')
```

在上面的例子中，`slim.stack`调用了`slim.fully_connected`三次。类似的，我们可以使用`stack`来简化多层的卷积层：

``` python
# Verbose way:
x = slim.conv2d(x, 32, [3, 3], scope='core/core_1')
x = slim.conv2d(x, 32, [1, 1], scope='core/core_2')
x = slim.conv2d(x, 64, [3, 3], scope='core/core_3')
x = slim.conv2d(x, 64, [1, 1], scope='core/core_4')
# Using stack:
slim.stack(x, slim.conv2d, [(32, [3, 3]), (32, [1, 1]), (64, [3, 3]), (64, [1, 1])], scope='core')
```

#### Scopes

&emsp;&emsp;除了`Tensorflow`中作用域(`scope`)之外(`name_scope`、`variable_scope`)，`TF-Slim`增加了新的作用域机制，称为`arg_scope`。这个新的作用域允许使用者明确一个或者多个操作和一些参数，这些定义好的操作或者参数会传递给`arg_scope`内部的每一个操作。先看如下代码片段：

``` python
net = slim.conv2d(
    inputs, 64, [11, 11], 4, padding='SAME',
    weights_initializer=tf.truncated_normal_initializer(stddev=0.01),
    weights_regularizer=slim.l2_regularizer(0.0005), scope='conv1')
net = slim.conv2d(
    net, 128, [11, 11], padding='VALID',
    weights_initializer=tf.truncated_normal_initializer(stddev=0.01),
    weights_regularizer=slim.l2_regularizer(0.0005), scope='conv2')
net = slim.conv2d(
    net, 256, [11, 11], padding='SAME',
    weights_initializer=tf.truncated_normal_initializer(stddev=0.01),
    weights_regularizer=slim.l2_regularizer(0.0005), scope='conv3')
```

从上面的代码中可以清楚地看出来有`3`层卷积层，其中很多超参数都是一样的。两个卷积层有相同的`padding`，所有三个卷积层有相同的`weights_initializer`和`weight_regularizer`。上面的代码包含了大量重复的值，其中一种解决方法是使用变量来说明一些默认的值：

``` python
padding = 'SAME'
initializer = tf.truncated_normal_initializer(stddev=0.01)
regularizer = slim.l2_regularizer(0.0005)
​
net = slim.conv2d(
    inputs, 64, [11, 11], 4, padding=padding, weights_initializer=initializer,
    weights_regularizer=regularizer, scope='conv1')
net = slim.conv2d(
    net, 128, [11, 11], padding='VALID', weights_initializer=initializer,
    weights_regularizer=regularizer, scope='conv2')
net = slim.conv2d(
    net, 256, [11, 11], padding=padding, weights_initializer=initializer,
    weights_regularizer=regularizer, scope='conv3')
```

上面的解决方案其实并没有减少代码的混乱程度。通过使用`arg_scope`，我们既可以保证每一层使用相同的值，也可以简化代码：

``` python
with slim.arg_scope(
    [slim.conv2d], padding='SAME',
    weights_initializer=tf.truncated_normal_initializer(stddev=0.01),
    weights_regularizer=slim.l2_regularizer(0.0005)):
    net = slim.conv2d(inputs, 64, [11, 11], scope='conv1')
    net = slim.conv2d(net, 128, [11, 11], padding='VALID', scope='conv2')
    net = slim.conv2d(net, 256, [11, 11], scope='conv3')
```

上面的例子表明，使用`arg_scope`可以使得代码变得更整洁、更干净并且更加容易维护。注意到，在`arg_scope`中规定的参数值，它们可以被局部覆盖。例如上面的`padding`参数被设置成`SAME`，但是在第二个卷积层中用`VALID`覆盖了这个参数。
&emsp;&emsp;我们也可以嵌套使用`arg_scope`，在相同的作用域内使用多个操作：

``` python
with slim.arg_scope(
    [slim.conv2d, slim.fully_connected], activation_fn=tf.nn.relu,
    weights_initializer=tf.truncated_normal_initializer(stddev=0.01),
    weights_regularizer=slim.l2_regularizer(0.0005)):

    with slim.arg_scope([slim.conv2d], stride=1, padding='SAME'):
        net = slim.conv2d(inputs, 64, [11, 11], 4, padding='VALID', scope='conv1')
        net = slim.conv2d(net, 256, [5, 5], weights_initializer=tf.truncated_normal_initializer(stddev=0.03), scope='conv2')
        net = slim.fully_connected(net, 1000, activation_fn=None, scope='fc')
```

在第一个`arg_scope`中，卷积层和全连接层被应用于相同的权重初始化和权重正则化；在第二个`arg_scope`中，额外的参数仅仅对卷积层`conv2d`起作用。

### Working Example: Specifying the VGG16 Layers

&emsp;&emsp;通过结合`TF-Slim`的`Variables`、`Operations`和`scopes`，我们可以使用比较少的代码来实现一个比较复杂的网络。例如整个`VGG`网络定义如下：

``` python
def vgg16(inputs):
    with slim.arg_scope(
     [slim.conv2d, slim.fully_connected], activation_fn=tf.nn.relu,
     weights_initializer=tf.truncated_normal_initializer(0.0, 0.01),
     weights_regularizer=slim.l2_regularizer(0.0005)):
        net = slim.repeat(inputs, 2, slim.conv2d, 64, [3, 3], scope='conv1')
        net = slim.max_pool2d(net, [2, 2], scope='pool1')
        net = slim.repeat(net, 2, slim.conv2d, 128, [3, 3], scope='conv2')
        net = slim.max_pool2d(net, [2, 2], scope='pool2')
        net = slim.repeat(net, 3, slim.conv2d, 256, [3, 3], scope='conv3')
        net = slim.max_pool2d(net, [2, 2], scope='pool3')
        net = slim.repeat(net, 3, slim.conv2d, 512, [3, 3], scope='conv4')
        net = slim.max_pool2d(net, [2, 2], scope='pool4')
        net = slim.repeat(net, 3, slim.conv2d, 512, [3, 3], scope='conv5')
        net = slim.max_pool2d(net, [2, 2], scope='pool5')
        net = slim.fully_connected(net, 4096, scope='fc6')
        net = slim.dropout(net, 0.5, scope='dropout6')
        net = slim.fully_connected(net, 4096, scope='fc7')
        net = slim.dropout(net, 0.5, scope='dropout7')
        net = slim.fully_connected(net, 1000, activation_fn=None, scope='fc8')

    return net
```

### Training Models

&emsp;&emsp;训练`Tensorflow`模型要求一个模型、一个`loss function`、梯度计算和一个训练的程序，用来迭代地根据`loss`计算模型权重的梯度和更新权重。`TF-Slim`提供了`loss function`和一些帮助函数来运行训练和评估。

#### Losses

&emsp;&emsp;`Loss function`定义了一个我们需要最小化的量。对于分类问题，主要是计算真正的分布与预测的概率分布之间的交叉熵；对于回归问题，主要是计算预测值与真实值均方误差。
&emsp;&emsp;特定的模型，比如说多任务学习模型，要求同时使用多个`loss function`。换句话说，最终被最小化的`loss function`是多个其他的`loss function`之和。例如一个同时预测图像中场景的类型和深度的模型，该模型的`loss function`就是分类`loss`和深度预测`loss`之和。
&emsp;&emsp;`TF-Slim`通过`losses`模块为用户提供了一种机制，使得定义`loss function`变得简单。例如下面的是我们想要训练`VGG`网络的简单示例：

``` python
import tensorflow as tf
import tensorflow.contrib.slim.nets as nets
​
vgg = nets.vgg
images, labels = ...  # Load the images and labels
predictions, _ = vgg.vgg_16(images)  # Create the model
# Define the loss functions and get the total loss
loss = slim.losses.softmax_cross_entropy(predictions, labels)
```

在上面这个例子中，我们首先创建一个模型(利用`TF-Slim`的`VGG`实现)，然后增加了标准的分类`loss`。现在来看看当我们有一个多个输出的多任务模型的情况：

``` python
images, scene_labels, depth_labels = ...  # Load the images and labels
scene_predictions, depth_predictions = CreateMultiTaskModel(images)  # Create the model
# Define the loss functions and get the total loss
classification_loss = slim.losses.softmax_cross_entropy(scene_predictions, scene_labels)
sum_of_squares_loss = slim.losses.sum_of_squares(depth_predictions, depth_labels)
# The following two lines have the same effect:
total_loss = classification_loss + sum_of_squares_loss
total_loss = slim.losses.get_total_loss(add_regularization_losses=False)
```

在这个例子中有`2`个`loss`，是通过调用`slim.losses.softmax_cross_entropy`和`slim.losses.sum_of_squares`得到。我们可以将这两个`loss`加在一起，或者调用`slim.losses.get_total_loss`来得到全部的`loss`(`total_loss`)。这是如何工作的？当你通过`TF-Slim`创建一个`loss`时，`TF-Slim`将`loss`加到一个特殊的`TensorFlow collection of loss functions`。这使得你既可以手动地管理全部的`loss`，也可以让`TF-Slim`来替你管理它们。
&emsp;&emsp;如果你想让`TF-Slim`为你管理`losses`，但是你有一个自己实现的`loss`该怎么办？`loss_ops.py`也有一个函数可以将你自己实现的`loss`加到`TF-Slims collection`中：

``` python
images, scene_labels, depth_labels, pose_labels = ...  # Load the images and labels
scene_predictions, depth_predictions, pose_predictions = CreateMultiTaskModel(images)  # Create the model
​
# Define the loss functions and get the total loss
classification_loss = slim.losses.softmax_cross_entropy(scene_predictions, scene_labels)
sum_of_squares_loss = slim.losses.sum_of_squares(depth_predictions, depth_labels)
pose_loss = MyCustomLossFunction(pose_predictions, pose_labels)
slim.losses.add_loss(pose_loss)  # Letting TF-Slim know about the additional loss.
​
# The following two ways to compute the total loss are equivalent:
regularization_loss = tf.add_n(slim.losses.get_regularization_losses())
total_loss1 = classification_loss + sum_of_squares_loss + pose_loss + regularization_loss
​
total_loss2 = slim.losses.get_total_loss()  # (Regularization Loss is included in the total loss by default)
```

在这个例子中，我们既可以手动地计算的出全部的`loss function`，也可以让`TF-Slim`知道这个额外的`loss`，然后让`TF-Slim`处理这个`loss`。

#### Training Loop

&emsp;&emsp;`TF-Slim`提供了一个简单但是很强的用于训练模型的工具(在`learning.py`中)，其中包括一个可以重复测量`loss`、计算梯度和将模型保存到磁盘的训练函数。举个例子，一旦定义好了模型、`loss function`和最优化方法，我们可以调用`slim.learning.create_train_op`和`slim.learning.train`来实现优化：

``` python
g = tf.Graph()
​
# Create the model and specify the losses
...
total_loss = slim.losses.get_total_loss()
optimizer = tf.train.GradientDescentOptimizer(learning_rate)
# create_train_op ensures that each time we ask for the loss,
# the update_ops are run and the gradients being computed are applied too
train_op = slim.learning.create_train_op(total_loss, optimizer)
logdir = ...  # Where checkpoints are stored.
slim.learning.train(train_op, logdir, number_of_steps=1000, save_summaries_secs=300, save_interval_secs=600):
```

在这个例子中，提供给`slim.learning.train`的参数有`train_op`(用于计算`loss`和梯度)；`logdir`(用于声明`checkpoints`和`event`文件保存的路径)；用`number_of_steps`参数来限制梯度下降的步数；`save_summaries_secs=300`表明每`5`分钟计算一次`summaries`；`save_interval_secs=600`表明每`10`分钟保存一次模型的`checkpoint`。

### Working Example: Training the VGG16 Model

&emsp;&emsp;下面是训练一个`VGG`网络的例子：

``` python
import tensorflow as tf
import tensorflow.contrib.slim.nets as nets
​
slim = tf.contrib.slim
vgg = nets.vgg
...
train_log_dir = ...
​
if not tf.gfile.Exists(train_log_dir):
    tf.gfile.MakeDirs(train_log_dir)
​
with tf.Graph().as_default():
    images, labels = ...  # Set up the data loading
    predictions = vgg.vgg_16(images, is_training=True)  # Define the model
    # Specify the loss function:
    slim.losses.softmax_cross_entropy(predictions, labels)
    total_loss = slim.losses.get_total_loss()
    tf.summary.scalar('losses/total_loss', total_loss)
    optimizer = tf.train.GradientDescentOptimizer(learning_rate=.001)  # Specify the optimization scheme
    # create_train_op that ensures that when we evaluate it to get the loss,
    # the update_ops are done and the gradient updates are computed
    train_tensor = slim.learning.create_train_op(total_loss, optimizer)
    slim.learning.train(train_tensor, train_log_dir)  # Actually runs training
```

### Fine-Tuning Existing Models

#### Brief Recap on Restoring Variables from a Checkpoint

&emsp;&emsp;当一个模型被训练完毕之后，它可以从一个给定的`checkpoint`中使用`tf.train.Saver`来恢复变量。在很多情况下，`tf.train.Saver`提供一个简单的机制来恢复所有变量或者一部分变量：

``` python
# Create some variables
v1 = tf.Variable(..., name="v1")
v2 = tf.Variable(..., name="v2")
restorer = tf.train.Saver()  # Add ops to restore all the variables
restorer = tf.train.Saver([v1, v2])  # Add ops to restore some variables
​
# Later, launch the model, use the saver to restore variables
# from disk, and do some work with the model
with tf.Session() as sess:
    restorer.restore(sess, "/tmp/model.ckpt")  # Restore variables from disk
    print("Model restored.")
    # Do some work with the model
```

#### Partially Restoring Models

&emsp;&emsp;在一个新的数据集或者一个新的任务上`fine-tune`一个预训练的模型通常是比较流行的，我们可以使用`TF-Slim`的`helper`函数来选择想要恢复的一部分变量：

``` python
# Create some variables.
v1 = slim.variable(name="v1", ...)
v2 = slim.variable(name="nested/v2", ...)
​
# Get list of variables to restore (which contains only 'v2').
# These are all equivalent methods:
variables_to_restore = slim.get_variables_by_name("v2")
# or
variables_to_restore = slim.get_variables_by_suffix("2")
# or
variables_to_restore = slim.get_variables(scope="nested")
# or
variables_to_restore = slim.get_variables_to_restore(include=["nested"])
# or
variables_to_restore = slim.get_variables_to_restore(exclude=["v1"])
​
# Create the saver which will be used to restore the variables
restorer = tf.train.Saver(variables_to_restore)
​
with tf.Session() as sess:
    restorer.restore(sess, "/tmp/model.ckpt")  # Restore variables from disk
    print("Model restored.")
    # Do some work with the model
```

#### Restoring models with different variable names

&emsp;&emsp;当从一个`checkpoint`中恢复变量时，`Saver`定位在`checkpoint`文件中变量的名字，并且将它们映射到当前图(`graph`)的变量中去。在上面的例子中，我们通过传递给`saver`一个变量列表来创建一个`saver`。在这种情况下，在`checkpoint`文件中定位的变量名隐式地从每个提供的变量的`var.op.name`中获得。
&emsp;&emsp;当`checkpoint`文件中的变量名与`graph`匹配时，将会工作良好。然而有时候，我们想要从一个与当前的`graph`不同变量名的`checkpoint`中恢复变量，那么在这种情况下，我们必须给`Saver`提供一个字典，该字典将每个`checkpoint`中变量名映射到每个`graph`的变量。下面的例子通过一个简单的函数获得`checkpoint`中的变量的名字：

``` python
# Assuming than 'conv1/weights' should be restored from 'vgg16/conv1/weights'
def name_in_checkpoint(var):
    return 'vgg16/' + var.op.name
​
# Assuming than 'conv1/weights' and 'conv1/bias' should be
# restored from 'conv1/params1' and 'conv1/params2'
def name_in_checkpoint(var):
    if "weights" in var.op.name:
        return var.op.name.replace("weights", "params1")

    if "bias" in var.op.name:
        return var.op.name.replace("bias", "params2")
​
variables_to_restore = slim.get_model_variables()
variables_to_restore = {name_in_checkpoint(var): var for var in variables_to_restore}
restorer = tf.train.Saver(variables_to_restore)
​
with tf.Session() as sess:
    restorer.restore(sess, "/tmp/model.ckpt")  # Restore variables from disk
```

#### Fine-Tuning a Model on a different task

&emsp;&emsp;考虑这么一种情况：我们有一个预训练好的`VGG16`模型，该模型是在`ImageNet`数据集上训练好的，有`1000`类。然而我们想要将其应用到只有`20`类的`Pascal VOC`数据集上。为了实现这个功能，我们可以使用不包括最后一层的预训练模型来初始化新模型：

``` python
image, label = MyPascalVocDataLoader(...)  # Load the Pascal VOC data
images, labels = tf.train.batch([image, label], batch_size=32)
​
predictions = vgg.vgg_16(images)  # Create the model
train_op = slim.learning.create_train_op(...)
# Specify where the Model, trained on ImageNet, was saved
model_path = '/path/to/pre_trained_on_imagenet.checkpoint'
log_dir = '/path/to/my_pascal_model_dir/'  # Specify where the new model will live
# Restore only the convolutional layers
variables_to_restore = slim.get_variables_to_restore(exclude=['fc6', 'fc7', 'fc8'])
init_fn = assign_from_checkpoint_fn(model_path, variables_to_restore)
slim.learning.train(train_op, log_dir, init_fn=init_fn)  # Start training
```

### Evaluating Models

&emsp;&emsp;一旦我们已经训练好了一个模型(或者模型正在训练之中)，想要看看模型的实际表现能力，这个可以通过使用一些评估度量来实现，该度量可以对模型的表现能力评分。而评估代码实际上是加载数据、做出预测、将预测结果与真实值做比较，最后得到得分。这个步骤可以运行一次或者周期重复。

#### Metrics

&emsp;&emsp;我们将度量定义为一个性能度量，它不是一个`loss`函数(`losses`是在训练的时候直接最优化)，但我们仍然感兴趣的是评估模型的目的。例如我们想要最优化`log loss`，但是我们感兴趣的度量可能是`F1`得分(`test accuracy`)，或者是`Intersection Over Union score`(这是不可微的，因此不能作为损失使用)。
&emsp;&emsp;`TF-Slim`提供了一些使得评估模型变得简单的度量操作。计算度量的值可以分为以下三个步骤：

- 初始化(`Initialization`)：初始化用于计算度量的变量。
- 聚合(`Aggregation`)：使用操作(比如求和操作)来计算度量。
- 终止化(`Finalization`)：这是可选的步骤，使用最终的操作来计算度量值，比如说计算均值、最小值、最大值等。

举个例子，为了计算`mean_absolute_error`，变量`count`和`total`变量被初始化为`0`。在聚合期间，我们观测到一些预测值和标签值，计算它们的绝对差值然后加到`total`中。每一次我们观测到新的一个数据，我们增加`count`。最后在`Finalization`期间，`total`除以`count`来获得均值`mean`。
&emsp;&emsp;下面的示例演示了声明度量标准的`API`。由于度量经常在测试集上进行评估，因此我们假设使用的是测试集：

``` python
images, labels = LoadTestData(...)
predictions = MyModel(images)
​
mae_value_op, mae_update_op = slim.metrics.streaming_mean_absolute_error(predictions, labels)
mre_value_op, mre_update_op = slim.metrics.streaming_mean_relative_error(predictions, labels)
pl_value_op, pl_update_op = slim.metrics.percentage_less(mean_relative_errors, 0.3)
```

一个度量的创建返回两个值：`value_op`和`update_op`。`value_op`是一个幂等操作，它返回度量的当前值；`update_op`是执行上面提到的聚合步骤的操作，以及返回度量的值。
&emsp;&emsp;跟踪每个`value_op`和`update_op`是很费力的，为了解决这个问题，`TF-Slim`提供了两个便利功能：

``` python
# Aggregates the value and update ops in two lists:
value_ops, update_ops = slim.metrics.aggregate_metrics(
    slim.metrics.streaming_mean_absolute_error(predictions, labels),
    slim.metrics.streaming_mean_squared_error(predictions, labels))
​
# Aggregates the value and update ops in two dictionaries
names_to_values, names_to_updates = slim.metrics.aggregate_metric_map({
    "eval/mean_absolute_error": slim.metrics.streaming_mean_absolute_error(predictions, labels),
    "eval/mean_squared_error": slim.metrics.streaming_mean_squared_error(predictions, labels),
})
```

### Working example: Tracking Multiple Metrics

&emsp;&emsp;将代码全部放在一起：

``` python
import tensorflow as tf
import tensorflow.contrib.slim.nets as nets
​
slim = tf.contrib.slim
vgg = nets.vgg
​
images, labels = load_data(...)  # Load the data
predictions = vgg.vgg_16(images)  # Define the network
​
# Choose the metrics to compute:
names_to_values, names_to_updates = slim.metrics.aggregate_metric_map({
    "eval/mean_absolute_error": slim.metrics.streaming_mean_absolute_error(predictions, labels),
    "eval/mean_squared_error": slim.metrics.streaming_mean_squared_error(predictions, labels),
})
​
# Evaluate the model using 1000 batches of data:
num_batches = 1000
​
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    sess.run(tf.local_variables_initializer())
​
    for batch_id in range(num_batches):
        sess.run(names_to_updates.values())
​
    metric_values = sess.run(names_to_values.values())

    for metric, value in zip(names_to_values.keys(), metric_values):
        print('Metric %s has value: %f' % (metric, value))
```

### Evaluation Loop

&emsp;&emsp;`TF-Slim`提供了一个评估模块(`evaluation.py`)，它包含了使用来自`metric_ops.py`模块编写模型评估脚本的辅助函数。这些功能包括定期运行评估、对数据批量进行评估、打印和汇总度量结果的功能：

``` python
import tensorflow as tf
​
slim = tf.contrib.slim
images, labels = load_data(...)  # Load the data
predictions = MyModel(images)  # Define the network
​
# Choose the metrics to compute:
names_to_values, names_to_updates = slim.metrics.aggregate_metric_map({
    'accuracy': slim.metrics.accuracy(predictions, labels),
    'precision': slim.metrics.precision(predictions, labels),
    'recall': slim.metrics.recall(mean_relative_errors, 0.3),
})
​
# Create the summary ops such that they also print out to std output:
summary_ops = []

for metric_name, metric_value in names_to_values.iteritems():
    op = tf.summary.scalar(metric_name, metric_value)
    op = tf.Print(op, [metric_value], metric_name)
    summary_ops.append(op)
​
num_examples = 10000
batch_size = 32
num_batches = math.ceil(num_examples / float(batch_size))
​
slim.get_or_create_global_step()  # Setup the global step
​
output_dir = ...  # Where the summaries are stored.
eval_interval_secs = ...  # How often to run the evaluation.
slim.evaluation.evaluation_loop(
    'local', checkpoint_dir, log_dir, num_evals=num_batches, eval_op=names_to_updates.values(),
    summary_op=tf.summary.merge(summary_ops), eval_interval_secs=eval_interval_secs)
```

---

### tf.nn.conv2d和tf.contrib.slim.conv2d的区别

&emsp;&emsp;在查看代码的时候，发现有代码用到卷积层是`tf.nn.conv2d`，但是也有使用的卷积层是`tf.contrib.slim.conv2d`。`tf.nn.conv2d`函数原型如下：

``` python
conv2d(input, filter, strides, padding, use_cudnn_on_gpu=None, data_format=None, name=None)
```

- `input`：需要做卷积的输入图像，它要求是一个`Tensor`，具有`[batch_size, in_height, in_width, in_channels]`这样的`shape`，具体含义是`[训练时一个batch的图片数量，图片高度，图片宽度，图像通道数]`。注意这是一个`4`维的`Tensor`，要求数据类型为`float32`和`float64`其中之一。
- `filter`：指定`CNN`中的卷积核，它要求是一个`Tensor`，具有`[filter_height, filter_width, in_channels, out_channels]`这样的`shape`，具体含义是`[卷积核的高度，卷积核的宽度，图像通道数，卷积核个数]`，要求类型与参数`input`相同。有一个地方需要注意，第三维`in_channels`就是参数`input`的第四维，这里是维度一致，不是数值一致。
- `strides`：卷积时在图像每一维的步长。这是一个一维的向量，长度为`4`，对应的是在`input`的`4`个维度上的步长。
- `padding`：`string`类型的变量，只能是`SAME`、`VALID`其中之一。这个值决定了不同的卷积方式，`SAME`代表卷积核可以停留图像边缘，`VALID`表示不能。
- `use_cudnn_on_gpu`：指定是否使用`cudnn`加速。
- `data_format`：指定`input`的格式，默认为`NHWC`格式。

该函数结果返回一个`Tensor`，这个输出就是我们常说的`feature map`。
&emsp;&emsp;`tf.contrib.slim.conv2d`函数原型如下：

``` python
convolution(
    inputs, num_outputs, kernel_size, stride=1, padding='SAME', data_format=None,
    rate=1, activation_fn=nn.relu, normalizer_fn=None, normalizer_params=None,
    weights_initializer=initializers.xavier_initializer(), weights_regularizer=None,
    biases_initializer=init_ops.zeros_initializer(), biases_regularizer=None, reuse=None,
    variables_collections=None, outputs_collections=None, trainable=True, scope=None):
```

- `inputs`：需要做卷积的输入图像。
- `num_outputs`：卷积核的个数(就是`filter`的个数)。
- `kernel_size`：卷积核的维度(卷积核的宽度和高度)。
- `stride`：卷积时在图像每一维的步长。
- `padding`：`padding`的方式选择，`VALID`或者`SAME`。
- `data_format`：指定`input`的格式。
- `rate`：使用`atrous convolution`的膨胀率。
- `activation_fn`：指定激活函数，默认为`ReLU`函数。
- `normalizer_fn`：正则化函数。
- `normalizer_params`：正则化函数的参数。
- `weights_initializer`：权重的初始化程序。
- `weights_regularizer`：权重可选的正则化程序。
- `biases_initializer`：`biase`的初始化程序。
- `biases_regularizer`：`biases`可选的正则化程序。
- `reuse`：是否共享层或者核变量。
- `variable_collections`：所有变量的集合列表或者字典。
- `outputs_collections`：输出被添加的集合。
- `trainable`：卷积层的参数是否可被训练。
- `scope`：共享变量所指的`variable_scope`。

&emsp;&emsp;在上述的`API`中，可以看出去两者并没有什么不同。只是`tf.contrib.slim.conv2d`提供了更多可以指定的初始化部分，而对于`tf.nn.conv2d`而言，其指定`filter`的方式相比较`tf.contrib.slim.conv2d`来说更加得复杂。去除掉少用的初始化部分，其实两者的`API`可以简化如下：

``` python
tf.contrib.slim.conv2d(
    inputs,
    num_outputs,  # [卷积核个数]
    kernel_size,  # [卷积核的高度, 卷积核的宽度]
    stride=1, padding='SAME',)
​
tf.nn.conv2d(
    input,  # 与上述一致
    filter,  # [卷积核的高度, 卷积核的宽度, 图像通道数, 卷积核个数]
    strides, padding,)
```