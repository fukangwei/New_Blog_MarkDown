---
title: pytorch函数总结
categories: 深度学习
abbrlink: bbe38406
date: 2019-01-13 20:05:19
---
### torch.ones

&emsp;&emsp;函数原型如下：<!--more-->

``` python
torch.ones(*sizes, out=None) -> Tensor
```

- `sizes(int...)`：整数序列，定义了输出形状。
- `out(Tensor, optional)`：结果张量。

返回一个全为`1`的张量，形状由可变参数`sizes`定义。

``` python
>>> torch.ones(2, 3)
 1  1  1
 1  1  1
[torch.FloatTensor of size 2x3]
>>> torch.ones(5)
 1
 1
 1
 1
 1
[torch.FloatTensor of size 5]
```

### torch.zeros

&emsp;&emsp;函数原型如下：

``` python
torch.zeros(*sizes, out=None) -> Tensor
```

- `sizes(int...)`：整数序列，定义了输出形状。
- `out(Tensor, optional)`：结果张量。

返回一个全为标量`0`的张量，形状由可变参数`sizes`定义。

``` python
>>> torch.zeros(2, 3)
 0  0  0
 0  0  0
[torch.FloatTensor of size 2x3]
>>> torch.zeros(5)
 0
 0
 0
 0
 0
[torch.FloatTensor of size 5]
```

### torch.arange

&emsp;&emsp;函数原型如下：

``` python
torch.arange(start, end, step=1, out=None) -> Tensor
```

- `start(float)`：序列的起始点。
- `end(float)`：序列的终止点。
- `step(float)`：相邻点的间隔大小。
- `out(Tensor, optional)`：结果张量。

返回一个`1`维张量，长度为`floor((end - start)/step)`，包含从`start`到`end`，以`step`为步长的一组序列值(默认步长为`1`)。

``` python
>>> torch.arange(1, 4)
 1
 2
 3
[torch.FloatTensor of size 3]
>>> torch.arange(1, 2.5, 0.5)
 1.0000
 1.5000
 2.0000
[torch.FloatTensor of size 3]
```

### torch.normal

&emsp;&emsp;函数原型如下：

``` python
torch.normal(means, std, out=None)
```

- `means(Tensor)`：均值。
- `std(Tensor)`：标准差。
- `out(Tensor)`：可选的输出张量。

返回一个张量，包含从给定参数`means`、`std`的离散正态分布中抽取随机数。均值`means`是一个张量，包含每个输出元素相关的正态分布的均值；`std`是一个张量，包含每个输出元素相关的正态分布的标准差。均值和标准差的形状不须匹配，但每个张量的元素个数须相同。

``` python
torch.normal(means=torch.arange(1, 11), std=torch.arange(1, 0, -0.1))
 1.5104
 1.6955
 2.4895
 4.9185
 4.9895
 6.9155
 7.3683
 8.1836
 8.7164
 9.8916
[torch.FloatTensor of size 10]
```

第二个函数原型如下：

``` python
torch.normal(mean=0.0, std, out=None)
```

- `means(Tensor, optional)`：所有分布均值。
- `std(Tensor)`：每个元素的标准差。
- `out(Tensor)`：可选的输出张量。

``` python
>>> torch.normal(mean=0.5, std=torch.arange(1, 6))
  0.5723
  0.0871
 -0.3783
 -2.5689
 10.7893
[torch.FloatTensor of size 5]
```

第三个函数原型如下：

``` python
torch.normal(means, std=1.0, out=None)
```

- `means(Tensor)`：每个元素的均值。
- `std(float, optional)`：所有分布的标准差。
- `out(Tensor)`：可选的输出张量。

``` python
>>> torch.normal(means=torch.arange(1, 6))
 1.1681
 2.8884
 3.7718
 2.5616
 4.2500
[torch.FloatTensor of size 5]
```

### torch.cat

&emsp;&emsp;函数原型如下：

``` python
torch.cat(inputs, dimension=0) -> Tensor
```

