---
title: Tensorflow之input pipeline性能指南
date: 2019-02-15 18:34:45
categories: 深度学习
---
&emsp;&emsp;`GPU`和`TPU`可以从根本上减少执行单个`training step`所需的时间。为了达到高性能，我们需要一个高效的`input pipeline`，它可以在当前`step`完成后为下一个`step`高效分发数据。`tf.data`的`API`可以帮助构建灵活和高效的`input pipeline`。该文档解释了`tf.data`的`API`特性以及最佳实践，来跨多种模型和加速器构建高性能`tensorflow input pipelines`。

### Input Pipeline结构

&emsp;&emsp;一个典型的`tensorflow training input pipeline`可以看成是一个`ETL process`问题：

- `Extract(E)`：从持久化存储中读取数据，可以是本地(比如`HDD`或`SSD`)或远程(比如`GCS`或`HDFS`)。
- `Transform(T)`：利用`CPU cores`来解析和执行在数据上的预处理操作，例如图片解压、数据转换(随机裁减、翻转、颜色扭曲)、重排(`shuffling`)以及`batching`。
- `Load(L)`：在加速设备上(例如`GPU`或`TPU`)加载转换后的数据，执行机器学习模型。

这种模式(`pattern`)可以有效地利用`CPU`，同时保留加速器让它去处理模型训练部分的重任。另外，将`input pipeline`看成是一个`ETL process`，这种结构更有利于性能优化。

&emsp;&emsp;当使用`tf.estimator.Estimator`的`API`时，前两个`phases`(`Extract`和`Transform`)可以在`input_fn`中被捕获，然后传给`tf.estimator.Estimator.train`。在代码中，看起来实现如下：

``` python
def parse_fn(example):
    """ Parse TFExample records and perform simple data augmentation """
    example_fmt = {
        "image": tf.FixedLengthFeature((), tf.string, ""),
        "label": tf.FixedLengthFeature((), tf.int64, -1)
    }
    parsed = tf.parse_single_example(example, example_fmt)
    image = tf.image.decode_image(parsed["image"])
    image = _augment_helper(image)  # augments image using slice, reshape, resize_bilinear
    return image, parsed["label"]
​
def input_fn():
    files = tf.data.Dataset.list_files("/path/to/dataset/train-*.tfrecord")
    dataset = files.interleave(tf.data.TFRecordDataset)
    dataset = dataset.shuffle(buffer_size=FLAGS.shuffle_buffer_size)
    dataset = dataset.map(map_func=parse_fn)
    dataset = dataset.batch(batch_size=FLAGS.batch_size)
    return dataset
```

### 性能优化

&emsp;&emsp;新计算设备(比如`CPU`或`TPU`)可以以一个越来越快的速率来训练神经网络，而`CPU`处理速度被证明是个瓶颈。`tf.data`的`API`提供给用户相应的构建块来设计`input pipeline`，可以有效利用`CPU`，优化在`ETL`过程中的每一步。

#### Pipeling

&emsp;&emsp;为了执行一个`training step`，你必须首先`extract`和`transform`训练数据，接着将它`feed`给一个运行在加速器上的模型。然而，在一个原始的同步实现(`naive synchronous implementation`)中，`CPU`会准备数据，加速器会保持空闲状态。相反地，当加速器在训练模型时，`CPU`会保持空闲状态。训练过程的时间会是`CPU`处理时间和加速器训练时间的总和。
&emsp;&emsp;`Pipelining`会将一个`training step`的预处理和模型执行在时序上重叠。当加速器执行了`N`个`training step`时，`CPU`会为第`N + 1`个`step`准备数据，这样做是为了减少总时间。如果没有做`pipeling`，`CPU`和`GPU/TPU`的空闲时间会有很多：

<img src="./Tensorflow之input pipeline性能指南/1.png" height="72" width="403">

&emsp;&emsp;而有了`pipeling`，空闲时间会急剧减少：

<img src="./Tensorflow之input pipeline性能指南/2.png" height="73" width="400">

&emsp;&emsp;`tf.data`的`API`通过`tf.data.Dataset.prefetch`转换，提供了一个软件实现的管道机制(`software pipeling`)，该转换可以被用于将数据生产时间和数据消费时间相解耦。特别的，该转换会使用一个后台线程，以及一个内部缓存(`internal buffer`)，来`prefetch`来自`input dataset`的元素(在它们被请求之前)。这样，为了达到上述的`pipeling`效果，你可以添加`prefetch(1)`到你的`dataset pipeline`中(或者`prefetch(n)`，如果单个`training step`消费`n`个元素的话)。
&emsp;&emsp;为了在我们的示例中达到该变化，可以做如下更改，将：

