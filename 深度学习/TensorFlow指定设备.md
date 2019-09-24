---
title: TensorFlow指定设备
date: 2019-01-16 14:08:23
categories: 深度学习
---
### 支持的设备

&emsp;&emsp;在一套标准的系统上通常有多个计算设备，`TensorFlow`支持`CPU`和`GPU`这两种设备。我们用指定字符串`strings`来标识这些设备：

- `/cpu:0`：机器中的`CPU`。
- `/gpu:0`：机器中的`GPU`，如果你有一个的话。
- `/gpu:1`：机器中的第二个`GPU`，以此类推。

如果一个`TensorFlow`的`operation`中兼有`CPU`和`GPU`的实现，当这个算子被指派设备时，`GPU`有优先权。

### 记录设备指派情况

&emsp;&emsp;为了获取你的`operations`和`Tensor`被指派到哪个设备上运行，用`log_device_placement`新建一个`session`，并设置为`True`：

``` python
import tensorflow as tf
​
# 新建一个graph
a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
c = tf.matmul(a, b)
# 新建“session with log_device_placement”，并设置为True
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
print(sess.run(c))  # 运行这个op
```

执行结果：

``` python
Device mapping:
/job:localhost/replica:0/task:0/device:GPU:0 -> device: 0, \
    name: GeForce MX150,
pci bus id: 0000:01:00.0, compute capability: 6.1
MatMul: (MatMul): /job:localhost/replica:0/task:0/device:GPU:0
b: (Const): /job:localhost/replica:0/task:0/device:GPU:0
a: (Const): /job:localhost/replica:0/task:0/device:GPU:0
[[22. 28.]
 [49. 64.]]
```

### 手工指派设备

&emsp;&emsp;如果你不想使用系统来为`operation`指派设备，而是手工指派设备，可以用`with tf.device`创建一个设备环境，这个环境下的`operation`都统一运行在环境指定的设备上：

``` python
import tensorflow as tf
​
with tf.device('/cpu:0'):
    a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
    b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
​
c = tf.matmul(a, b)
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
print(sess.run(c))
```

执行结果：

``` python
Device mapping:
/job:localhost/replica:0/task:0/device:GPU:0 -> device: 0, \
    name: GeForce MX150,
pci bus id: 0000:01:00.0, compute capability: 6.1
MatMul: (MatMul): /job:localhost/replica:0/task:0/device:GPU:0
b: (Const): /job:localhost/replica:0/task:0/device:CPU:0
a: (Const): /job:localhost/replica:0/task:0/device:CPU:0
[[22. 28.]
 [49. 64.]]
```

你会发现`a`和`b`操作都被指派给了`cpu:0`。
&emsp;&emsp;为了避免出现指定设备不存在的情况，可以在创建的`session`里把参数`allow_soft_placement`设置为`True`，这样`tensorFlow`会自动选择一个存在并且支持的设备来运行`operation`：

``` python
import tensorflow as tf
​
with tf.device('/gpu:2'):
    a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
    b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')

c = tf.matmul(a, b)
sess = tf.Session(config=tf.ConfigProto(allow_soft_placement=True,
                                        log_device_placement=True))
print(sess.run(c))
```

### 使用多个GPU

&emsp;&emsp;如果你想让`TensorFlow`在多个`GPU`上运行，你可以建立`multi-tower`结构，在这个结构里，每个`tower`分别被指配给不同的`GPU`运行：

``` python
import tensorflow as tf
​
c = []

for d in ['/gpu:2', '/gpu:3']:
    with tf.device(d):
        a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3])
        b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2])
        c.append(tf.matmul(a, b))
​
with tf.device('/cpu:0'):
    sum = tf.add_n(c)
​
# 新建“session with log_device_placement”，并设置为True
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
print(sess.run(sum))  # 运行这个op
```

执行结果：

``` python
Device mapping:
/job:localhost/replica:0/task:0/gpu:0 -> device: 0, name: Tesla K20m, \
    pci bus id: 0000:02:00.0
/job:localhost/replica:0/task:0/gpu:1 -> device: 1, name: Tesla K20m, \
    pci bus id: 0000:03:00.0
/job:localhost/replica:0/task:0/gpu:2 -> device: 2, name: Tesla K20m, \
    pci bus id: 0000:83:00.0
/job:localhost/replica:0/task:0/gpu:3 -> device: 3, name: Tesla K20m, \
    pci bus id: 0000:84:00.0
Const_3: /job:localhost/replica:0/task:0/gpu:3
Const_2: /job:localhost/replica:0/task:0/gpu:3
MatMul_1: /job:localhost/replica:0/task:0/gpu:3
Const_1: /job:localhost/replica:0/task:0/gpu:2
Const: /job:localhost/replica:0/task:0/gpu:2
MatMul: /job:localhost/replica:0/task:0/gpu:2
AddN: /job:localhost/replica:0/task:0/cpu:0
[[44. 56.]
 [98. 128.]]
```

