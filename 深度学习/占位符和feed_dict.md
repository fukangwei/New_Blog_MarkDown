---
title: 占位符和feed_dict
categories: 深度学习
abbrlink: 3a082f31
date: 2019-02-06 19:43:34
---
&emsp;&emsp;虽然`TensorFlow`可以创建各种形式的常量和变量，但同样还支持占位符。占位符并没有初始值，它只会分配必要的内存。在会话中，占位符可以使用`feed_dict`馈送数据。<!--more-->
&emsp;&emsp;`feed_dict`是一个字典，在字典中需要给出每一个用到的占位符的取值。在训练神经网络时，需要每次提供一个批量的训练样本，如果每次迭代选取的数据要通过常量表示，那么`TensorFlow`的计算图会非常大，因为每增加一个常量，`TensorFlow`都会在计算图中增加一个结点。所以拥有几百万次迭代的神经网络会拥有极其庞大的计算图，而占位符却可以解决这一问题，它只会拥有占位符这一个结点。
&emsp;&emsp;下面一段代码分别展示了使用常量和占位符进行计算：

``` python
import tensorflow as tf

w1 = tf.Variable(tf.random_normal([1, 2], stddev=1, seed=1))
# 因为需要重复输入x，而每建一个x就会生成一个结点，计算图的效率会很低，所以使用占位符
x = tf.placeholder(tf.float32, shape=(1, 2))
x1 = tf.constant([[0.7, 0.9]])
​
a = x + w1
b = x1 + w1
​
sess = tf.Session()
sess.run(tf.global_variables_initializer())
# 运行y时将占位符填上，feed_dict为字典，变量名不可变
y_1 = sess.run(a, feed_dict={x: [[0.7, 0.9]]})
y_2 = sess.run(b)
print(y_1)
print(y_2)
sess.close()
```

其中`y_1`的计算过程使用占位符，而`y_2`的计算过程使用常量。下面是使用占位符的案例(计算两点之间的距离)：

``` python
import tensorflow as tf
import numpy as np
​
def calculate_eucledian_distance(point1, point2):
    difference = tf.subtract(point1, point2)
    power2 = tf.pow(difference, tf.constant(2.0, shape=(1, 2)))
    add = tf.reduce_sum(power2)
    eucledian_distance = tf.sqrt(add)
    return eucledian_distance
​
list_of_points1_ = [[1, 2], [3, 4], [5, 6], [7, 8]]
list_of_points2_ = [[15, 16], [13, 14], [11, 12], [9, 10]]
​
list_of_points1 = np.array([np.array(elem).reshape(1, 2) for elem in list_of_points1_])
list_of_points2 = np.array([np.array(elem).reshape(1, 2) for elem in list_of_points2_])
​
graph = tf.Graph()
​
with graph.as_default():
    # 使用tf.placeholder创建占位符 ，在session.run过程中再投递数据
    point1 = tf.placeholder(tf.float32, shape=(1, 2))
    point2 = tf.placeholder(tf.float32, shape=(1, 2))
    dist = calculate_eucledian_distance(point1, point2)
​
with tf.Session(graph=graph) as session:
    tf.global_variables_initializer().run()

    for ii in range(len(list_of_points1)):
        point1_ = list_of_points1[ii]
        point2_ = list_of_points2[ii]
        # 使用feed_dict将数据投入到[dist]中
        feed_dict = {point1: point1_, point2: point2_}
        distance = session.run([dist], feed_dict=feed_dict)
        print("the distance between {} and {} -> {}".format(point1_, point2_, distance))
```

执行结果：

``` python
the distance between [[1 2]] and [[15 16]] -> [19.79899]
the distance between [[3 4]] and [[13 14]] -> [14.142136]
the distance between [[5 6]] and [[11 12]] -> [8.485281]
the distance between [[7 8]] and [[ 9 10]] -> [2.828427]
```

&emsp;&emsp;**补充说明**：也可以这样理解：`feed_dict`是为`placeholder`准备数据的。