``` python
dataset = dataset.batch(batch_size=FLAGS.batch_size)
return dataset
```

变更成：

``` python
dataset = dataset.batch(batch_size=FLAGS.batch_size)
dataset = dataset.prefetch(buffer_size=FLAGS.prefetch_buffer_size)
return dataset
```

注意，`prefetch`转换在任何时间都会有好处，有机会将一个`producer`的工作量和一个`consumer`的工作量在时序上重合。

#### 并行化数据转换

&emsp;&emsp;当准备一个`batch`时，`input elements`需要被预处理。为了这个目的，`tf.data`的`API`提供了`tf.data.Dataset.map`转换，它会将一个用户定义的函数(例如运行示例中的`parse_fn`)应用到`input dataset`中的每个元素上。由于`input elements`相互独立，预处理可以跨多个`CPU core`并行进行。为了达到该目的，`map`转换提供了`num_parallel_calls`参数来指定并行度。例如，下图展示了在`map`转换中设置`num_parallel_calls=2`的效果：

<img src="./Tensorflow之input pipeline性能指南/3.png" height="196" width="342">

&emsp;&emsp;选择`num_parallel_calls`的最佳值取决于你的硬件、训练数据的特性(比如`size`和`shape`)、`map`函数的开销、以及在`CPU`上同时发生的其它处理过程，一个简单的启发法(`heuristic`)是使用可提供的`CPU cores`的数目。例如，如果机器具有`4`个`cores`，那么设置`num_parallel_calls=4`更有效。在另一方面，将`num_parallel_calls`设置为一个比可提供的`CPU`数更大的值，会导致无效调度，反而会减慢速度。
&emsp;&emsp;可以将代码：

``` python
dataset = dataset.map(map_func=parse_fn)
```

更改为：

``` python
dataset = dataset.map(map_func=parse_fn, num_parallel_calls=FLAGS.num_parallel_calls)
```

更进一步，如果你的`batch_size`是几百或几千，你的`pipeline`将可能从`batch`创建并行化中获得额外收益。为了达到该目的，`tf.data`的`API`提供了`tf.contrib.data.map_and_batch`转换，它可以用效地将`map`转换和`batch`转换相`混合`(`fuse`)。
&emsp;&emsp;可以将下面代码：

``` python
dataset = dataset.map(map_func=parse_fn, num_parallel_calls=FLAGS.num_parallel_calls)
dataset = dataset.batch(batch_size=FLAGS.batch_size)
```

更改为：

``` python
dataset = dataset.apply(
    tf.contrib.data.map_and_batch(
        map_func=parse_fn, batch_size=FLAGS.batch_size
    )
)
```

#### 数据抽取并行化

&emsp;&emsp;在现实中，`input data`可以在远端存储(例如`GCS`或`HDFS`)，可能因为`input data`太大在本地存不下，也可能因为训练是分布式的，不希望在每台机器上复制`input data`。当在本地读取数据时，一个`dataset pipeline`可以很好地工作，而当远端读取时可能会有`I/O`瓶颈。这是由于以下在本地存储和远端存储的不同之处：

- 首字节的时间(`Time-to-first-byte`)：从远端存储上读取一个文件的首字节，可以会比本地存储花费多个数量级。
- 读取吞吐量(`Read throughput`)：远端存储通常提供了更大的聚集带宽，读取单个文件可能只利用了该带宽的一小部分。

另一方面，一旦原始字节被读到内存中，有必要做并行化和数据压缩(比如`protobuf`)，这会增加额外开销。这种开销不用管数据是否在本地或远端存储，但在远端存储的情况下，如果数据没有有效地做`prefetch`，开销会很大。
&emsp;&emsp;为了减轻多种数据抽取开销的影响，`tf.data`提供了`tf.contrib.data.parallel_interleave`转换。使用该转换来并行化其它`datasets`内容(比如`data file readers`)交错执行。`datasets`的重合数目可以通过`cycle_length`参数进行指定。

<img src="./Tensorflow之input pipeline性能指南/4.png" height="199" width="358">

&emsp;&emsp;为了在运行示例中应用该变化，将：

``` python
dataset = files.interleave(tf.data.TFRecordDataset)
```

变更为：

``` python
dataset = files.apply(
    tf.contrib.data.parallel_interleave(
        tf.data.TFRecordDataset, cycle_length=FLAGS.num_parallel_readers
    )
)
```

远端存储系统的吞吐量可以随时间变化，这是由于负载和网络事件。