- `inputs(sequence of Tensors)`：可以是任意相同`Tensor`类型的`python`序列。
- `dimension(int, optional)`：沿着此维连接张量序列。

在给定维度上对输入的张量序列`seq`进行连接操作。

``` python
>>> x = torch.randn(2, 3)
>>> x
 0.5983 -0.0341  2.4918
 1.5981 -0.5265 -0.8735
[torch.FloatTensor of size 2x3]
>>> torch.cat((x, x, x), 0)
 0.5983 -0.0341  2.4918
 1.5981 -0.5265 -0.8735
 0.5983 -0.0341  2.4918
 1.5981 -0.5265 -0.8735
 0.5983 -0.0341  2.4918
 1.5981 -0.5265 -0.8735
[torch.FloatTensor of size 6x3]
>>> torch.cat((x, x, x), 1)
 0.5983 -0.0341  2.4918  0.5983 -0.0341  2.4918  0.5983 -0.0341  2.4918
 1.5981 -0.5265 -0.8735  1.5981 -0.5265 -0.8735  1.5981 -0.5265 -0.8735
[torch.FloatTensor of size 2x9]
```

### size

&emsp;&emsp;函数原型如下：

``` python
size() -> torch.Size
```

返回`tensor`的大小。

``` python
>>> torch.Tensor(3, 4, 5).size()
torch.Size([3, 4, 5])
```

### torch.randn

&emsp;&emsp;函数原型如下：

``` python
torch.randn(*sizes, out=None) -> Tensor
```

- `sizes(int...)`：整数序列，定义了输出形状。
- `out(Tensor, optinal)`：结果张量。

返回一个张量，包含了从标准正态分布(均值为`0`，方差为`1`，即`高斯白噪声`)中抽取一组随机数，形状由可变参数`sizes`定义。

``` python
>>> torch.randn(4)
-0.1145
 0.0094
-1.1717
 0.9846
[torch.FloatTensor of size 4]
>>> torch.randn(2, 3)
 1.4339  0.3351 -1.0999
 1.5458 -0.9643 -0.3558
[torch.FloatTensor of size 2x3]
```

### torch.max

&emsp;&emsp;函数原型如下：

``` python
torch.max(input)
```

返回输入张量所有元素的最大值，参数`input`(`Tensor`)是输入张量。

``` python
>>> a = torch.randn(1, 3)
>>> a
0.4729 -0.2266 -0.2085
[torch.FloatTensor of size 1x3]
>>> torch.max(a)
0.4729
```

第二个函数原型如下：

``` python
torch.max(input, dim, max=None, max_indices=None) -> (Tensor, LongTensor)
```

- `input(Tensor)`：输入张量。
- `dim(int)`：指定的维度。
- `max(Tensor, optional)`：结果张量，包含给定维度上的最大值。
- `max_indices(LongTensor, optional)`：结果张量，包含给定维度上每个最大值的位置索引。

返回输入张量给定维度上每行的最大值，并同时返回每个最大值的位置索引。输出形状中，将`dim`维设定为`1`，其它与输入形状保持一致。

``` python
>> a = torch.randn(4, 4)
>> a
0.0692  0.3142  1.2513 -0.5428
0.9288  0.8552 -0.2073  0.6409
1.0695 -0.0101 -2.4507 -1.2230
0.7426 -0.7666  0.4862 -0.6628
[torch.FloatTensor of size 4x4]
>>> torch.max(a, 1)
(1.2513 0.9288 1.0695 0.7426 [torch.FloatTensor of size 4x1],
 2      0      0      0      [torch.LongTensor of size 4x1])
```

第三个函数原型如下：

``` python
torch.max(input, other, out=None) -> Tensor
```

- `input(Tensor)`：输入张量。
- `other(Tensor)`：输出张量。
- `out(Tensor, optional)`：结果张量。

返回输入张量给定维度上每行的最大值，并同时返回每个最大值的位置索引，即`out_i = max(input_i, other_i)`。输出形状中，将`dim`维设定为`1`，其它与输入形状保持一致。

