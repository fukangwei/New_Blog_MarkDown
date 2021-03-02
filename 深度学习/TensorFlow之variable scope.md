---
title: TensorFlow之variable scope
categories: 深度学习
date: 2019-02-13 11:10:48
---
&emsp;&emsp;`Tensorflow`为了更好的管理变量，提供了`variable scope`机制，其官方解释如下：<!--more-->
&emsp;&emsp;Variable scope object to carry defaults to provide to `get_variable`.
&emsp;&emsp;Many of the arguments we need for `get_variable` in a variable store are most easily handled with a context. This object is used for the defaults. Attributes:

- `name`: `name` of the current scope, used as prefix in `get_variable`.
- `initializer`: 传给`get_variable`的默认`initializer`。如果`get_variable`的时候指定了`initializer`，那么将覆盖这个默认的`initializer`。
- `regularizer`: 传给`get_variable`的默认`regulizer`。
- `reuse`: `Boolean` or `None`, setting the reuse in `get_variable`.
- `caching_device`: `string`, `callable`, or `None`: the caching device passed to `get_variable`.
- `partitioner`: `callable` or `None`: the partitioner passed to `get_variable`.
- `custom_getter`: default custom getter passed to `get_variable`.
- `name_scope`: The name passed to `tf.name_scope`.
- `dtype`: default type passed to `get_variable` (defaults to `DT_FLOAT`).

&emsp;&emsp;`regularizer`参数的作用是给在本`variable_scope`下创建的`weights`加上正则项，这样我们就可以不同`variable_scope`下的参数加不同的正则项了。可以看出，用`variable scope`管理`get_varibale`是很方便的。

### 确定get_variable的prefixed name

&emsp;&emsp;代码示例如下：

``` python
import tensorflow as tf

with tf.variable_scope("tet1"):
    var3 = tf.get_variable("var3", shape=[2], dtype=tf.float32)
    print(var3.name)

with tf.variable_scope("tet2"):
    var4 = tf.get_variable("var4", shape=[2], dtype=tf.float32)
    print(var4.name)
```

执行结果：

``` python
tet1/var3:0
tet2/var4:0
```

`variable scope`是可以嵌套的：

``` python
import tensorflow as tf

with tf.variable_scope("tet1"):
    var3 = tf.get_variable("var3", shape=[2], dtype=tf.float32)
    print(var3.name)

    with tf.variable_scope("tet2"):
        var4 = tf.get_variable("var4", shape=[2], dtype=tf.float32)
        print(var4.name)
```

执行结果：

``` python
tet1/var3:0
tet1/tet2/var4:0
```

&emsp;&emsp;`get_varibale.name`以创建变量的`scope`作为名字的`prefix`：

``` python
import tensorflow as tf

def te2():
    with tf.variable_scope("te2"):
        var2 = tf.get_variable("var2", shape=[2], dtype=tf.float32)
        print(var2.name)

        def te1():
            with tf.variable_scope("te1"):
                var1 = tf.get_variable("var1", shape=[2], dtype=tf.float32)

            return var1

        return te1()  # 在scope的te2内调用的

res = te2()
print(res.name)
```

执行结果：

``` python
te2/var2:0
te2/te1/var1:0
```

对比下面的代码：

``` python
import tensorflow as tf

def te2():
    with tf.variable_scope("te2"):
        var2 = tf.get_variable("var2", shape=[2], dtype=tf.float32)
        print(var2.name)

        def te1():
            with tf.variable_scope("te1"):
                var1 = tf.get_variable("var1", shape=[2], dtype=tf.float32)

            return var1

    return te1()  # 在scope的te2外面调用的

res = te2()
print(res.name)
```

执行结果：

``` python
te2/var2:0
te1/var1:0
```

&emsp;&emsp;需要注意一点的是`tf.variable_scope("name")`与`tf.variable_scope(scope)`的区别，代码`1`如下：

``` python
import tensorflow as tf

with tf.variable_scope("scope"):
    var1 = tf.get_variable("w", shape=[1])
    print(var1.name)  # 执行结果“scope/w”

    with tf.variable_scope("scope"):
        var2 = tf.get_variable("w", shape=[1])
        print(var2.name)  # 执行结果“scope/scope/w”
```

代码`2`如下：

``` python
import tensorflow as tf

with tf.variable_scope("scope"):
    var1 = tf.get_variable("w", shape=[1])
    print(var1.name)  # 执行结果“scope/w”
    scope = tf.get_variable_scope()

    with tf.variable_scope(scope):  # 这种方式设置的scope，是用的外部的scope
        var2 = tf.get_variable("w", shape=[1])  # 这个变量的name也是“scope/w”，因此会报出错误
        print(var2.name)
```

### 共享变量

&emsp;&emsp;共享变量的前提是变量的名字是一样的，变量的名字是由变量名和其`scope`前缀一起构成，`tf.get_variable_scope().reuse_variables()`允许共享当前`scope`下的所有变量。

``` python
import tensorflow as tf

with tf.variable_scope("level1"):
    var1 = tf.get_variable("w", shape=[1])
    print(var1.name)
    scope = tf.get_variable_scope()

    with tf.variable_scope("level2"):
        var2 = tf.get_variable("w", shape=[1])
        print(var2.name)

with tf.variable_scope("level1", reuse=True):
    var1 = tf.get_variable("w", shape=[1])
    print(var1.name)
    scope = tf.get_variable_scope()

    with tf.variable_scope(scope):
        var2 = tf.get_variable("w", shape=[1])
        print(var2.name)
```

