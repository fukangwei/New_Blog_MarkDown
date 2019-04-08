---
title: CNN与SVM联用
date: 2019-01-16 13:31:51
categories: 深度学习
---
&emsp;&emsp;本文演示如何使用卷积神经网络来提取手写数字图片特征，并使用`SVM`进行分类。该实验使用了`UCI`手写数字数据集，其中前`256`维是`16 * 16`的图片，后`10`维是`one hot`编码的标签，即`1000000000`代表`0`，`0010000000`代表`2`。
&emsp;&emsp;使用`CNN`提取特征的原因如下：

- 由于卷积和池化计算的性质，使得图像中的平移部分对于最后的特征向量是没有影响的。从这一角度说，提取到的特征更不容易过拟合。
- 可以利用不同的卷积、池化和最后输出的特征向量的大小控制整体模型的拟合能力。在过拟合时可以降低特征向量的维数，在欠拟合时可以提高卷积层的输出维数。

&emsp;&emsp;算法流程如下：

1. 整理训练网络的数据。
2. 建立卷积神经网络。
3. 将数据代入进行训练。
4. 保存训练好的模型。
5. 把数据代入模型获得特征向量。
6. 用特征向量送入`SVM`进行训练。
7. 使用`SVM`进行预测，获得结果。

``` python
import random
import numpy as np
import tensorflow as tf
from sklearn import svm
import time
​
start = time.clock()
​
right0 = 0.0  # 记录预测为1，且实际为1的结果数
error0 = 0  # 记录预测为1，但实际为0的结果数
right1 = 0.0  # 记录预测为0，且实际为0的结果数
error1 = 0  # 记录预测为0，但实际为1的结果数
​
def weight_variable(shape):  # 初始化权值向量
    initial = tf.truncated_normal(shape, stddev=0.1)
    return tf.Variable(initial)
​
def bias_variable(shape):  # 初始化偏置向量
    initial = tf.constant(0.1, shape=shape)
    return tf.Variable(initial)
​
def conv2d(x, W):  # 二维卷积运算，步长为1，输出大小不变
    return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], padding='SAME')
​
def max_pool_2x2(x):  # 池化运算，将卷积特征缩小为“1/2”
    return tf.nn.max_pool(x, ksize=[1, 2, 2, 1], strides=[1, 2, 2, 1], padding='SAME')
​
for file_num in range(10):
    print('testing NO.%d dataset.......' % file_num)
    ff = open('digit_train_' + file_num.__str__() + '.data')
    rr = ff.readlines()
    x_test2 = []
    y_test2 = []
​
    for i in range(len(rr)):
        x_test2.append(list(map(int, map(float, rr[i].split(' ')[:256]))))
        y_test2.append(list(map(int, rr[i].split(' ')[256:266])))

    ff.close()
    # 读出测试数据
    ff2 = open('digit_test_' + file_num.__str__() + '.data')
    rr2 = ff2.readlines()
    x_test3 = []
    y_test3 = []

    for i in range(len(rr2)):
        x_test3.append(list(map(int, map(float, rr2[i].split(' ')[:256]))))
        y_test3.append(list(map(int, rr2[i].split(' ')[256:266])))

    ff2.close()
​
    # 给x、y留出占位符，以便未来填充数据
    x = tf.placeholder("float", [None, 256])
    y_ = tf.placeholder("float", [None, 10])
    # 设置输入层的W和b
    W = tf.Variable(tf.zeros([256, 10]))
    b = tf.Variable(tf.zeros([10]))
    # 计算输出，采用的函数是softmax(输入的时候是“one hot”编码)
    y = tf.nn.softmax(tf.matmul(x, W) + b)
    # 第一个卷积层，“5*5”的卷积核，输出向量是32维
    w_conv1 = weight_variable([5, 5, 1, 32])
    b_conv1 = bias_variable([32])
    # 图片大小是“16*16”，“-1”代表其他维数自适应
    x_image = tf.reshape(x, [-1, 16, 16, 1])
    h_conv1 = tf.nn.relu(conv2d(x_image, w_conv1) + b_conv1)
    h_pool1 = max_pool_2x2(h_conv1)
​
    # 第二层卷积层，输入向量是32维，输出64维，还是“5*5”的卷积核
    w_conv2 = weight_variable([5, 5, 32, 64])
    b_conv2 = bias_variable([64])
    h_conv2 = tf.nn.relu(conv2d(h_pool1, w_conv2) + b_conv2)
    h_pool2 = max_pool_2x2(h_conv2)
​
    # 全连接层的w和b
    w_fc1 = weight_variable([4 * 4 * 64, 256])
    b_fc1 = bias_variable([256])  # 此时输出的维数是256维
    h_pool2_flat = tf.reshape(h_pool2, [-1, 4 * 4 * 64])
    # h_fc1是提取出的256维特征，后面就是用这个参数输入到SVM中
    h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, w_fc1) + b_fc1)
​
    # 设置dropout，否则很容易过拟合
    keep_prob = tf.placeholder("float")
    h_fc1_drop = tf.nn.dropout(h_fc1, keep_prob)
​
    # 输出层，本次实验只利用它的输出训练CNN
    w_fc2 = weight_variable([256, 10])
    b_fc2 = bias_variable([10])
​
    y_conv = tf.nn.softmax(tf.matmul(h_fc1_drop, w_fc2) + b_fc2)
    # 以交叉熵的形式设置误差代价
    cross_entropy = -tf.reduce_sum(y_ * tf.log(y_conv))
    # 用adma的优化算法优化目标函数
    train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)
    correct_prediction = tf.equal(tf.argmax(y_conv, 1), tf.argmax(y_, 1))
    accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))
​
    with tf.Session() as sess:
        sess.run(tf.global_variables_initializer())
        for i in range(3000):  # 跑3000轮迭代，每次随机从训练样本中抽出50个进行训练
            batch = ([], [])
            p = random.sample(range(795), 50)
            for k in p:
                batch[0].append(x_test2[k])
                batch[1].append(y_test2[k])

            if i % 100 == 0:
                train_accuracy = accuracy.eval(feed_dict={x: batch[0],
                                                          y_: batch[1],
                                                          keep_prob: 1.0})

            train_step.run(feed_dict={x: batch[0], y_: batch[1], keep_prob: 0.6})
​
        print("test accuracy %g" % accuracy.eval(feed_dict={x: x_test3,
                                                            y_: y_test3,
                                                            keep_prob: 1.0}))
​
        # 以下两步都是为了将源数据的“one hot”编码改为1和0，数字“5”可以改为其他值
        for h in range(len(y_test2)):
            if np.argmax(y_test2[h]) == 5:
                y_test2[h] = 1
            else:
                y_test2[h] = 0
        for h in range(len(y_test3)):
            if np.argmax(y_test3[h]) == 5:
                y_test3[h] = 1
            else:
                y_test3[h] = 0
​
        # 将原来的x带入训练好的CNN中，计算出来全连接层的特征向量，将结果作为SVM中的特征向量
        x_temp = []

        for g in x_test2:
            x_temp.append(sess.run(h_fc1, feed_dict={x: np.array(g).reshape((1, 256))})[0])  

        x_temp2 = []

        for g in x_test3:
            x_temp2.append(sess.run(h_fc1, feed_dict={x: np.array(g).reshape((1, 256))})[0])
​
        clf = svm.SVC(C=0.9, kernel='linear')
        clf.fit(x_temp, y_test2)
​
        print('svm testing accuracy:', clf.score(x_temp2, y_test3))
        for j in range(len(x_temp2)):
            # 在验证时，对出现的四种情况分别使用四个变量进行存储
            if clf.predict(x_temp2[j].reshape(1, -1))[0] == y_test3[j] == 1:
                right0 += 1
            elif clf.predict(x_temp2[j].reshape(1, -1))[0] == y_test3[j] == 0:
                right1 += 1
            elif clf.predict(x_temp2[j].reshape(1, -1))[0] == 1 and y_test3[j] == 0:
                error0 += 1
            else:
                error1 += 1
​
accuracy = right0 / (right0 + error0)  # 准确率
recall = right0 / (right0 + error1)  # 召回率
print('svm right ratio:', (right0 + right1) / (right0 + right1 + error0 + error1))
print('accuracy:', accuracy)
print('recall:', recall)
print('F1 score:', 2 * accuracy * recall / (accuracy + recall))  # 计算F1值
​
end = time.clock()
print("time is:", end - start)
```