``` python
>>> a = torch.randn(4)
>>> a
 1.3869
 0.3912
-0.8634
-0.5468
[torch.FloatTensor of size 4]
>>> b = torch.randn(4)
>>> b
 1.0067
-0.8010
 0.6258
 0.3627
[torch.FloatTensor of size 4]
>>> torch.max(a, b)
 1.3869
 0.3912
 0.6258
 0.3627
[torch.FloatTensor of size 4]
```

### torch.squeeze

&emsp;&emsp;函数原型如下：

``` python
torch.squeeze(input, dim=None, out=None)
```

- `input(Tensor)`：输入张量。
- `dim(int, optional)`：如果给定，则`input`只会在给定维度挤压。
- `out(Tensor, optional)`：输出张量。

将输入张量形状中的`1`去除并返回。如果输入shape是`A * 1 * B * 1 * C * 1 * D`，那么输出shape就是`A * B * C * D`。当给定`dim`时，那么挤压操作只在给定维度上。例如，输入形状为`A * 1 * B`，`squeeze(input, 0)`将会保持张量不变，只有用`squeeze(input, 1)`，形状会变成`A * B`。注意，返回张量与输入张量共享内存，所以改变其中一个的内容会改变另一个。

``` python
>>> x = torch.zeros(2,1,2,1,2)
>>> x.size()
(2L, 1L, 2L, 1L, 2L)
>>> y = torch.squeeze(x)
>>> y.size()
(2L, 2L, 2L)
>>> y = torch.squeeze(x, 0)
>>> y.size()
(2L, 1L, 2L, 1L, 2L)
>>> y = torch.squeeze(x, 1)
>>> y.size()
(2L, 2L, 1L, 2L)
```

### torch.sum

&emsp;&emsp;函数原型如下：

``` python
torch.sum(input) -> float
```

返回输入张量`input`所有元素的和。

``` python
>>> a = torch.randn(1, 3)
>>> a
 0.6170  0.3546  0.0253
[torch.FloatTensor of size 1x3]
>>> torch.sum(a)
0.9969287421554327
```

第二个函数原型如下：

``` python
torch.sum(input, dim, out=None) -> Tensor
```

- `input(Tensor)`：输入张量。
- `dim(int)`：缩减的维度。
- `out(Tensor, optional)`：结果张量。

返回输入张量给定维度上的数据和。输出形状与输入相同，除了给定维度上为`1`。

``` python
>>> a = torch.randn(4, 4)
>>> a
-0.4640  0.0609  0.1122  0.4784
-1.3063  1.6443  0.4714 -0.7396
-1.3561 -0.1959  1.0609 -1.9855
 2.6833  0.5746 -0.5709 -0.4430
[torch.FloatTensor of size 4x4]
>>> torch.sum(a, 1)
 0.1874
 0.0698
-2.4767
 2.2440
[torch.FloatTensor of size 4x1]
```

### torch.linspace

&emsp;&emsp;函数原型如下：

``` python
torch.linspace(start, end, steps=100, out=None) -> Tensor
```

- `start(float)`：序列的起始点。
- `end(float)`：序列的最终值。
- `steps(int)`：在`start`和`end`间生成的样本数。
- `out(Tensor, optional)`：结果张量。

返回一个`1`维张量，包含在区间`start`和`end`上均匀间隔的`steps`个点，输出`1`维张量的长度为`steps`。

``` python
>>> torch.linspace(3, 10, steps=5)
  3.0000
  4.7500
  6.5000
  8.2500
 10.0000
[torch.FloatTensor of size 5]
>>> torch.linspace(-10, 10, steps=5)
-10
 -5
  0
  5
 10
[torch.FloatTensor of size 5]
>>> torch.linspace(start=-10, end=10, steps=5)
-10
 -5
  0
  5
 10
[torch.FloatTensor of size 5]
```

### torch.manual_seed

