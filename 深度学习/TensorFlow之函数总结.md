---
title: TensorFlow之函数总结
categories: 深度学习
abbrlink: aa17735e
date: 2019-02-13 19:48:39
---
### get_shape函数

&emsp;&emsp;`get_shape`函数主要用于获取一个张量的维度，并且输出张量每个维度上面的值。如果是二维矩阵，也就是输出行和列的值，使用非常方便。<!--more-->

``` python
import tensorflow as tf

with tf.Session() as sess:
    A = tf.random_normal(shape=[3, 4])
    print(A.get_shape())
    print(A.get_shape)
```

执行结果：

``` python
(3, 4)
<bound method Tensor.get_shape of <tf.Tensor 'random_normal:0' shape=(3, 4) dtype=float32>>
```

第一个输出是一个元祖，就是数值，而第二输出就是一个张量的对象，里面包含更多的东西。如果你需要输出某一个维度上面的值，那就用下面的这种方式：

``` python
A.get_shape()[0]
```

这就表示第一个维度。该函数经常和`as_list`一起使用：

``` python
import tensorflow as tf

varX = tf.constant([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
sess = tf.Session()
varX_shape = tf.shape(varX)
print(sess.run(varX_shape))  # 输出“[3 3]”
varX_shape = varX.get_shape().as_list()
print(varX_shape)  # 输出“[3, 3]”
```

### random_normal函数

&emsp;&emsp;`tf.random_normal`函数用于从服从指定正态分布的数值中取出指定个数的值：

``` python
tf.random_normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None)
```

- `shape`：输出张量的形状。
- `mean`：正态分布的均值。
- `stddev`是正态分布的标准差。
- `dtype`：输出的类型。
- `seed`：随机数种子，是一个整数。
- `name`：操作的名称。

&emsp;&emsp;以下程序定义一个`w1`变量：

``` python
import tensorflow as tf

w1 = tf.Variable(tf.random_normal([2, 3], stddev=1, seed=1))

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print(w1)
    print(sess.run(w1))
```

执行结果：

``` python
<tf.Variable 'Variable:0' shape=(2, 3) dtype=float32_ref>
[[-0.8113182   1.4845988   0.06532937]
 [-2.4427042   0.0992484   0.5912243 ]]
```

变量`w1`声明之后并没有被赋值，需要在`Session`中调用`run(tf.global_variables_initializer())`方法初始化之后才会被具体赋值。`tf`中张量与常规向量不同的是，执行`print(w1)`输出的是`w1`的形状和数据类型等属性信息，获取`w1`的值需要调用`sess.run(w1)`方法。

### TensorFlow的算术操作

&emsp;&emsp;`TensorFlow`的算术操作如下：

操作                                          | 描述
----------------------------------------------|-------
`tf.add(x, y, name=None)`                     | 求和
`tf.multiply(x, y, name=None)`                | 减法
`tf.multiply(x, y, name=None)`                | 乘法
`tf.div(x, y, name=None)`，推荐使用`tf.divide` | 除法
`tf.mod(x, y, name=None)`                     | 取模
`tf.abs(x, name=None)`                        | 求绝对值
`tf.negative(x, name=None)`                   | 取负(`y = -x`)
`tf.sign(x, name=None)`                       | 返回符号
`tf.inv(x, name=None)`                        | 取反
`tf.square(x, name=None)`                     | 计算平方
`tf.round(x, name=None)`                      | 四舍五入最接近的整数
`tf.sqrt(x, name=None)`                       | 开根号
`tf.pow(x, y, name=None)`                     | 幂次方
`tf.exp(x, name=None)`                        | 计算`e`的次方
`tf.log(x, name=None)`                        | 计算`log`
`tf.maximum(x, y, name=None)`                 | 返回最大值
`tf.minimum(x, y, name=None)`                 | 返回最小值
`tf.cos(x, name=None)`                        | 三角函数`cos`
`tf.sin(x, name=None)`                        | 三角函数`sin`
`tf.tan(x, name=None)`                        | 三角函数`tan`
`tf.atan(x, name=None)`                       | 三角函数`atan`
`tf.sign(x, name=None)`                       | `y = sign(x) = -1 if x < 0; 0 if x == 0; 1 if x > 0`
`tf.round(x, name=None)`                      | `a is [0.9, 2.5, 2.3, -4.4], tf.round(a) is [1.0, 3.0, 2.0, -4.0]`
`tf.pow(x, y, name=None)`                     | `x is [[2, 2], [3, 3]], y is [[8, 16], [2, 3]], tf.pow(x, y) is [[256, 65536], [9, 27]]`

### assign函数

&emsp;&emsp;`tf.assign(A, new_number)`的功能是把`A`的值变为`new_number`：

``` python
import tensorflow as tf

state = tf.Variable(0, name='counter')
one = tf.constant(1)  # 定义常量one
new_value = tf.add(state, one)  # 定义加法步骤(注意，此步并没有直接计算)
update = tf.assign(state, new_value)  # 将State更新成new_value
init = tf.global_variables_initializer()

with tf.Session() as sess:
    sess.run(init)

    for _ in range(3):
        sess.run(update)
        print(sess.run(state))
```

&emsp;&emsp;`assign`函数也可以用于给图变量赋值：

``` python
In [1]: import tensorflow as tf
In [2]: v = tf.Variable(3, name='v')
In [3]: v2 = v.assign(5)
In [4]: sess = tf.InteractiveSession()
In [5]: sess.run(v.initializer)
In [6]: sess.run(v)
Out[6]: 3
In [7]: sess.run(v2)
Out[7]: 5
```

### tf.contrib.layers.separable_conv2d

&emsp;&emsp;Aliases:

- `tf.contrib.layers.separable_conv2d`
- `tf.contrib.layers.separable_convolution2d`

``` python
tf.contrib.layers.separable_conv2d(
    inputs, num_outputs, kernel_size, depth_multiplier, stride=1, padding='SAME',
    data_format=DATA_FORMAT_NHWC, rate=1, activation_fn=tf.nn.relu, normalizer_fn=None,
    normalizer_params=None, weights_initializer=initializers.xavier_initializer(),
    weights_regularizer=None, biases_initializer=tf.zeros_initializer(), biases_regularizer=None,
    reuse=None, variables_collections=None, outputs_collections=None, trainable=True, scope=None)
```

Defined in `tensorflow/contrib/layers/python/layers/layers.py`. Adds a `depth-separable` `2D` convolution with optional `batch_norm` layer.
&emsp;&emsp;This op first performs a depthwise convolution that acts separately on channels, creating a variable called `depthwise_weights`. If `num_outputs` is not `None`, it adds a pointwise convolution that mixes channels, creating a variable called `pointwise_weights`. Then, if `normalizer_fn` is `None`, it adds bias to the result, creating a variable called `biases`, otherwise, `the normalizer_fn` is applied. It finally applies an activation function to produce the end result.

