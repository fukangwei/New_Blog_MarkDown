---
title: TensorFlow之线程和队列
categories: 深度学习
date: 2019-02-13 15:33:07
---
### 理解TensorFlow的Queue

&emsp;&emsp;这篇文章来说明`TensorFlow`里与`Queue`有关的概念和用法，其实概念只有三个：

- `Queue`是`TF`队列和缓存机制的实现。
- `QueueRunner`是`TF`中对操作`Queue`的线程的封装。
- `Coordinator`是`TF`中用来协调线程运行的工具。

虽然它们经常同时出现，但这三样东西在`TensorFlow`中是可以单独使用的。

#### Queue

&emsp;&emsp;根据实现的方式不同，分成具体的几种类型：

- `tf.FIFOQueue`：按入列顺序出列的队列(如果希望读入的训练样本是有序的)。
- `tf.RandomShuffleQueue`：随机顺序出列的队列。
- `tf.PaddingFIFOQueue`：以固定长度批量出列的队列。
- `tf.PriorityQueue`：带优先级出列的队列。

这些类型的`Queue`除了自身的性质不太一样外，创建、使用的方法基本是相同的。
&emsp;&emsp;创建函数的参数：

``` python
tf.FIFOQueue(capacity, dtypes, shapes=None, names=None ...)
```

`Queue`主要包含入列(`enqueue`)和出列(`dequeue`)两个操作。`enqueue`操作返回计算图中的一个`Operation`节点，`dequeue`操作返回一个`Tensor`值。`Tensor`在创建时同样只是一个定义(或称为`声明`)，需要放在`Session`中运行才能获得真正的数值。下面是一个单独使用`Queue`的例子：

``` python
import tensorflow as tf

tf.InteractiveSession()
# 创建一个FIFO队列，初始化队列插入0.1、0.2、0.3这三个数字
q = tf.FIFOQueue(3, "float")
init = q.enqueue_many(([0.1, 0.2, 0.3],))
# 定义出队、“+2”、入队操作
x = q.dequeue()
y = x + 2
q_inc = q.enqueue([y])

with tf.Session() as sess:
    sess.run(init)

    for i in range(2):
        sess.run(q_inc)  # 执行2次操作，队列的值变为0.3、2.1、2.2

    quelen = sess.run(q.size())

    for i in range(quelen):
        print(sess.run(q.dequeue()))
```

执行结果：

``` python
0.3
2.1
2.2
```

注意，如果一次性入列超过`Queue Size`的数据，`enqueue`操作会卡住，直到有数据(被其他线程)从队列取出；对一个已经取空的队列使用`dequeue`操作也会卡住，直到有新的数据(从其他线程)写入。

#### RandomShuffleQueue

&emsp;&emsp;`RandomShuffleQueue`在`TensorFlow`使用异步计算时非常重要，因为`TensorFlow`的会话是支持多线程的，可以在主线程里执行训练操作，使用`RandomShuffleQueue`作为训练输入，开多个线程来准备训练样本，将样本压入队列后，主线程会从队列中每次取出`mini-batch`的样本进行训练。

``` python
import tensorflow as tf

tf.InteractiveSession()
# 最大长度为10，最小长度为2，类型为float的随机队列
q = tf.RandomShuffleQueue(capacity=10, min_after_dequeue=2, dtypes='float')

sess = tf.Session()

for i in range(0, 10):
    sess.run(q.enqueue(i))

for i in range(0, 8):
    print(sess.run(q.dequeue()))
```

执行结果是乱序的：

``` python
4.0
0.0
2.0
3.0
8.0
5.0
1.0
7.0
```

#### QueueRunner

&emsp;&emsp;`Tensorflow`的计算主要在使用`CPU/GPU`和内存，而数据读取涉及磁盘操作，速度远低于前者操作。因此通常会使用多个线程读取数据，然后使用一个线程消费数据。`QueueRunner`就是来管理这些读写队列的线程的。
&emsp;&emsp;`QueueRunner`需要与`Queue`一起使用，但并不一定必须使用`Coordinator`。

``` python
import tensorflow as tf

q = tf.FIFOQueue(10, "float")
counter = tf.Variable(0.0)  # 计数器
increment_op = tf.assign_add(counter, 1.0)  # 给计数器加一
enqueue_op = q.enqueue(counter)  # 将计数器加入队列
# 创建QueueRunner，用多个线程向队列添加数据。这里实际创建了4个线程，两个增加计数，两个执行入队
qr = tf.train.QueueRunner(q, enqueue_ops=[increment_op, enqueue_op] * 2)
sess = tf.InteractiveSession()  # 主线程
tf.global_variables_initializer().run()
qr.create_threads(sess, start=True)  # 启动入队线程

for i in range(20):
    print(sess.run(q.dequeue()))
```

执行结果：

``` python
3.0
6.0
11.0
13.0
18.0
21.0
25.0
26.0
28.0
31.0
```

增加计数的进程会不停的后台运行，执行入队的进程会先执行`10`次(因为队列长度只有`10`)，然后主线程开始消费数据。当一部分数据消费被后，入队的进程又会开始执行。最终主线程消费完`20`个数据后停止，但其他线程继续运行，程序不会结束。

#### Coordinator

&emsp;&emsp;`Coordinator`是个用来保存线程组运行状态的协调器对象，它和`TensorFlow`的`Queue`没有必然关系，可以单独和`Python`线程使用。

``` python
import tensorflow as tf

q = tf.FIFOQueue(1000, 'float')
counter = tf.Variable(0.0)  # 计数器
increment_op = tf.assign_add(counter, tf.constant(1.0))  # 计数器加一
enqueue_op = q.enqueue(counter)  # 入队
# 线程面向队列q，启动2个线程，每个线程中是[in, en]两个操作
qr = tf.train.QueueRunner(q, enqueue_ops=[increment_op, enqueue_op] * 2)

sess = tf.Session()
sess.run(tf.global_variables_initializer())

coord = tf.train.Coordinator()
# 线程管理器启动线程，接收协调器管理
enqueue_thread = qr.create_threads(sess, coord=coord, start=True)

for i in range(0, 10):
    print(sess.run(q.dequeue()))

coord.request_stop()  # 向各个线程发终止信号
coord.join(enqueue_thread)  # 等待各个线程成功结束
```

执行结果：

``` python
5.0
8.0
11.0
17.0
21.0
38.0
42.0
45.0
48.0
54.0
```