&emsp;&emsp;函数原型如下：

``` python
torch.manual_seed(seed)
```

设定生成随机数的种子，并返回一个`torch._C.Generator`对象，参数`seed(int or long)`是种子。

### torch.unsqueeze

&emsp;&emsp;函数原型如下：

``` python
torch.unsqueeze(input, dim, out=None)
```

- `tensor(Tensor)`：输入张量。
- `dim(int)`：插入维度的索引。
- `out(Tensor, optional)`：结果张量。

返回一个新的张量，对输入的制定位置插入维度`1`。返回张量与输入张量共享内存，所以改变其中一个的内容会改变另一个。如果`dim`为负，则将会被转化`dim + input.dim() + 1`。

``` python
>>> x = torch.Tensor([1, 2, 3, 4])
>>> torch.unsqueeze(x, 0)
 1  2  3  4
[torch.FloatTensor of size 1x4]
>>> torch.unsqueeze(x, 1)
 1
 2
 3
 4
[torch.FloatTensor of size 4x1]
```

### view

&emsp;&emsp;函数原型如下：

``` python
view(*args) -> Tensor
```

返回一个有相同数据但大小不同的`tensor`，返回的`tensor`必须有与原`tensor`相同的数据和相同数目的元素，但可以有不同的大小。一个`tensor`必须是连续的(`contiguous`)才能被查看。

``` python
>>> x = torch.randn(4, 4)
>>> x.size()
torch.Size([4, 4])
>>> y = x.view(16)
>>> y.size()
torch.Size([16])
# the size -1 is inferred from other dimensions
>>> z = x.view(-1, 8)
>>> z.size()
torch.Size([2, 8])
```

### Convolution

&emsp;&emsp;函数原型如下：

``` python
torch.nn.functional.conv1d(
    input, weight, bias=None, stride=1,
    padding=0, dilation=1, groups=1)
```

- `input`：输入张量的形状(`minibatch, in_channels, iW`)。
- `weight`：过滤器的形状(`out_channels, in_channels, kW`)。
- `bias`：可选偏置的形状(`out_channels`)。
- `stride`：卷积核的步长。

对几个输入平面组成的输入信号应用`1D`卷积。

``` python
>>> filters = autograd.Variable(torch.randn(33, 16, 3))
>>> inputs = autograd.Variable(torch.randn(20, 16, 50))
>>> F.conv1d(inputs, filters)
```

第二个函数原型如下：

``` python
torch.nn.functional.conv2d(
    input, weight, bias=None, stride=1,
    padding=0, dilation=1, groups=1)
```

- `input`：输入张量(`minibatch, in_channels, iH, iW`)。
- `weight`：过滤器张量(`out_channels, in_channels/groups, kH, kW`)。
- `bias`：可选偏置张量(`out_channels`)。
- `stride`：卷积核的步长，可以是单个数字或一个元组(`sh, sw`)。
- `padding`：输入边缘零填充，可以是单个数字或元组。
- `groups`：将输入分成组，`in_channels`应该被组数除尽。

对几个输入平面组成的输入信号应用`2D`卷积。

``` python
>>> # With square kernels and equal stride
>>> filters = autograd.Variable(torch.randn(8,4,3,3))
>>> inputs = autograd.Variable(torch.randn(1,4,5,5))
>>> F.conv2d(inputs, filters, padding=1)
```

第三个函数原型如下：

``` python
torch.nn.functional.conv3d(
    input, weight, bias=None, stride=1,
    padding=0, dilation=1, groups=1)
```

- `input`：输入张量的形状(`minibatch, in_channels, iT, iH, iW`)。
- `weight`：过滤器张量的形状(`out_channels, in_channels, kT, kH, kW`)。
- `bias`：可选偏置张量的形状(`out_channels`)。
- `stride`：卷积核的步长，可以是单个数字或一个元组(`sh, sw`)。
- `padding`：输入上隐含零填充，可以是单个数字或元组。