执行结果：

``` python
level1/w:0
level1/level2/w:0
level1/w:0
level1/w:0
```


---

&emsp;&emsp;在`Tensorflow`中，有两个`scope`，一个是`name_scope`，另一个是`variable_scope`，这两个`scope`到底有什么区别呢？
&emsp;&emsp;先看第一个程序：

``` python
import tensorflow as tf

with tf.name_scope("hello") as name_scope:
    arr1 = tf.get_variable("arr1", shape=[2, 10], dtype=tf.float32)
    print(name_scope)
    print(arr1.name)
    print("scope_name:%s " % tf.get_variable_scope().original_name_scope)
```

执行结果：

``` python
hello/
arr1:0
scope_name:
```

可以看出，`tf.name_scope`返回的是一个`string`，在`name_scope`中定义的`variable`的`name`并没有`hello/`前缀；`tf.get_variable_scope`的`original_name_scope`是空。
&emsp;&emsp;第二个程序如下：

``` python
import tensorflow as tf

with tf.variable_scope("hello") as variable_scope:
    arr1 = tf.get_variable("arr1", shape=[2, 10], dtype=tf.float32)
    print(variable_scope)
    print(variable_scope.name)  # 打印出变量空间名字
    print(arr1.name)
    # tf.get_variable_scope获取的就是variable_scope
    print(tf.get_variable_scope().original_name_scope)

    with tf.variable_scope("xixi") as v_scope2:
        # tf.get_variable_scope获取的就是v_scope2
        print(tf.get_variable_scope().original_name_scope)
```

执行结果：

``` python
<tensorflow.python.ops.variable_scope.VariableScope object at 0x00000000086649B0>
hello
hello/arr1:0
hello/
hello/xixi/
```

可以看出，`tf.variable_scope`返回的是一个`op`对象，`variable_scope`中定义的`variable`的`name`加上了`hello/`前缀；`tf.get_variable_scope`的`original_name_scope`是嵌套后的`scope name`。
&emsp;&emsp;第三个程序如下：

``` python
import tensorflow as tf

with tf.name_scope("name1"):
    with tf.variable_scope("var1"):
        w = tf.get_variable("w", shape=[2])
        res = tf.add(w, [3])

print(w.name)
print(res.name)
```

执行结果：

``` python
var1/w:0
name1/var1/Add:0
```

可以看出，`variable scope`和`name scope`都会给`op`的`name`加上前缀。
&emsp;&emsp;对比三个个程序可以看出：

- `name_scope`对`get_variable`创建的变量的名字不会有任何影响，而创建的`op`会被加上前缀。
- `tf.get_variable_scope`返回的只是`variable_scope`，不管`name_scope`，所以以后我们在使用`tf.get_variable_scope().reuse_variables()`时可以无视`name_scope`。

&emsp;&emsp;其它情况如下：

``` python
import tensorflow as tf

with tf.name_scope("scope1") as scope1:
    with tf.name_scope("scope2") as scope2:
        print(scope2)  # 结果为“scope1/scope2/”
```

另外一个：

``` python
import tensorflow as tf

with tf.variable_scope("scope1") as scope1:
    with tf.variable_scope("scope2") as scope2:
        print(scope2.name)  # 结果为“scope1/scope2”
```

### name_scope可以用来干什么？

&emsp;&emsp;典型的`TensorFlow`可以有数以千计的节点，如此多而难以一下全部看到，甚至无法使用标准图表工具来展示。为简单起见，我们为`op/tensor`名划定范围，并且可视化把该信息用于在图表中的节点上定义一个层级。默认情况下，只有顶层节点会显示。下面这个例子使用`tf.name_scope`在`hidden`命名域下定义了三个操作：

``` python
import tensorflow as tf

with tf.name_scope('hidden') as scope:
    a = tf.constant(5, name='alpha')
    W = tf.Variable(tf.random_uniform([1, 2], -1.0, 1.0), name='weights')
    b = tf.Variable(tf.zeros([1]), name='biases')
    print(a.name)
    print(W.name)
    print(b.name)
```

执行结果：

``` python
hidden/alpha:0
hidden/weights:0
hidden/biases:0
```

`name_scope`是给`op_name`加前缀，`variable_scope`是给`get_variable`创建的变量的名字加前缀。
&emsp;&emsp;`tf.variable_scope`有时也会处理命名冲突：

``` python
import tensorflow as tf

def test(name=None):
    with tf.variable_scope(name, default_name="scope") as scope:
        w = tf.get_variable("w", shape=[2, 10])

test()
test()
ws = tf.trainable_variables()

for w in ws:
    print(w.name)
```

执行结果：

``` python
scope/w:0
scope_1/w:0
```

可以看出，如果只是使用`default_name`这个属性来创建`variable_scope`的时候，会处理命名冲突。
&emsp;&emsp;`tf.name_scope(None)`有清除`name scope`的作用：

``` python
import tensorflow as tf

with tf.name_scope("hehe"):
    w1 = tf.Variable(1.0)

    with tf.name_scope(None):
        w2 = tf.Variable(2.0)

print(w1.name)
print(w2.name)
```

执行结果：

``` python
hehe/Variable:0
Variable:0
```

&emsp;&emsp;简单来看：

- 使用`tf.Variable`的时候，`tf.name_scope`和`tf.variable_scope`都会给`Variable`和`op`的`name`属性加上前缀。
- 使用`tf.get_variable`的时候，`tf.name_scope`就不会给`tf.get_variable`创建出来的`Variable`加前缀。