- `inputs`: A tensor of size `[batch_size, height, width, channels]`.
- `num_outputs`: The number of pointwise convolution output filters. If `num_outputs` is `None`, then we skip the pointwise convolution stage.
- `kernel_size`: A list of length `2`: `[kernel_height, kernel_width]` of the filters. Can be an `int` if both values are the same.
- `depth_multiplier`: The number of depthwise convolution output channels for each input channel.
- `stride`: A list of length `2`: `[stride_height, stride_width]`, specifying the depthwise convolution stride. Can be an `int` if both strides are the same.
- `padding`: One of `VALID` or `SAME`.
- `data_format`: A string. `NHWC` (default) and `NCHW` are supported.
- `rate`: A list of length `2`: `[rate_height, rate_width]`, specifying the dilation rates for atrous convolution. Can be an `int` if both rates are the same. If any value is larger than one, then both stride values need to be one.
- `activation_fn`: Activation function. The default value is a `ReLU` function. Explicitly set it to `None` to skip it and maintain a linear activation.
- `normalizer_fn`: Normalization function to use instead of biases. If `normalizer_fn` is provided then `biases_initializer` and `biases_regularizer` are ignored and biases are not created nor added. default set to `None` for no normalizer function
- `normalizer_params`: Normalization function parameters.
- `weights_initializer`: An initializer for the weights.
- `weights_regularizer`: Optional regularizer for the weights.
- `biases_initializer`: An initializer for the biases. If `None`, skip biases.
- `biases_regularizer`: Optional regularizer for the biases.
- `reuse`: Whether or not the layer and its variables should be reused. To be able to reuse the layer scope must be given.
- `variables_collections`: Optional list of collections for all the variables or a dictionary containing a different list of collection per variable.
- `outputs_collections`: Collection to add the outputs.
- `trainable`: Whether or not the variables should be trainable or not.
- `scope`: Optional scope for `variable_scope`.

Returns a `Tensor` representing the output of the operation.

### tf.contrib.layers.xavier_initializer

&emsp;&emsp;Aliases:

- `tf.contrib.layers.xavier_initializer`
- `tf.contrib.layers.xavier_initializer_conv2d`

``` python
tf.contrib.layers.xavier_initializer(uniform=True, seed=None, dtype=tf.float32)
```

Defined in `tensorflow/contrib/layers/python/layers/initializers.py`. Returns an initializer performing `Xavier` initialization for weights. This function implements the weight initialization from `Xavier Glorot` and `Yoshua Bengio`: Understanding the difficulty of training deep feedforward neural networks. International conference on artificial intelligence and statistics.
&emsp;&emsp;This initializer is designed to keep the scale of the gradients roughly the same in all layers.

- `uniform`: Whether to use uniform or normal distributed random initialization.
- `seed`: A `Python` integer. Used to create random seeds. See `tf.set_random_seed` for behavior.
- `dtype`: The data type. Only floating point types are supported.

### tf.contrib.layers.l2_regularizer

&emsp;&emsp;The function is:

``` python
tf.contrib.layers.l2_regularizer(scale, scope=None)
```

Defined in `tensorflow/contrib/layers/python/layers/regularizers.py`. Returns a function that can be used to apply `L2` regularization to weights. Small values of `L2` can help prevent overfitting the training data.

- `scale`: A scalar multiplier `Tensor`. `0.0` disables the regularizer.
- `scope`: An optional scope name.

### tf.placeholder_with_default

&emsp;&emsp;The function is:

``` python
tf.placeholder_with_default(input, shape, name=None)
```

Defined in `tensorflow/python/ops/gen_array_ops.py`. A placeholder op that passes through input when its output is not fed.

- `input`: A `Tensor`. The default value to produce when output is not fed.
- `shape`: A `tf.TensorShape` or list of `ints`. The (possibly partial) shape of the tensor.
- `name`: A name for the operation (optional).

Returns a `Tensor`. Has the same type as input.

### tf.split

&emsp;&emsp;The function is:

``` python
tf.split(value, num_or_size_splits, axis=0, num=None, name='split')
```

Defined in `tensorflow/python/ops/array_ops.py`. Splits a tensor into sub tensors.
&emsp;&emsp;If `num_or_size_splits` is an integer type, `num_split`, then splits `value` along dimension `axis` into `num_split` smaller tensors. Requires that `num_split` evenly divides `value.shape[axis]`.
&emsp;&emsp;If `num_or_size_splits` is not an integer type, it is presumed to be a `Tensor` `size_splits`, then splits `value` into `len(size_splits)` pieces. The shape of the `i-th` piece has the same size as the `value` except along dimension `axis` where the size is `size_splits[i]`.
&emsp;&emsp;For example:

``` python
# 'value' is a tensor with shape [5, 30]. Split 'value' into 3 tensors
# with sizes [4, 15, 11] along dimension 1
split0, split1, split2 = tf.split(value, [4, 15, 11], 1)
tf.shape(split0)  # [5, 4]
tf.shape(split1)  # [5, 15]
tf.shape(split2)  # [5, 11]
# Split 'value' into 3 tensors along dimension 1
split0, split1, split2 = tf.split(value, num_or_size_splits=3, axis=1)
tf.shape(split0)  # [5, 10]
```

- `value`: The `Tensor` to split.
- `num_or_size_splits`: Either a `0-D` integer `Tensor` indicating the number of splits along `split_dim` or a `1-D` integer `Tensor` containing the sizes of each output tensor along `split_dim`. If a scalar then it must evenly divide `value.shape[axis]`; otherwise the sum of sizes along the split dimension must match that of the value.
- `axis`: A `0-D` `int32` `Tensor`. The dimension along which to split. Must be in the range `[-rank(value), rank(value))`.
- `num`: Optional, used to specify the number of outputs when it cannot be inferred from the shape of `size_splits`.
- `name`: A `name` for the operation (optional).

&emsp;&emsp;Returns: if `num_or_size_splits` is a scalar returns `num_or_size_splits` `Tensor` objects; if `num_or_size_splits` is a `1-D` `Tensor` returns `num_or_size_splits.get_shape[0]` `Tensor` objects resulting from splitting value.

### tf.reshape