对几个输入平面组成的输入信号应用`3D`卷积。

``` python
>>> filters = autograd.Variable(torch.randn(33, 16, 3, 3, 3))
>>> inputs = autograd.Variable(torch.randn(20, 16, 50, 10, 20))
>>> F.conv3d(inputs, filters)
```

### Normalization

&emsp;&emsp;函数原型如下：

``` python
torch.nn.BatchNorm1d(num_features, eps=1e-05, momentum=0.1, affine=True)
```

- `num_features`：来自期望输入的特征数，该期望输入的大小为(`batch_size, num_features [, width]`)。
- `eps`：为保证数值稳定性(分母不能趋近或取`0`)，给分母加上的值。
- `momentum`：动态均值和动态方差所使用的动量。
- `affine`：一个布尔值，当设为`True`时，给该层添加可学习的仿射变换参数。

对小批量(`mini-batch`)的`2d`或`3d`输入进行批标准化(`Batch Normalization`)。在每一个小批量(`mini-batch`)数据中，计算输入各个维度的均值和标准差。
&emsp;&emsp;在训练时，该层计算每次输入的均值与方差，并进行移动平均，移动平均默认的动量值为`0.1`；在验证时，训练求得的均值和方差将用于标准化验证数据。
&emsp;&emsp;对于`Shape`，输入(`N, C`)或者(`N, C, L`)，输出(`N, C`)或者(`N, C, L`)，即输入输出都相同。

``` python
# With Learnable Parameters
>>> m = nn.BatchNorm1d(100)
# Without Learnable Parameters
>>> m = nn.BatchNorm1d(100, affine=False)
>>> input = autograd.Variable(torch.randn(20, 100))
>>> output = m(input)
```

&emsp;&emsp;第二个函数原型如下：

``` python
torch.nn.BatchNorm2d(num_features, eps=1e-05, momentum=0.1, affine=True)
```

- `num_features`：来自期望输入的特征数，该期望输入的大小为(`batch_size, num_features, height, width`)。
- `eps`：为保证数值稳定性(分母不能趋近或取`0`)，给分母加上的值。
- `momentum`：动态均值和动态方差所使用的动量。
- `affine`：一个布尔值，当设为`True`时，给该层添加可学习的仿射变换参数。

对小批量的`3d`数据组成的`4d`输入进行批标准化。在每一个小批量数据中，计算输入各个维度的均值和标准差。
&emsp;&emsp;在训练时，该层计算每次输入的均值与方差，并进行移动平均，移动平均默认的动量值为`0.1`；在验证时，训练求得的均值和方差将用于标准化验证数据。
&emsp;&emsp;对于`Shape`，输入(`N, C, H, W`)，输出(`N, C, H, W`)，即输入输出相同。

``` python
# With Learnable Parameters
>>> m = nn.BatchNorm2d(100)
# Without Learnable Parameters
>>> m = nn.BatchNorm2d(100, affine=False)
>>> input = autograd.Variable(torch.randn(20, 100, 35, 45))
>>> output = m(input)
```

&emsp;&emsp;第三个函数原型：

``` python
torch.nn.BatchNorm3d(num_features, eps=1e-05, momentum=0.1, affine=True)
```

- `num_features`：来自期望输入的特征数，该期望输入的大小为(`batch_size, num_features, depth, height, width`)。
- `eps`：为保证数值稳定性(分母不能趋近或取`0`)，给分母加上的值。
- `momentum`：动态均值和动态方差所使用的动量。
- `affine`：一个布尔值，当设为`True`时，给该层添加可学习的仿射变换参数。

对小批量的`4d`数据组成的`5d`输入进行批标准化操作。在每一个小批量数据中，计算输入各个维度的均值和标准差。
&emsp;&emsp;在训练时，该层计算每次输入的均值与方差，并进行移动平均，移动平均默认的动量值为`0.1`；在验证时，训练求得的均值和方差将用于标准化验证数据。
&emsp;&emsp;对于`Shape`，输入(`N, C, D, H, W`)，输出(`N, C, D, H, W`)，即输入输出相同。