### 限制GPU资源使用

&emsp;&emsp;为了加快运行效率，`TensorFlow`在初始化时会尝试分配所有可用的`GPU`显存资源给自己，这在多人使用的服务器上工作时就会导致`GPU`占用，别人无法使用`GPU`工作的情况。
&emsp;&emsp;`tf`提供了两种控制`GPU`资源使用的方法，一是让`TensorFlow`在运行过程中动态申请显存，需要多少就申请多少，第二种方式就是限制`GPU`的使用率。
&emsp;&emsp;动态申请显存如下：

``` python
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
session = tf.Session(config=config)
```

&emsp;&emsp;限制`GPU`使用率：

``` python
config = tf.ConfigProto()
# 占用40%显存
config.gpu_options.per_process_gpu_memory_fraction = 0.4
session = tf.Session(config=config)
# 或者
gpu_options=tf.GPUOptions(per_process_gpu_memory_fraction=0.4)
config=tf.ConfigProto(gpu_options=gpu_options)
session = tf.Session(config=config)
```

&emsp;&emsp;设置使用哪块`GPU`，可以是在`python`程序中设置：

``` python
os.environ['CUDA_VISIBLE_DEVICES'] = '0'  # 使用“GPU 0”
os.environ['CUDA_VISIBLE_DEVICES'] = '0,1'  # 使用“GPU 0”和“GPU 1”
```

---

&emsp;&emsp;`tf.device`是`tf.Graph.device`的一个包装，是一个用于指定新创建的操作(`operation`)的默认设备的环境管理器。参数为`device_name_or_function`，可以传入一个设备字符串或者环境操作函数(如`tf.DeviceSpec`)。

- 如果传入的是一个设备名称字符串，那么在此环境中构造的所有操作都将被分配给带有该名称的设备，除非被其他嵌套的设备环境(其他的`tf.device`)所覆盖。
- 如果传入的是一个函数，它将被当作一个从操作对象到设备名称字符串的函数，并在每次创建新操作时调用它。操作将被分配给带有返回名称的设备。
- 如果是`None`，所有的来自代码段上下文的设备调用将被忽略。

``` python
with g.device('/device:GPU:0'):
    # All operations constructed in this context will be placed on GPU 0
with g.device(None):
    # All operations constructed in this context will have no assigned device
​
# Defines a function from "Operation" to device string
def matmul_on_gpu(n):
    if n.type == "MatMul":
        return "/device:GPU:0"
    else:
        return "/cpu:0"
​
with g.device(matmul_on_gpu):
    # All operations of type "MatMul" constructed in this context will be
    # placed on GPU 0; all other operations will be placed on CPU 0
```

&emsp;&emsp;`tf.DeviceSpec`返回的是部分或者全部的设备指定，在整个`graph`中来描述状态存储和计算发生的位置，并且允许解析设备规范的字符串，以验证它们的有效性，然后合并它们或以编码方式组合它们：

``` python
# Place the operations on device "GPU:0" in the "ps" job
device_spec = DeviceSpec(job="ps", device_type="GPU", device_index=0)

with tf.device(device_spec):
    # Both my_var and squared_var will be placed on /job:ps/device:GPU:0
    my_var = tf.Variable(..., name="my_variable")
    squared_var = tf.square(my_var)
```

如果一个`DeviceSpec`被部分指定，将根据定义的范围与其他`DeviceSpecs`合并，在内部内定义的`DeviceSpec`组件优先于在外层内定义的组件：

``` python
with tf.device(DeviceSpec(job="train",)):
    with tf.device(DeviceSpec(job="ps", device_type="GPU", device_index=0):
        # Nodes created here will be assigned to /job:ps/device:GPU:0
    with tf.device(DeviceSpec(device_type="GPU", device_index=1):
        # Nodes created here will be assigned to /job:train/device:GPU:1
```

&emsp;&emsp;`DeviceSpec`的参数如下：

- `job`：作业名称。
- `task`：任务索引。
- `device_type`：设备类型(`CPU`或`GPU`)。
- `device_index`：设备索引，如果未指定，则可以使用任意的设备。