&emsp;&emsp;`tf.reshape(tensor, shape, name=None)`的作用是将`tensor`变换为参数`shape`的形式。其中`shape`为一个列表形式，特殊的一点是列表中可以存在`-1`，`-1`代表的含义是不用我们自己指定这一维的大小，函数会自动计算，但列表中只能存在一个`-1`(当然如果存在多个`-1`，就是一个存在多解的方程了)。
&emsp;&emsp;`TensorFlow`根据`shape`变换矩阵的方式为`reshape(t, shape) => reshape(t, [-1]) => reshape(t, shape)`，首先将矩阵t变为一维矩阵，然后再对矩阵的形式进行更改。

``` python
# tensor 't' is [1, 2, 3, 4, 5, 6, 7, 8, 9]. tensor 't' has shape [9]
reshape(t, [3, 3]) ==> [[1, 2, 3],
                        [4, 5, 6],
                        [7, 8, 9]]
# tensor 't' is [[[1, 1], [2, 2]],
#                [[3, 3], [4, 4]]]
# tensor 't' has shape [2, 2, 2]
reshape(t, [2, 4]) ==> [[1, 1, 2, 2],
                        [3, 3, 4, 4]]
# tensor 't' is [[[1, 1, 1],
#                 [2, 2, 2]],
#                [[3, 3, 3],
#                 [4, 4, 4]],
#                [[5, 5, 5],
#                 [6, 6, 6]]]
# tensor 't' has shape [3, 2, 3]. pass '[-1]' to flatten 't'
reshape(t, [-1]) ==> [1, 1, 1, 2, 2, 2, 3, 3, 3, 4, 4, 4, 5, 5, 5, 6, 6, 6]
# -1 can also be used to infer the shape
reshape(t, [2, -1]) ==> [[1, 1, 1, 2, 2, 2, 3, 3, 3],  # -1 is inferred to be 9
                         [4, 4, 4, 5, 5, 5, 6, 6, 6]]
reshape(t, [-1, 9]) ==> [[1, 1, 1, 2, 2, 2, 3, 3, 3],  # -1 is inferred to be 2
                         [4, 4, 4, 5, 5, 5, 6, 6, 6]]
reshape(t, [ 2, -1, 3]) ==> [[[1, 1, 1],  # -1 is inferred to be 3
                              [2, 2, 2],
                              [3, 3, 3]],
                             [[4, 4, 4],
                              [5, 5, 5],
                              [6, 6, 6]]]
reshape(t, []) ==> 7  # tensor 't' is [7]. shape `[]` reshapes to a scalar
```

### tf.shape

&emsp;&emsp;The function is:

``` python
tf.shape(input, name=None, out_type=tf.int32)
```

Defined in `tensorflow/python/ops/array_ops.py`. Returns the shape of a tensor. This operation returns a `1-D` integer tensor representing the shape of input.

``` python
t = tf.constant([[[1, 1, 1], [2, 2, 2]], [[3, 3, 3], [4, 4, 4]]])
tf.shape(t)  # [2, 2, 3]
```

- `input`: A `Tensor` or `SparseTensor`.
- `name`: A name for the operation (optional).
- `out_type`: (Optional) The specified output type of the operation (`int32` or `int64`).

### tf.concat

&emsp;&emsp;该函数用于连接两个矩阵的操作：

``` python
tf.concat(concat_dim, values, name='concat')
```

- `concat_dim`：`tensor`连接的方向(维度)，`cancat_dim`维度可以不一样，其他维度的尺寸必须一样。
- `values`：两个或者一组待连接的`tensor`。
- `name`：指定该操作的`name`。

``` python
t1 = [[1, 2, 3], [4, 5, 6]]
t2 = [[7, 8, 9], [10, 11, 12]]
tf.concat(0, [t1, t2]) => [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]]
tf.concat(1, [t1, t2]) => [[1, 2, 3, 7, 8, 9], [4, 5, 6, 10, 11, 12]]
```

这里要注意的是，如果是两个向量，它们是无法调用`tf.concat(1, [t1, t2])`来连接的，因为它们对应的`shape`只有一个维度，当然不能在第二维上连了，虽然实际中两个向量可以在行上连，但是放在程序里是会报错的。如果要连，必须要调用`tf.expand_dims`来扩维：

``` python
t1 = tf.constant([1, 2, 3])
t2 = tf.constant([4, 5, 6])
# concated = tf.concat(1, [t1, t2])  # 这样会报错
t1 = tf.expand_dims(tf.constant([1, 2, 3]),1)
t2 = tf.expand_dims(tf.constant([4, 5, 6]),1)
concated = tf.concat(1, [t1, t2])  # 这样就是正确的
```

### tf.add_n

&emsp;&emsp;The function is:

``` python
tf.add_n(inputs, name=None)
```

Defined in `tensorflow/python/ops/math_ops.py`. Adds all input tensors `element-wise`.

- `inputs`: A list of `Tensor` objects, each with same shape and type.
- `name`: A `name` for the operation (optional).

&emsp;&emsp;Returns a `Tensor` of same shape and type as the elements of `inputs`.

### tf.squeeze

&emsp;&emsp;The function is:

``` python
tf.squeeze(input, squeeze_dims=None, name=None)
```

Defined in `tensorflow/python/ops/array_ops.py`. Removes dimensions of size `1` from the shape of a tensor.
&emsp;&emsp;Given a tensor `input`, this operation returns a tensor of the same type with all dimensions of size `1` removed. If you don't want to remove all size `1` dimensions, you can remove specific size `1` dimensions by specifying axis.

``` python
# 't' is a tensor of shape [1, 2, 1, 3, 1, 1]
tf.shape(tf.squeeze(t))  # [2, 3]
Or, to remove specific size 1 dimensions:
# 't' is a tensor of shape [1, 2, 1, 3, 1, 1]
tf.shape(tf.squeeze(t, [2, 4]))  # [1, 2, 3, 1]
```

- `input`: A `Tensor`. The input to squeeze.
- `axis`: An optional list of `ints`. If specified, only squeezes the dimensions listed. The dimension index starts at `0`. It is an error to squeeze a dimension that is not `1`. Must be in the range `[-rank(input), rank(input))`.
- `name`: A `name` for the operation (optional).
- `squeeze_dims`: Deprecated keyword argument that is now `axis`.

&emsp;&emsp;Return a `Tensor`. Has the same type as `input`. Contains the same data as `input`, but has one or more dimensions of size `1` removed.

### tf.expand_dims

&emsp;&emsp;The function is:

``` python
tf.expand_dims(input, axis=None, name=None, dim=None)
```

Defined in `tensorflow/python/ops/array_ops.py`. Inserts a dimension of `1` into a tensor's shape.
&emsp;&emsp;Given a tensor `input`, this operation inserts a dimension of `1` at the dimension index `axis` of `input's` shape. The dimension index `axis` starts at `zero`; if you specify a negative number for `axis` it is counted backward from the end.
&emsp;&emsp;This operation is useful if you want to add a batch dimension to a single element. For example, if you have a single image of shape `[height, width, channels]`, you can make it a batch of `1` image with `expand_dims(image, 0)`, which will make the shape `[1, height, width, channels]`.
&emsp;&emsp;Other examples:

``` python
# 't' is a tensor of shape [2]
tf.shape(tf.expand_dims(t, 0))  # [1, 2]
tf.shape(tf.expand_dims(t, 1))  # [2, 1]
tf.shape(tf.expand_dims(t, -1))  # [2, 1]
# 't2' is a tensor of shape [2, 3, 5]
tf.shape(tf.expand_dims(t2, 0))  # [1, 2, 3, 5]
tf.shape(tf.expand_dims(t2, 2))  # [2, 3, 1, 5]
tf.shape(tf.expand_dims(t2, 3))  # [2, 3, 5, 1]
```

This operation requires that:

``` python
-1 - input.dims() <= dim <= input.dims()
```

This operation is related to `squeeze()`, which removes dimensions of size `1`.

- `input`: A `Tensor`.
- `axis`: `0-D` (scalar). Specifies the dimension index at which to expand the shape of `input`. Must be in the range `[-rank(input) - 1, rank(input)]`.
- `name`: The `name` of the output `Tensor`.
- `dim`: `0-D` (scalar). Equivalent to `axis`, to be deprecated.

&emsp;&emsp;Return a `Tensor` with the same data as `input`, but its shape has an additional dimension of size `1` added.

### tf.Session.as_default

&emsp;&emsp;如果使用关键字`with`来指定会话，可以在会话中执行`Operation.run`或`Tensor.eval`，以得到运行的`tensor`结果：

``` python
c = tf.constant(...)
sess = tf.Session()

with sess.as_default():
    assert tf.get_default_session() is sess
    print(c.eval())
```

使用函数`tf.get_default_session`来得到当前默认的会话。需要注意的是，退出`as_default`上下文管理器时，并没有关闭该会话(`session`)，所以你必须明确地关闭会话：

``` python
c = tf.constant(...)
sess = tf.Session()

with sess.as_default():
    print(c.eval())

with sess.as_default():
    print(c.eval())

sess.close()  # 关闭会话
```

而使用`with tf.Session()`的方式可以创建并自动关闭会话。

### 错误类(Error classes)

&emsp;&emsp;错误类如下：

操作                                              | 描述
--------------------------------------------------|------
`class tf.OpError`                                | 一个基本的错误类型，在当TF执行失败时候报错
`tf.OpError.op`                                   | 返回执行失败的操作节点，有的操作(如`Send`或`Recv`)可能不会返回，那就要用用到`node_def`方法
`tf.OpError.node_def`                             | 以`NodeDef proto`形式表示失败的`op`
`tf.OpError.error_code`                           | 描述该错误的整数错误代码
`tf.OpError.message`                              | 返回错误信息
`class tf.errors.CancelledError`                  | 当操作或者阶段被取消时候报错
`class tf.errors.UnknownError`                    | 未知错误类型
`class tf.errors.InvalidArgumentError`            | 在接收到非法参数时候报错
`class tf.errors.NotFoundError`                   | 当发现不存在所请求的一个实体时候，比如文件或目录
`class tf.errors.AlreadyExistsError`              | 当创建的实体已经存在的时候报错
`class tf.errors.PermissionDeniedError`           | 没有执行权限做某操作的时候报错
`class tf.errors.FailedPreconditionError`         | 系统没有条件执行某个行为时候报错
`class tf.errors.AbortedError`                    | 操作中止时报错，常常发生在并发情形
`class tf.errors.OutOfRangeError`                 | 超出范围报错
`class tf.errors.UnimplementedError`              | 某个操作没有执行时报错
`class tf.errors.InternalError`                   | 当系统经历了一个内部错误时报出
`class tf.errors.ResourceExhaustedError`          | 资源耗尽时报错
`class tf.errors.DataLossError`                   | 当出现不可恢复的错误，例如在运行`tf.WholeFileReader.read`读取整个文件的同时文件被删减
`tf.errors.XXXXX.__init__(node_def, op, message)` | 使用该形式方法创建以上各种错误类

### tf.one_hot

&emsp;&emsp;该函数用于将输入转换成`one-hot`形式：

``` python
tf.one_hot(indices, depth, on_value, off_value, axis)
```

- `indices`：非负整数表示的标签列表，`len(indices)`就是分类的类别数。`tf.one_hot`返回的张量的阶数为`indeces`的阶数加上`1`。当`indices`的某个分量取`-1`时，即对应的向量没有独热值。
- `depth`：每个独热向量的维度。
- `on_value`：独热值。
- `off_value`：非独热值。
- `axis`：指定第几阶为`depth`维独热向量，默认为`-1`，即指定张量的最后一维为独热向量。例如对于一个`2`阶张量而言，`axis = 0`时，每个列向量是一个独热的`depth`维向量；`axis = 1`时，每个行向量是一个独热的`depth`维向量。

``` python
import tensorflow as tf
import numpy as np

z = np.random.randint(0, 10, size=[10])
y = tf.one_hot(z, 10, on_value=1, off_value=None, axis=0)

with tf.Session()as sess:
    print(z)
    print(sess.run(y))
```

执行结果：

``` python
[4 4 5 8 9 0 5 0 3 1]
[[0 0 0 0 0 1 0 1 0 0]
 [0 0 0 0 0 0 0 0 0 1]
 [0 0 0 0 0 0 0 0 0 0]
 [0 0 0 0 0 0 0 0 1 0]
 [1 1 0 0 0 0 0 0 0 0]
 [0 0 1 0 0 0 1 0 0 0]
 [0 0 0 0 0 0 0 0 0 0]
 [0 0 0 0 0 0 0 0 0 0]
 [0 0 0 1 0 0 0 0 0 0]
 [0 0 0 0 1 0 0 0 0 0]]
```

### tf.sparse_to_dense

&emsp;&emsp;函数原型如下：

``` python
tf.sparse_to_dense(sparse_indices, output_shape, sparse_values, default_value, name=None)
```

这个函数的作用是将一个稀疏表示转换成一个密集张量。具体将稀疏张量`sparse`转换成密集张量`dense`的步骤如下：

``` python
# If sparse_indices is scalar
dense[i] = (i == sparse_indices ? sparse_values : default_value)
# If sparse_indices is a vector, then for each i
dense[sparse_indices[i]] = sparse_values[i]
# If sparse_indices is an n by d matrix, then for each i in [0, n)
dense[sparse_indices[i][0], ..., sparse_indices[i][d-1]] = sparse_values[i]
```