``` python
# With Learnable Parameters
>>> m = nn.BatchNorm3d(100)
# Without Learnable Parameters
>>> m = nn.BatchNorm3d(100, affine=False)
>>> input = autograd.Variable(torch.randn(20, 100, 35, 45, 10))
>>> output = m(input)
```

### 池化层函数

&emsp;&emsp;函数原型如下：

``` python
torch.nn.MaxPool1d(
    kernel_size, stride=None, padding=0, dilation=1,
    return_indices=False, ceil_mode=False)
```

- `kernel_size(int or tuple)`：`max pooling`的窗口大小。
- `stride(int or tuple, optional)`：`max pooling`的窗口移动的步长，默认值是`kernel_size`。
- `padding(int or tuple, optional)`：输入的每一条边补充`0`的层数。
- `dilation(int or tuple, optional)`：一个控制窗口中元素步幅的参数。
- `return_indices`：如果等于`True`，则会返回输出最大值的序号，对于上采样操作会有帮助。
- `ceil_mode`：如果等于`True`，计算输出信号大小的时候，会使用向上取整，代替默认的向下取整的操作。

对于输入信号的输入通道，提供`1`维最大池化(`max pooling`)操作。如果`padding`不是0，会在输入的每一边添加相应数目`0`；`dilation`用于控制内核点之间的距离。
&emsp;&emsp;对于`shape`，输入(`N, C_in, L_in`)，输出(`N, C_out, L_out`)。

``` python
# pool of size = 3, stride = 2
>>> m = nn.MaxPool1d(3, stride=2)
>>> input = autograd.Variable(torch.randn(20, 16, 50))
>>> output = m(input)
```

&emsp;&emsp;第二个函数原型如下：

``` python
torch.nn.MaxPool2d(
    kernel_size, stride=None, padding=0, dilation=1,
    return_indices=False, ceil_mode=False)
```

对于输入信号的输入通道，提供`2`维最大池化(`max pooling`)操作。如果`padding`不是`0`，会在输入的每一边添加相应数目`0`；`dilation`用于控制内核点之间的距离。
&emsp;&emsp;参数`kernel_size`、`stride`、`padding`和`dilation`数据类型：可以是一个`int`类型的数据，此时卷积`height`和`width`值相同；也可以是一个`tuple`数组(包含两个`int`类型的数据)，第一个`int`数据表示`height`的数值，第二个`int`类型的数据表示`width`的数值。

- `kernel_size(int or tuple)`：`max pooling`的窗口大小。
- `stride(int or tuple, optional)`：`max pooling`的窗口移动的步长，默认值是`kernel_size`。
- `padding(int or tuple, optional)`：输入的每一条边补充`0`的层数。
- `dilation(int or tuple, optional)`：一个控制窗口中元素步幅的参数。
- `return_indices`：如果等于`True`，会返回输出最大值的序号，对于上采样操作会有帮助。
- `ceil_mode`：如果等于`True`，计算输出信号大小的时候，会使用向上取整，代替默认的向下取整的操作。

``` python
# pool of square window of size=3, stride=2
>>> m = nn.MaxPool2d(3, stride=2)
# pool of non-square window
>>> m = nn.MaxPool2d((3, 2), stride=(2, 1))
>>> input = autograd.Variable(torch.randn(20, 16, 50, 32))
>>> output = m(input)
```

&emsp;&emsp;第三个函数原型如下：

``` python
torch.nn.MaxPool3d(
    kernel_size, stride=None, padding=0, dilation=1,
    return_indices=False, ceil_mode=False)
```

- `kernel_size(int or tuple)`：`max pooling`的窗口大小。
- `stride(int or tuple, optional)`：`max pooling`的窗口移动的步长，默认值是`kernel_size`。
- `padding(int or tuple, optional)`：输入的每一条边补充`0`的层数。
- `dilation(int or tuple, optional)`：一个控制窗口中元素步幅的参数。
- `return_indices`：如果等于`True`，会返回输出最大值的序号，对于上采样操作会有帮助。
- `ceil_mode`：如果等于`True`，计算输出信号大小的时候，会使用向上取整，代替默认的向下取整的操作。