默认情况下，`dense`中的填充值`default_value`都是`0`，除非该值被设置成一个标量。

- `sparse_indices`是稀疏矩阵中那些个别元素对应的索引值，有三种情况：

1. 如果`sparse_indices`是个数，那么它只能指定一维矩阵的某一个元素。
2. 如果`sparse_indices`是个向量，那么它可以指定一维矩阵的多个元素。
3. 如果`sparse_indices`是个矩阵，那么它可以指定二维矩阵的多个元素。

- `output_shape`是输出的稀疏矩阵的`shape`。
- `sparse_values`是个别元素的值，分为两种情况：

1. 如果`sparse_values`是个数，则所有索引指定的位置都用这个数。
2. 如果`sparse_values`是个向量，则输出矩阵的某一行向量里某一行对应的数(所以这里向量的长度应该和输出矩阵的行数对应，不然报错)。

- `default_value`是未指定元素的默认值，一般如果是稀疏矩阵，就是`0`了。

``` python
import tensorflow as tf

BATCH_SIZE = 5
# 真实标签，shape为[5, 1]
label = tf.expand_dims(tf.constant([1, 3, 5, 7, 9]), 1)
# 真实标签的索引，shape为[5, 1]
index = tf.expand_dims(tf.range(0, BATCH_SIZE), 1)
# 将标签和索引tensor在第二个维度上连接起来，新的concated的shape为[5, 2]
concated = tf.concat([index, label], 1)
# onehot_labels的shape为[5, 10]
onehot_labels = tf.sparse_to_dense(concated, [BATCH_SIZE, 10], 1.0, 0.0)

with tf.Session() as sess:
    print(sess.run(concated))
    print("----------------")
    onehot1 = sess.run(onehot_labels)
    print(onehot1)
```

执行结果：

``` python
[[0 1]
 [1 3]
 [2 5]
 [3 7]
 [4 9]]
----------------
[[0. 1. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 1. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 1. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 1. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 1.]]
```

&emsp;&emsp;如果`output_shape`是一个行向量：

``` python
import tensorflow as tf

predicted_class = [1, 3, 5, 7, 9]
nb_classes = 10
one_hot = tf.sparse_to_dense(predicted_class, [nb_classes], 1.0)

with tf.Session() as sess:
    onehot1 = sess.run(one_hot)
    print(onehot1)  # 输出“[0. 1. 0. 1. 0. 1. 0. 1. 0. 1.]”
```

### tf.nn.in_top_k

&emsp;&emsp;`tf.nn.in_top_k`用于计算预测的结果和实际结果的是否相等，并返回一个`bool`类型的张量：

``` python
tf.nn.in_top_k(prediction, target, K)
```

- `prediction`：预测的结果，大小就是预测样本的数量乘以输出的维度。
- `target`：实际样本类别的标签，大小就是样本数量的个数。
- `K`：每个样本的预测结果的前`k`个最大的数里面是否包含`targets`预测中的标签，一般都是取`1`。

``` python
import tensorflow as tf

A = [[0.8, 0.6, 0.3], [0.1, 0.6, 0.4]]
B = [1, 1]
out = tf.nn.in_top_k(A, B, 1)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print(sess.run(out))  # 输出“[False  True]”
```

因为`A`张量里面的第一个元素的最大值的标签是`0`，第二个元素的最大值的标签是`1`，但实际上是`1`和`1`，所以输出就是`False`和`True`。如果把`K`改成`2`，那么第一个元素的前面`2`个最大元素的位置是`0`和`1`，第二个元素的就是`1`和`2`。而`B`是`[1, 1]`，包含在里面，所以输出结果就是`True`和`True`。

### initialized_value

&emsp;&emsp;你有时候会需要用另一个变量的初始化值给当前变量初始化。由于`tf.initialize_all_variables`是并行地初始化所有变量，所以在有这种需求的情况下需要小心。
&emsp;&emsp;用其它变量的值初始化一个新的变量时，使用其它变量的`initialized_value`属性。你可以直接把已初始化的值作为新变量的初始值，或者把它当做`tensor`计算得到一个值赋予新变量：

``` python
# Create a variable with a random value
weights = tf.Variable(tf.random_normal([784, 200], stddev=0.35), name="weights")
# Create another variable with the same value as 'weights'
w2 = tf.Variable(weights.initialized_value(), name="w2")
# Create another variable with twice the value of 'weights'
w_twice = tf.Variable(weights.initialized_value() * 0.2, name="w_twice")
```

### tf.py_func

&emsp;&emsp;这是一个可以把`TensorFlow`和`Python`原生代码无缝衔接起来的函数，有了它，你就可以在`TensorFlow`里面自由的实现你想要的功能，而不用考虑`TensorFlow`有没有实现它的`API`，并且可以帮助我们实现自由地检查该功能模块的输入输出是否正确，而不受到`TensorFlow`的先构造计算图再运行导致的不能单独检测单一模块的功能的限制。
&emsp;&emsp;它的具体功能描述是包装一个普通的`Python`函数，这个函数接受`numpy`的数组作为输入和输出，让这个函数可以作为`TensorFlow`计算图上的计算节点`OP`来使用：

``` python
py_func(func, inp, Tout, stateful=True, name=None)
```

- `func`：一个`Python`函数，它接受`NumPy`数组作为输入和输出，并且数组的类型和大小必须和输入和输出用来衔接的`Tensor`大小和数据类型相匹配。
- `inp`：输入的`Tensor`列表。
- `Tout`：输出`Tensor`数据类型的列表或元祖。
- `stateful`：状态，布尔值。
- `name`：节点`OP`的名称。

&emsp;&emsp;`operation`分为有状态的与无状态的`operation`：无状态的`operation`主要进行数学计算，比如矩阵乘法、加法等，如果给该`OP`同一个输入，那么将会得到同一个输出；有状态的`operation`(`stateful operation`)分为`variable`以及`queue`，`variable`负责保存机器学习模型的模型参数，`queue`提供更加复杂的模型架构，给定同一个输入，可能会得到不同的输出。`common subexpression elimination`(`CSE`)公共子表达式消除只会在无状态的节点`OP`上执行。
&emsp;&emsp;简单代码示例：

``` python
def my_func(x):
    return np.sinh(x)

inp = tf.placeholder(tf.float32)
y = tf.py_func(my_func, [inp], tf.float32)
```

缺点如下：

- 这个被包装过的的计算函数的内部部分不会被序列化到`GraphDef`里面去，所以，如果你要序列化存储和恢复模型，就不能使用该函数。
- 这个被包装的计算节点`OP`与调用它的`Python`程序必须运行在同一个物理设备上，也就是说，如果使用分布式`TensorFlow`，必须使用`tf.train.Server`和`with tf.device`来保证二者在同一个服务器内。