对于输入信号的输入通道，提供`3`维最大池化(`max pooling`)操作。如果`padding`不是`0`，会在输入的每一边添加相应数目`0`；`dilation`用于控制内核点之间的距离。
&emsp;&emsp;参数`kernel_size`、`stride`、`padding`、`dilation`数据类型：可以是`int`类型的数据，此时卷积`height`和`width`值相同；也可以是一个`tuple`数组(包含两个`int`类型的数据)，第一个`int`数据表示`height`的数值，第二个`int`类型的数据表示`width`的数值。

``` python
# pool of square window of size=3, stride=2
>>>m = nn.MaxPool3d(3, stride=2)
# pool of non-square window
>>> m = nn.MaxPool3d((3, 2, 2), stride=(2, 1, 2))
>>> input = autograd.Variable(torch.randn(20, 16, 50,44, 31))  
>>> output = m(input)
```

### torchvision.transforms.CenterCrop(size)

&emsp;&emsp;将`PIL.Image`根据给定的`size`进行中心切割。`size`可以是`tuple(target_height, target_width)`，也可以是一个`Integer`，在这种情况下，切出来的图片是正方形。

### ConvTranspose2d

&emsp;&emsp;二维反卷积层的函数原型如下：

``` python
torch.nn.ConvTranspose2d(
    in_channels, out_channels, kernel_size, stride=1, padding=0,
    output_padding=0, groups=1, bias=True, dilation=1)
```

- `in_channels`：输入信号的通道数。
- `out_channels`：卷积后输出结果的通道数。
- `kernel_size`：卷积核的形状。
- `stride`：卷积每次移动的步长。
- `padding`：处理边界时填充`0`的数量，默认为`0`(不填充)。
- `output_padding`：输出时在每一个维度首尾补`0`的数量(卷积时，形状不同的输入数据对相同的核函数可以产生形状相同的结果；反卷积时，同一个输入对相同的核函数可以产生多个形状不同的输出，而输出结果只能有一个，因此必须对输出形状进行约束)。
- `bias`：为`True`时表示添加偏置。
- `dilation`：采样间隔数量，大于`1`时为非致密采样。
- `groups`：控制输入和输出之间的连接，当`group`为`1`时，输出是所有输入的卷积；当`group`为`2`时，相当于有并排的两个卷积层，每个卷积层只在对应的输入通道和输出通道之间计算，并且输出时会将所有输出通道简单的首尾相接作为结果输出。

&emsp;&emsp;`in_channels`和`out_channels`都应当可以被`groups`整除。`kernel_size`、`stride`、`padding`和`output_padding`可以为:

- 单个`int`值：宽和高均被设定为此值。
- 两个`int`组成的`tuple`：第一个`int`为高度，第二个`int`为宽度。

&emsp;&emsp;输入和输出的`shape`如下：

- 输入`Input`：(`N, Cin, Hin, Win`)。
- 输出`Output`：(`N, Cout, Hout, Wout`)，其中：

``` python
Hout = (Hin - 1) * stride[0] - 2 * padding[0] + kernel_size[0] + output_padding[0]
Wout = (Win - 1) * stride[1] - 2 * padding[1] + kernel_size[1] + output_padding[1]
```

### ReLU

&emsp;&emsp;对输入运用修正线性单元函数(`Relu(x) = max(0, x)`)：

``` python
torch.nn.ReLU(inplace=False)
```

参数`inplace`的默认设置为`False`，表示新创建一个对象对其修改；也可以设置为`True`，表示直接对这个对象进行修改。

### cuda.is_available

&emsp;&emsp;Check whether pytorch is using GPU:

``` python
import torch
use_gpu = torch.cuda.is_available()
```