### tf.trainable_variables

&emsp;&emsp;在创造变量(`tf.Variable`、`tf.get_variable`等)时，都会有一个`trainable`的选项，表示该变量是否可训练，这个函数会返回图中所有`trainable=True`的变量。`tf.get_variable`和`tf.Variable`的默认选项是`True`，而`tf.constant`只能是`False`：

``` python
import tensorflow as tf

a = tf.get_variable('a', shape=[5, 2])
b = tf.get_variable('b', shape=[2, 5], trainable=False)
c = tf.constant([1, 2, 3], dtype=tf.int32, shape=[8], name='c')
d = tf.Variable(tf.random_uniform(shape=[3, 3]), name='d')
tvar = tf.trainable_variables()
tvar_name = [x.name for x in tvar]
print(tvar)
print(tvar_name)
```

执行结果：

``` python
[<tf.Variable 'a:0' shape=(5, 2) dtype=float32_ref>, <tf.Variable 'd:0' shape=(3, 3) dtype=float32_ref>]
['a:0', 'd:0']
```

### tf.train.ExponentialMovingAverage

&emsp;&emsp;该函数用于更新参数，就是采用滑动平均的方法更新参数：

``` python
tf.train.ExponentialMovingAverage(decay, steps)
```

这个函数初始化需要提供一个衰减速率(`decay`)，用于控制模型的更新速度。这个函数还会维护一个影子变量，也就是更新参数后的参数值，这个影子变量的初始值就是这个变量的初始值，影子变量值的更新方式如下：

``` python
shadow_variable = decay * shadow_variable + (1 - decay) * variable
```

`shadow_variable`是影子变量；`variable`表示待更新的变量，也就是变量被赋予的值；`decay`为衰减速率，一般设为接近于`1`的数(`0.99`或`0.999`)，其越大模型越稳定，因为`decay`越大，参数更新的速度就越慢，趋于稳定。
&emsp;&emsp;`tf.train.ExponentialMovingAverage`这个函数还提供了自己动更新`decay`的计算方式：

``` python
decay = min(decay, (1 + steps) / (10 + steps))
```

`steps`是迭代的次数，可以自己设定。

``` python
import tensorflow as tf
import numpy as np

v1 = tf.Variable(0, dtype=tf.float32)
step = tf.Variable(tf.constant(0))
ema = tf.train.ExponentialMovingAverage(0.99, step)
maintain_average = ema.apply([v1])

with tf.Session() as sess:
    init = tf.global_variables_initializer()
    sess.run(init)
    print(sess.run([v1, ema.average(v1)]))  # 初始的值都为0
    sess.run(tf.assign(v1, 5))  # 把v1变为5
    sess.run(maintain_average)
    # “decay = min(0.99, 1/10) = 0.1”，“v1 = 0.1 * 0 + 0.9 * 5 = 4.5”
    print(sess.run([v1, ema.average(v1)]))
    sess.run(tf.assign(step, 10000))  # steps = 10000
    sess.run(tf.assign(v1, 10))  # v1 = 10
    sess.run(maintain_average)
    # “decay = min(0.99, (1 + 10000)/(10 + 10000)) = 0.99”，“v1 = 0.99 * 4.5 + 0.01 * 10 = 4.555”
    print(sess.run([v1, ema.average(v1)]))
    sess.run(maintain_average)
    # “decay = min(0.99, (1 + 10000)/(10 + 10000)) = 0.99”，“v1 = 0.99 * 4.555 + 0.01 * 10 = 4.6”
    print(sess.run([v1, ema.average(v1)]))
```

### tf.moving_average_variables(scope=None)

&emsp;&emsp;Returns all variables that maintain their moving averages. If an `ExponentialMovingAverage` object is created and `the apply()` method is called on a list of variables, these variables will be added to the `GraphKeys.MOVING_AVERAGE_VARIABLES` collection. This convenience function returns the contents of that collection.
&emsp;&emsp;`scope` (optional) is a string. If supplied, the resulting list is filtered to include only items whose name attribute matches `scope` using `re.match`. Items without a name attribute are never returned if a `scope` is supplied. The choice of `re.match` means that a `scope` without special tokens filters by prefix.
&emsp;&emsp;使用方式如下：

``` python
variable_averages = tf.train.ExponentialMovingAverage(decay, global_step)
variables_to_average = (tf.trainable_variables() + tf.moving_average_variables())
variables_averages_op = variable_averages.apply(variables_to_average)
train_op = tf.group(opt, variables_averages_op)
```

### tf.control_dependencies

&emsp;&emsp;在有些机器学习程序中，我们想要指定某些操作执行的依赖关系，这时可以使用`tf.control_dependencies(control_inputs)`来实现。该函数返回一个控制依赖的上下文管理器，使用`with`关键字可以让在这个上下文环境中的操作都在`control_inputs`执行。

``` python
with g.control_dependencies([a, b, c]):
    # 'd' and 'e' will only run after 'a', 'b', and 'c' have executed
    d = ...
    e = ...
```

可以嵌套`control_dependencies`使用：

``` python
with g.control_dependencies([a, b]):
    # Ops constructed here run after 'a' and 'b'
    with g.control_dependencies([c, d]):
        # Ops constructed here run after 'a', 'b', 'c', and 'd'
```

可以传入`None`来消除依赖：

``` python
with g.control_dependencies([a, b]):
    # Ops constructed here run after 'a' and 'b'
    with g.control_dependencies(None):
        # Ops constructed here run normally, not waiting for either 'a' or 'b'
        with g.control_dependencies([c, d]):
            # Ops constructed here run after 'c' and 'd',
            # also not waiting for either 'a' or 'b'
```

注意，控制依赖只对那些在上下文环境中建立的操作有效，仅仅在`context`中使用一个操作或张量是没用的：

``` python
def my_func(pred, tensor):  # WRONG
    t = tf.matmul(tensor, tensor)
    with tf.control_dependencies([pred]):
        # The matmul op is created outside the context,
        # so no control dependency will be added
        return t

def my_func(pred, tensor):  # RIGHT
    with tf.control_dependencies([pred]):
        # The matmul op is created in the context,
        # so a control dependency will be added
        return tf.matmul(tensor, tensor)
```

### tf.group

&emsp;&emsp;The function is:

``` python
tf.group(*inputs, **kwargs)
```

Create an op that groups multiple operations. When this op finishes, all ops in `inputs` have finished. This op has no output.

- `*inputs`: Zero or more tensors to group.
- `name`: A name for this operation (optional).

Returns an Operation that executes all its `inputs`.

``` python
import tensorflow as tf

b = tf.random_uniform([2, 3], minval=10, maxval=11)
w = tf.random_uniform([2, 3], minval=10, maxval=11)
mul = tf.multiply(w, 2)
add = tf.add(w, 2)
group = tf.group(mul, add)

with tf.Session() as sess:
    print(sess.run(group))
```

### tf.add_to_collection和tf.get_collection

&emsp;&emsp;`tf.add_to_collection`是把变量放入一个集合，把很多变量变成一个列表；`tf.get_collection`是从一个集合中取出全部变量，返回值是一个列表；`tf.add_n`是把一个列表的东西都依次加起来：

``` python
import tensorflow as tf
import numpy as np

v1 = tf.get_variable(name='v1', shape=[1], initializer=tf.constant_initializer(0))
tf.add_to_collection('loss', v1)
v2 = tf.get_variable(name='v2', shape=[1], initializer=tf.constant_initializer(2))
tf.add_to_collection('loss', v2)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print(tf.get_collection('loss'))
    print(sess.run(tf.add_n(tf.get_collection('loss'))))
```

执行结果：

``` python
[<tf.Variable 'v1:0' shape=(1,) dtype=float32_ref>, <tf.Variable 'v2:0' shape=(1,) dtype=float32_ref>]
[2.]
```

### tf.stack

&emsp;&emsp;The function is:

``` python
tf.stack(values, axis=0, name='stack')
```

Stacks a list of `rank-R` tensors into one `rank-(R+1)` tensor.

- `values`: A list of `Tensor` objects with the same shape and type.
- `axis`: An `int`. The `axis` to stack along. Defaults to the first dimension. Negative `values` wrap around, so the valid range is `[-(R+1), R+1)`.
- `name`: A `name` for this operation (optional).

Return a stacked `Tensor` with the same type as `values`.
&emsp;&emsp;Packs the list of tensors in `values` into a tensor with rank one higher than each tensor in `values`, by packing them along the `axis` dimension.
&emsp;&emsp;Given a list of length `N` of tensors of shape `(A, B, C)`: if `axis == 0` then the output tensor will have the shape `(N, A, B, C)`; if `axis == 1` then the output tensor will have the shape `(A, N, B, C)`. For example:

``` python
x = tf.constant([1, 4])
y = tf.constant([2, 5])
z = tf.constant([3, 6])
tf.stack([x, y, z])  # [[1, 4], [2, 5], [3, 6]] (Pack along first dim.)
tf.stack([x, y, z], axis=1)  # [[1, 2, 3], [4, 5, 6]]
```

This is the opposite of `unstack`. The `numpy` equivalent is:

``` python
tf.stack([x, y, z]) = np.stack([x, y, z])
```

### tf.unstack

&emsp;&emsp;The function is:

``` python
tf.unstack(value, num=None, axis=0, name='unstack')
```

Unpacks the given dimension of a `rank-R` tensor into `rank-(R-1)` tensors.
&emsp;&emsp;Unpacks `num` tensors from `value` by chipping it along the `axis` dimension. If `num` is not specified (the default), it is inferred from `value's` shape. If `value.shape[axis]` is not known, `ValueError` is raised.
&emsp;&emsp;For example, given a tensor of shape `(A, B, C, D)`:

- If `axis == 0` then the `i'th` tensor in output is the slice `value[i, :, :, :]` and each tensor in output will have shape `(B, C, D)`. Note that the dimension unpacked along is gone, unlike split.
- If `axis == 1` then the `i'th` tensor in output is the slice `value[:, i, :, :]` and each tensor in output will have shape `(A, C, D)`.
&emsp;&emsp;Code example:

``` python
import tensorflow as tf

c = tf.constant([[1, 2, 3], [4, 5, 6]])
d = tf.unstack(c, axis=0)
e = tf.unstack(c, axis=1)

with tf.Session() as sess:
    print(sess.run(d))  # 输出“[array([1, 2, 3]), array([4, 5, 6])]”
    print(sess.run(e))  # 输出“[array([1, 4]), array([2, 5]), array([3, 6])]”
```

This is the opposite of `stack`.

- `value`: A rank `R > 0` `Tensor` to be unstacked.
- `num`: An `int`. The length of the dimension `axis`. Automatically inferred if `None` (the default).
- `axis`: An `int`. The `axis` to `unstack` along. Defaults to the first dimension. Negative values wrap around, so the valid range is `[-R, R)`.
- `name`: A `name` for the operation (optional).

Returns the list of `Tensor` objects unstacked from `value`.

### tf.transpose

&emsp;&emsp;The function is:

``` python
tf.transpose(a, perm=None, name='transpose', conjugate=False)
```

Transposes `a`. Permutes the dimensions according to `perm`. The returned tensor's dimension `i` will correspond to the input dimension `perm[i]`. If `perm` is not given, it is set to `(n-1...0)`, where `n` is the rank of the input tensor. Hence by default, this operation performs a regular matrix transpose on `2-D` input `Tensors`. If `conjugate` is `True` and `a.dtype` is either `complex64` or `complex128` then the values of `a` are conjugated and transposed.

- `a`: A `Tensor`.
- `perm`: A permutation of the dimensions of `a`.
- `name`: A `name` for the operation (optional).
- `conjugate`: Optional `bool`. Setting it to `True` is mathematically equivalent to `tf.conj(tf.transpose(input))`.

&emsp;&emsp;For example:

``` python
x = tf.constant([[1, 2, 3], [4, 5, 6]])
tf.transpose(x)
# [[1, 4]
#  [2, 5]
#  [3, 6]]
tf.transpose(x, perm=[1, 0])  # Equivalently
# [[1, 4]
#  [2, 5]
#  [3, 6]]
# If x is complex, setting conjugate=True gives the conjugate transpose
x = tf.constant([[1 + 1j, 2 + 2j, 3 + 3j], [4 + 4j, 5 + 5j, 6 + 6j]])
tf.transpose(x, conjugate=True)
# [[1 - 1j, 4 - 4j],
#  [2 - 2j, 5 - 5j],
#  [3 - 3j, 6 - 6j]]
# "perm" is more useful for n-dimensional tensors, for n > 2
x = tf.constant([[[1, 2, 3], [4, 5, 6]],
                 [[7, 8, 9], [10, 11, 12]]])
# Take the transpose of the matrices in dimension-0
# (this common operation has a shorthand "matrix_transpose")
tf.transpose(x, perm=[0, 2, 1])
# [[[1,  4],
#   [2,  5],
#   [3,  6]],
#  [[7, 10],
#   [8, 11],
#   [9, 12]]]
```

&emsp;&emsp;In `numpy`, transposes are `memory-efficient` constant time operations as they simply return a new view of the same data with adjusted strides. `TensorFlow` does not support strides, so transpose returns a new tensor with the items permuted.

### tf.set_random_seed

&emsp;&emsp;The function is:

``` python
tf.set_random_seed(seed)
```

Sets the `graph-level` random `seed`. Operations that rely on a random `seed` actually derive it from two seeds: the `graph-level` and `operation-level` seeds. This sets the `graph-level` seed.
&emsp;&emsp;Its interactions with `operation-level` seeds is as follows:

- If neither the `graph-level` nor the operation `seed` is set: A random `seed` is used for this op.
- If the `graph-level` `seed` is set, but the operation `seed` is not: The system deterministically picks an operation `seed` in conjunction with the `graph-level` `seed` so that it gets a unique random sequence.
- If the `graph-level` `seed` is not set, but the operation `seed` is set: A default `graph-level` `seed` and the specified operation `seed` are used to determine the random sequence.
- If both the `graph-level` and the operation `seed` are set: Both seeds are used in conjunction to determine the random sequence.

&emsp;&emsp;To generate different sequences across sessions, set neither `graph-level` nor `op-level` seeds:

``` python
import tensorflow as tf

a = tf.random_uniform([1])
b = tf.random_normal([1])
print("Session 1")

with tf.Session() as sess1:
    print(sess1.run(a))  # generates 'A1'
    print(sess1.run(a))  # generates 'A2'
    print(sess1.run(b))  # generates 'B1'
    print(sess1.run(b))  # generates 'B2'

print("Session 2")

with tf.Session() as sess2:
    print(sess2.run(a))  # generates 'A3'
    print(sess2.run(a))  # generates 'A4'
    print(sess2.run(b))  # generates 'B3'
    print(sess2.run(b))  # generates 'B4'
```

To generate the same repeatable sequence for an op across sessions, set the `seed` for the op:

``` python
import tensorflow as tf

a = tf.random_uniform([1], seed=1)
b = tf.random_normal([1])
# Repeatedly running this block with the same graph will generate the same
# sequence of values for 'a', but different sequences of values for 'b'.
print("Session 1")

with tf.Session() as sess1:
    print(sess1.run(a))  # generates 'A1'
    print(sess1.run(a))  # generates 'A2'
    print(sess1.run(b))  # generates 'B1'
    print(sess1.run(b))  # generates 'B2'

print("Session 2")

with tf.Session() as sess2:
    print(sess2.run(a))  # generates 'A1'
    print(sess2.run(a))  # generates 'A2'
    print(sess2.run(b))  # generates 'B3'
    print(sess2.run(b))  # generates 'B4'
```

To make the random sequences generated by all ops be repeatable across sessions, set a `graph-level` seed:

``` python
import tensorflow as tf

tf.set_random_seed(1234)
a = tf.random_uniform([1])
b = tf.random_normal([1])
# Repeatedly running this block with the same graph
# will generate the same sequences of 'a' and 'b'.
print("Session 1")

with tf.Session() as sess1:
    print(sess1.run(a))  # generates 'A1'
    print(sess1.run(a))  # generates 'A2'
    print(sess1.run(b))  # generates 'B1'
    print(sess1.run(b))  # generates 'B2'

print("Session 2")

with tf.Session() as sess2:
    print(sess2.run(a))  # generates 'A1'
    print(sess2.run(a))  # generates 'A2'
    print(sess2.run(b))  # generates 'B1'
    print(sess2.run(b))  # generates 'B2'
```

### tf.identity

&emsp;&emsp;下面的程序想要执行`5`次循环，每次循环给`x`加`1`并赋值给`y`，然后打印出来：

``` python
import tensorflow as tf

x = tf.Variable(0.0)
x_plus_1 = tf.assign_add(x, 1)  # 返回一个op，表示给变量x加1的操作

with tf.control_dependencies([x_plus_1]):
    y = x

init = tf.global_variables_initializer()

with tf.Session() as session:
    init.run()

    for i in range(5):
        print(y.eval())
```

打印出的结果都是`0.0`，也就是说没有达到预期的效果。这是因为`y`只是复制了`x`变量内容，并未和`tensorflow`图上的节点相联系，不能执行节点上的操作。进行如下修改就能实现需要的功能：

``` python
import tensorflow as tf

x = tf.Variable(0.0)
x_plus_1 = tf.assign_add(x, 1)

with tf.control_dependencies([x_plus_1]):
    y = tf.identity(x)  # 修改部分

init = tf.global_variables_initializer()

with tf.Session() as session:
    init.run()

    for i in range(5):
        print(y.eval())
```

也就是说，`tf.identity`返回了一个和`x`相同的的新`tensor`。

### TensorFlow之命令行参数

&emsp;&emsp;`TensorFlow`定义了`tf.app.flags`(也可以用它的别名`tf.flags`)，用于支持接受命令行传递参数，相当于接收`argv`：

``` python
import tensorflow as tf

# 第一个是参数名称，第二个参数是默认值，第三个是参数描述
tf.app.flags.DEFINE_string('str_name', 'def_v_1', "descript1")
tf.app.flags.DEFINE_integer('int_name', 10, "descript2")
tf.app.flags.DEFINE_boolean('bool_name', False, "descript3")
FLAGS = tf.app.flags.FLAGS

def main(_):
    print(FLAGS.str_name)
    print(FLAGS.int_name)
    print(FLAGS.bool_name)

if __name__ == '__main__':
    tf.app.run()  # run main function
```

执行结果：

``` python
$ python test.py --str_name "hello" --int_name 12 --bool_name True
hello
12
True
```

### Session.run和Tensor.eval

&emsp;&emsp;`TensorFlow`运行代码时，在会话中需要运行节点，可能会碰到两种方式，即`Session.run`和`Tensor.eval`，两者之间的差异如下。
&emsp;&emsp;如果你有一个`Tensor t`，在使用`t.eval`时，等价于`tf.get_default_session().run(t)`，实例如下：

``` python
t = tf.constant(42.0)
sess = tf.Session()

with sess.as_default():  # or `with sess:` to close on exit
    assert sess is tf.get_default_session()
    assert t.eval() == sess.run(t)
```

这其中最主要的区别就在于，你可以使用`sess.run`在同一步获取多个`tensor`中的值：

``` python
t = tf.constant(42.0)
u = tf.constant(37.0)
tu = tf.mul(t, u)
ut = tf.mul(u, t)

with sess.as_default():
    tu.eval()  # runs one step
    ut.eval()  # runs one step
    sess.run([tu, ut])  # evaluates both tensors in a single step
```

注意到，每次使用`eval`和`run`时，都会执行整个计算图，为了获取计算的结果，将它分配给`tf.Variable`，然后获取。