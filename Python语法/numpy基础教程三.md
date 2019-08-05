---
title: numpy基础教程三
date: 2019-08-04 08:11:52
tags:
---
### Broadcasting机制

&emsp;&emsp;`NumPy`数组运算通常是逐元素(`element-by-element`)计算，因此要求两个数组的形状必须相同：

``` python
>>> a = np.array([1.0, 2.0, 3.0])
>>> b = np.array([2.0, 2.0, 2.0])
>>> a * b
array([ 2., 4., 6.])
```

`NumPy`的`Broadcasting`机制解除了这种限制，在两个数组的形状满足某种条件的情况下，不同形状的数组之间仍可以进行算术运算。最简单的就是数组乘以一个标量：

``` python
>>> a = np.array([1.0, 2.0, 3.0])
>>> b = 2.0
>>> a * b
array([ 2., 4., 6.])
```

结果和第一个`b`是数组的例子相同。可以认为标量`b`被拉伸成了和`a`相同形状的数组，拉伸后数组每个元素的值为先前标量值的复制。实际上复制操作并不会真正进行，只是在计算时使用标量的值罢了，因此节省了内存且效率更高。对于`==`等运算符的使用，`Broadcasting`的效果更显著：

``` python
>>> labels = np.array([1,2,0,0,2])
>>> labels == 0
array([False, False,  True,  True, False], dtype=bool)
>>> (labels == 0).astype(np.float32)
array([ 0.,  0.,  1.,  1.,  0.], dtype=float32)
```

&emsp;&emsp;但并不是任何维数不等的数组运算时都能触发广播机制，只有当两个数组的`trailing dimensions compatible`(尾部维度兼容)时才会触发广播，否则报错`ValueError: frames are not aligned exception`。
&emsp;&emsp;什么是尾部维度？维数较大的数组的比维数较小的数组多出来的维度看做`头部维度`，剩下的就是`尾部维度`。将两数组的`shape`右对齐，右半边可以上下对应的部分就是尾部，如下面`1`和`2`轴。`b`数组将会在`0`轴广播`256`遍：

``` python
            axis:   0     1   2
a     (3d array): 256 x 256 x 3
b     (2d array):       256 x 3
a + b (2d array): 256 x 256 x 3
``

什么是兼容？来自官方文档`Broadcasting`的解释很简练：

``` python
Two dimensions are compatible when they are equal, or one of them is 1
```

即对应的尾部维度的长是相等的或者其中有一方是`1`。上面`a`、`b`对应的尾部维度是相等的，因此`a`、`b`的维度是兼容的，对应第一种情形。下面的`a`、`b`对应第二种情形：

``` python
            axis: 0   1   2   3
A     (4d array): 8 x 1 x 6 x 1
B     (3d array):     7 x 1 x 5
A + B (2d array): 8 x 7 x 6 x 5
```

即`1`是一张万能牌，它与任意长度的维度兼容，`A`在`1`轴广播`7`遍，在`3`轴广播`5`遍，而`B`在`0`轴广播`8`遍。如果我们把`0`轴空着的部分看做`1`，那么一切都将适用第二种情形`one of them is 1`。
&emsp;&emsp;对于任意`shape`的数组，我们可以利用`np.newaxis`使它们兼容，如下面的情况不会触发广播机制：

``` python
a     (2d array): 3 x 4
b     (2d array): 5 x 4
a + b ValueError
```

若转化为下面的形式(当然意义也变了，怎么转化应当根据需求而定)，可以触发广播：

``` python
a[:, np.newaxis, :]     (3d array): 3 x 1 x 4
b                       (2d array):     5 x 4
a[:, np.newaxis, :] + b (3d array): 3 x 5 x 4
```

### numpy.isfinite

&emsp;&emsp;`numpy.isfinite(x, /, out=None, *, where=True, casting='same_kind', order='K', dtype=None, subok=True[, signature, extobj]) = <ufunc 'isfinite'>`: Test `element-wise` for finiteness (not infinity or not Not a Number). The result is returned as a `boolean` array. Parameters:

- `x`: array_like. Input values.
- `out`: ndarray, None, or tuple of ndarray and None, optional. A location into which the result is stored. If provided, it must have a shape that the inputs broadcast to. If not provided or None, a `freshly-allocated` array is returned. A tuple (possible only as a keyword argument) must have length equal to the number of outputs.
- `where`: array_like, optional. Values of True indicate to calculate the ufunc at that position, values of False indicate to leave the value in the output alone.
- `**kwargs` : For other keyword-only arguments, see the `ufunc` docs.

&emsp;&emsp;Returns:

- `y` : ndarray, `bool`. For scalar input, the result is a new `boolean` with value `True` if the input is finite; otherwise the value is `False` (input is either positive infinity, negative infinity or Not a Number). For array input, the result is a `boolean` array with the same dimensions as the input and the values are `True` if the corresponding element of the input is finite; otherwise the values are `False` (element is either positive infinity, negative infinity or Not a Number).

&emsp;&emsp;Notes: Not a Number, positive infinity and negative infinity are considered to be non-finite.
&emsp;&emsp;NumPy uses the `IEEE Standard for Binary Floating-Point for Arithmetic` (`IEEE 754`). This means that `Not a Number` is not equivalent to infinity. Also that positive infinity is not equivalent to negative infinity. But infinity is equivalent to positive infinity. Errors result if the second argument is also supplied when `x` is a scalar input, or if first and second arguments have different shapes.

``` python
>>> np.isfinite(1)
True
>>> np.isfinite(0)
True
>>> np.isfinite(np.nan)
False
>>> np.isfinite(np.inf)
False
>>> np.isfinite(np.NINF)
False
>>> np.isfinite([np.log(-1.), 1., np.log(0)])
array([False, True, False])
>>> x = np.array([-np.inf, 0., np.inf])
>>> y = np.array([2, 2, 2])
>>> np.isfinite(x, y)
array([0, 1, 0])
>>> y
array([0, 1, 0])
```

### numpy.rollaxis

&emsp;&emsp;`numpy.rollaxis(a, axis, start=0)`: Roll the specified axis backwards, until it lies in a given position(实际上是把`axis`轴移到`start`位置).
&emsp;&emsp;This function continues to be supported for backward compatibility, but you should prefer `moveaxis`. The `moveaxis` function was added in `NumPy 1.11`. Parameters:

- `a`: ndarray. Input array.
- `axis`: int. The axis to roll backwards. The positions of the other axes do not change relative to one another.
- `start`: int, optional. The axis is rolled until it lies before this position. The default, 0, results in a "complete" roll.

&emsp;&emsp;Returns:

- `res`: ndarray. For NumPy >= 1.10.0 a view of a is always returned. For earlier NumPy versions a view of a is returned only if the order of the axes is changed, otherwise the input array is returned.

``` python
>>> a = np.ones((3,4,5,6))
>>> np.rollaxis(a, 3, 1).shape
(3, 6, 4, 5)
>>> np.rollaxis(a, 2).shape
(5, 3, 4, 6)
>>> np.rollaxis(a, 1, 4).shape
(3, 5, 6, 4)
```

### numpy.average

&emsp;&emsp;`numpy.average(a, axis=None, weights=None, returned=False)`: Compute the weighted average along the specified axis. Parameters:

- `a`: array_like. Array containing data to be averaged. If `a` is not an array, a conversion is attempted.
- `axis`: None or int or tuple of ints, optional. Axis or axes along which to average a. The default, `axis=None`, will average over all of the elements of the input array. If axis is negative it counts from the last to the first axis. If axis is a tuple of ints, averaging is performed on all of the axes specified in the tuple instead of a single axis or all the axes as before.
- `weights`: array_like, optional. An array of weights associated with the values in a. Each value in a contributes to the average according to its associated weight. The weights array can either be `1-D` (in which case its length must be the size of a along the given axis) or of the same shape as a. If `weights=None`, then all data in a are assumed to have a weight equal to one.

&emsp;&emsp;returned : bool, optional. Default is `False`. If True, the tuple (average, sum_of_weights) is returned, otherwise only the average is returned. If `weights=None`, sum_of_weights is equivalent to the number of elements over which the average is taken.
&emsp;&emsp;Returns: average, [sum_of_weights] : array_type or double. Return the average along the specified axis. When returned is `True`, return a tuple with the average as the first element and the sum of the weights as the second element. The return type is Float if a is of integer type, otherwise it is of the same type as a. sum_of_weights is of the same type as average.
&emsp;&emsp;Raises:

- `ZeroDivisionError`: When all weights along axis are zero. See numpy.ma.average for a version robust to this type of error.
- `TypeError`: When the length of 1D weights is not the same as the shape of a along axis.

``` python
>>> data = range(1,5)
>>> data
[1, 2, 3, 4]
>>> np.average(data)
2.5
>>> np.average(range(1,11), weights=range(10,0,-1))
4.0
>>> data = np.arange(6).reshape((3,2))
>>> data
array([[0, 1],
       [2, 3],
       [4, 5]])
>>> np.average(data, axis=1, weights=[1./4, 3./4])
array([ 0.75,  2.75,  4.75])
>>> np.average(data, weights=[1./4, 3./4])
Traceback (most recent call last):
...
TypeError: Axis must be specified when shapes of a and weights differ.
```

### numpy.full

&emsp;&emsp;numpy.full(shape, fill_value, dtype=None, order='C'): Return a new array of given shape and type, filled with fill_value. Parameters:

- `shape`: int or sequence of ints. Shape of the new array, e.g., (2, 3) or 2.
- `fill_value`: scalar. Fill value.
- `dtype`: data-type, optional. The desired data-type for the array The default, None, means np.array(fill_value).dtype.
- `order`: {'C', 'F'}, optional. Whether to store multidimensional data in `C-` or `Fortran-contiguous` (`row-` or `column-wise`) order in memory.

&emsp;&emsp;Returns:

- `out`: ndarray. Array of fill_value with the given shape, dtype, and order.

``` python
>>> np.full((2, 2), np.inf)
array([[ inf,  inf],
       [ inf,  inf]])
>>> np.full((2, 2), 10)
array([[10, 10],
       [10, 10]])
```

### numpy.fromstring

&emsp;&emsp;`numpy.fromstring(string, dtype=float, count=-1, sep='')`: A new `1-D` array initialized from text data in a string. Parameters:

- `string`: str: A string containing the data.
- `dtype`: `data-type`, optional. The data type of the array; default: float. For binary input data, the data must be in exactly this format.
- `count`: int, optional. Read this number of dtype elements from the data. If this is negative (the default), the count will be determined from the length of the data.
- `sep`: str, optional. The string separating numbers in the data; extra whitespace between elements is also ignored. Deprecated since version 1.14: If this argument is not provided, fromstring falls back on the behaviour of frombuffer after encoding unicode string inputs as either `utf-8` (`python 3`), or the default encoding (`python 2`).

&emsp;&emsp;Returns:

- `arr`: ndarray. The constructed array.

&emsp;&emsp;Raises: `ValueError`. If the string is not the correct size to satisfy the requested dtype and count.

``` python
>>> np.fromstring('1 2', dtype=int, sep=' ')
array([1, 2])
>>> np.fromstring('1, 2', dtype=int, sep=',')
array([1, 2])
```

### np.numpy.nan_to_num

&emsp;&emsp;在使用`numpy`数组的过程中，时常会出现`nan`或者`inf`的元素，可能会造成数值计算时的一些错误。`numpy`提供了`nan_to_num`函数，使`nan`和`inf`能够最简单地转换成相应的数值：

``` python
numpy.nan_to_num(x)
```

使用`0`代替数组`x`中的`nan`元素，使用有限的数字代替`inf`元素：

``` python
>>> import numpy as np
>>> a = np.array([[np.nan,np.inf], [-np.nan,-np.inf]])
>>> a
array([[ nan,  inf],
       [ nan, -inf]])
>>> np.nan_to_num(a)
array([[ 0.00000000e+000,  1.79769313e+308],
       [ 0.00000000e+000, -1.79769313e+308]])
```

### numpy.meshgrid

&emsp;&emsp;Return coordinate matrices from coordinate vectors. Make N-D coordinate arrays for vectorized evaluations of N-D scalar/vector fields over N-D grids, given one-dimensional coordinate arrays `x1, x2, ..., xn`. Changed in version 1.9: 1-D and 0-D cases are allowed. Parameters:

- `x1, x2, ..., xn`: array_like. `1-D` arrays representing the coordinates of a grid.
- `indexing`: {'xy', 'ij'}, optional. Cartesian ('xy', default) or matrix ('ij') indexing of output. See Notes for more details.
- `sparse`: bool, optional. If True a sparse grid is returned in order to conserve memory. Default is False.
- `copy`: bool, optional. If False, a view into the original arrays are returned in order to conserve memory. Default is True. Please note that sparse=False, copy=False will likely return non-contiguous arrays. Furthermore, more than one element of a broadcast array may refer to a single memory location. If you need to write to the arrays, make copies first.

&emsp;&emsp;Returns:

- `X1, X2, ..., XN`: ndarray. For vectors x1, x2,…, 'xn' with lengths Ni=len(xi) , return (N1, N2, N3,...Nn) shaped arrays if indexing='ij' or (N2, N1, N3,...Nn) shaped arrays if indexing='xy' with the elements of xi repeated to fill the matrix along the first dimension for x1, the second for x2 and so on.

&emsp;&emsp;Notes: This function supports both indexing conventions through the indexing keyword argument. Giving the string 'ij' returns a meshgrid with matrix indexing, while 'xy' returns a meshgrid with Cartesian indexing. In the 2-D case with inputs of length M and N, the outputs are of shape (N, M) for 'xy' indexing and (M, N) for 'ij' indexing. In the 3-D case with inputs of length M, N and P, outputs are of shape (N, M, P) for 'xy' indexing and (M, N, P) for 'ij' indexing. The difference is illustrated by the following code snippet:

``` python
xv, yv = np.meshgrid(x, y, sparse=False, indexing='ij')
for i in range(nx):
    for j in range(ny):
        # treat xv[i, j], yv[i, j]
​
xv, yv = np.meshgrid(x, y, sparse=False, indexing='xy')
for i in range(nx):
    for j in range(ny):
        # treat xv[j, i], yv[j, i]
```

In the 1-D and 0-D case, the indexing and sparse keywords have no effect.

``` python
>>> nx, ny = (3, 2)
>>> x = np.linspace(0, 1, nx)
>>> y = np.linspace(0, 1, ny)
>>> xv, yv = np.meshgrid(x, y)
>>> xv
array([[ 0. ,  0.5,  1. ],
       [ 0. ,  0.5,  1. ]])
>>> yv
array([[ 0.,  0.,  0.],
       [ 1.,  1.,  1.]])
>>> xv, yv = np.meshgrid(x, y, sparse=True)  # make sparse output arrays
>>> xv
array([[ 0. ,  0.5,  1. ]])
>>> yv
array([[ 0.],
       [ 1.]])
```

meshgrid is very useful to evaluate functions on a grid.

``` python
>>> x = np.arange(-5, 5, 0.1)
>>> y = np.arange(-5, 5, 0.1)
>>> xx, yy = np.meshgrid(x, y, sparse=True)
>>> z = np.sin(xx**2 + yy**2) / (xx**2 + yy**2)
>>> h = plt.contourf(x,y,z)
```

### numpy.convolve(卷积)

&emsp;&emsp;`numpy.convolve(a, v, mode='full')`: Returns the discrete, linear convolution of two one-dimensional sequences.
&emsp;&emsp;The convolution operator is often seen in signal processing, where it models the effect of a linear `time-invariant` system on a signal. In probability theory, the sum of two independent random variables is distributed according to the convolution of their individual distributions.
&emsp;&emsp;If `v` is longer than `a`, the arrays are swapped before computation. Parameters:

- `a`: (N,) array_like. First one-dimensional input array.
- `v`: (M,) array_like. Second one-dimensional input array.
- `mode`: {'full', 'valid', 'same'}, optional.

1. `full`: By default, mode is 'full'. This returns the convolution at each point of overlap, with an output shape of `(N + M - 1,)`. At the end-points of the convolution, the signals do not overlap completely, and boundary effects may be seen.
2. `same`: Mode 'same' returns output of length max(M, N). Boundary effects are still visible.
3. `valid`: Mode 'valid' returns output of length max(M, N) - min(M, N) + 1. The convolution product is only given for points where the signals overlap completely. Values outside the signal boundary have no effect.

&emsp;&emsp;Returns:

- `out`: ndarray. Discrete, linear convolution of a and v.

&emsp;&emsp;Notes: The discrete convolution operation is defined as:
&emsp;&emsp;It can be shown that a convolution x(t) * y(t) in time/space is equivalent to the multiplication X(f) Y(f) in the Fourier domain, after appropriate padding (padding is necessary to prevent circular convolution). Since multiplication is more efficient (faster) than convolution, the function scipy.signal.fftconvolve exploits the FFT to calculate the convolution of large data-sets.
&emsp;&emsp;Examples: Note how the convolution operator flips the second array before "sliding" the two across one another:

``` python
>>> np.convolve([1, 2, 3], [0, 1, 0.5])
array([ 0. ,  1. ,  2.5,  4. ,  1.5])
```

Only return the middle values of the convolution. Contains boundary effects, where zeros are taken into account:

``` python
>>> np.convolve([1,2,3],[0,1,0.5], 'same')
array([ 1. ,  2.5,  4. ])
```

The two arrays are of the same length, so there is only one position where they completely overlap:

``` python
>>> np.convolve([1,2,3],[0,1,0.5], 'valid')
array([ 2.5])
```

### numpy.random.permutation

&emsp;&emsp;`numpy.random.permutation(x)`: Randomly permute a sequence, or return a permuted range. If x is a multi-dimensional array, it is only shuffled along its first index. Parameters:

- `x`: int or array_like. If x is an integer, randomly permute np.arange(x). If x is an array, make a copy and shuffle the elements randomly.

&emsp;&emsp;Returns:

- `out`: ndarray. Permuted sequence or array range.

``` python
>>> np.random.permutation(10)
array([1, 7, 4, 3, 0, 9, 2, 5, 8, 6])
>>> np.random.permutation([1, 4, 9, 12, 15])
array([15,  1,  9,  4, 12])
>>> arr = np.arange(9).reshape((3, 3))
>>> np.random.permutation(arr)
array([[6, 7, 8],
       [0, 1, 2],
       [3, 4, 5]])
```

### numpy.matrix.A

&emsp;&emsp;`matrix.A`: Return self as an ndarray object. Equivalent to np.asarray(self).

``` python
>>> x = np.matrix(np.arange(12).reshape((3,4))); x
matrix([[ 0,  1,  2,  3],
        [ 4,  5,  6,  7],
        [ 8,  9, 10, 11]])
>>> x.getA()
array([[ 0,  1,  2,  3],
       [ 4,  5,  6,  7],
       [ 8,  9, 10, 11]])
```

numpy.cov(协方差)
    numpy.cov(m, y=None, rowvar=True, bias=False, ddof=None, fweights=None, aweights=None): Estimate a covariance matrix, given data and weights.
    Covariance indicates the level to which two variables vary together. If we examine N-dimensional samples, X = [x1, x2, ... xN]^T, then the covariance matrix element Cij is the covariance of xi and xj. The element Cii is the variance of xi. Parameters: 
m : array_like. A 1-D or 2-D array containing multiple variables and observations. Each row of m represents a variable, and each column a single observation of all those variables. Also see rowvar below.
y : array_like, optional. An additional set of variables and observations. y has the same form as that of m.
rowvar : bool, optional. If rowvar is True (default), then each row represents a variable, with observations in the columns. Otherwise, the relationship is transposed: each column represents a variable, while the rows contain observations.
bias : bool, optional. Default normalization (False) is by (N - 1), where N is the number of observations given (unbiased estimate). If bias is True, then normalization is by N. These values can be overridden by using the keyword ddof in numpy versions >= 1.5.
ddof : int, optional. If not None the default value implied by bias is overridden. Note that ddof=1 will return the unbiased estimate, even if both fweights and aweights are specified, and ddof=0 will return the simple average. See the notes for the details. The default value is None. New in version 1.5.
fweights : array_like, int, optional. 1-D array of integer freguency weights; the number of times each observation vector should be repeated. New in version 1.10.
aweights : array_like, optional. 1-D array of observation vector weights. These relative weights are typically large for observations considered "important" and smaller for observations considered less "important". If ddof=0 the array of weights can be used to assign probabilities to observation vectors. New in version 1.10.
Returns: out : ndarray. The covariance matrix of the variables.
    Notes: Assume that the observations are in the columns of the observation array m and let f = fweights and a = aweights for brevity. The steps to compute the weighted covariance are as follows:
>>> w = f * a
>>> v1 = np.sum(w)
>>> v2 = np.sum(w * a)
>>> m -= np.sum(m * w, axis=1, keepdims=True) / v1
>>> cov = np.dot(m * w, m.T) * v1 / (v1**2 - ddof * v2)
Note that when a == 1, the normalization factor v1 / (v1**2 - ddof * v2) goes over to 1 / (np.sum(f) - ddof) as it should.
    Examples: Consider two variables, x0 and x1, which correlate perfectly, but in opposite directions:
>>> x = np.array([[0, 2], [1, 1], [2, 0]]).T
>>> x
array([[0, 1, 2],
       [2, 1, 0]])
Note how x0 increases while x1 decreases. The covariance matrix shows this clearly:
>>> np.cov(x)
array([[ 1., -1.],
       [-1.,  1.]])
Note that element C0,1, which shows the correlation between x0 and x1, is negative. Further, note how x and y are combined:
>>> x = [-2.1, -1,  4.3]
>>> y = [3,  1.1,  0.12]
>>> X = np.stack((x, y), axis=0)
>>> print(np.cov(X))
[[ 11.71        -4.286     ]
 [ -4.286        2.14413333]]
>>> print(np.cov(x, y))
[[ 11.71        -4.286     ]
 [ -4.286        2.14413333]]
>>> print(np.cov(x))
11.71

numpy.fromfunction
    使用函数规则创建数组是非常方便的方法，我们常用numpy的fromfunction函数来实现这个功能。
    创建一个函数，它是“y = i * 2”的表示形式：
import numpy as np
​
def func(i):
    print(i)  # 输出“[0. 1. 2. 3. 4.]”
    return i * 2
​
array = np.fromfunction(func, (5,))
print(array)  # 输出“[0. 2. 4. 6. 8.]”
fromfunction的第二个参数定义了数组的形状，参数(5,)表示的数组是[0,1,2,3,4]。
    假如创建的是二维数组，则函数式有两个自变量，例如“y = i * j”这个函数：
import numpy as np
​
def func(i, j):
    print("i =\n", i)
    print("j =\n", j)
    return i * j
​
array = np.fromfunction(func, (3, 3))
print("----------")
print(array)
执行结果：
i =
 [[0. 0. 0.]
 [1. 1. 1.]
 [2. 2. 2.]]
j =
 [[0. 1. 2.]
 [0. 1. 2.]
 [0. 1. 2.]]
----------
[[0. 0. 0.]
 [0. 1. 2.]
 [0. 2. 4.]]
换一种取值范围：
import numpy as np
​
def func(i, j):
    print("i =\n", i)
    print("j =\n", j)
    return i * j
​
array = np.fromfunction(func, (3, 2))
print("----------")
print(array)
执行结果：
i =
 [[0. 0.]
 [1. 1.]
 [2. 2.]]
j =
 [[0. 1.]
 [0. 1.]
 [0. 1.]]
----------
[[0. 0.]
 [0. 1.]
 [0. 2.]]

astype和dtype
    astype用于数据类型转换，dtype用于查看数据类型：
import numpy as np
​
arr = np.array([1, 2, 3, 4, 5])
print(arr)  # 输出“[1 2 3 4 5]”
print(arr.dtype)  # 输出“int32”
float_arr = arr.astype(np.float64)  # int型转为float型
print(float_arr.dtype)  # 输出“float64”
print(float_arr)  # 输出“[1. 2. 3. 4. 5.]”
# ------------------------------------------
arr2 = np.array([1.1, 2.2, 3.3, 4.4, 5.3221])
print(arr2)  # 输出“[1.1    2.2    3.3    4.4    5.3221]”
print(arr2.dtype)  # 输出“float64”
int_arr2 = arr2.astype(np.int32)  # float型转为int型
print(int_arr2)  # 输出“[1 2 3 4 5]”
# ------------------------------------------
numeric_strings = np.array(['1.2', '2.3', '3.2141'], dtype=np.string_)
print(numeric_strings)  # 输出“[b'1.2' b'2.3' b'3.2141']”
# 此处写的是float，而不是np.float64。Numpy很聪明，会将python类型映射到等价的dtype上
float_arr = numeric_strings.astype(float)
print(float_arr)  # 输出“[1.2    2.3    3.2141]”

numpy.ndarray.tofile
    ndarray.tofile(fid, sep="", format="%s"): Write array to a file as text or binary (default). Data is always written in "C" order, independent of the order of a. The data produced by this method can be recovered using the function fromfile(). Parameters: 
fid: file or str. An open file object, or a string containing a filename.
sep: str. Separator between array items for text output. If ""(empty), a binary file is written, equivalent to file.write(a.tobytes()).
format: str. Format string for text file output. Each entry in the array is formatted to text by first converting it to the closest Python type, and then using "format" % item.
    Notes: This is a convenience function for quick storage of array data. Information on endianness and precision is lost, so this method is not a good choice for files intended to archive data or transport data between machines with different endianness. Some of these problems can be overcome by outputting the data as text files, at the expense of speed and file size.
    When fid is a file object, array contents are directly written to the file, bypassing the file object's write method. As a result, tofile cannot be used with files objects supporting compression (e.g., GzipFile) or file-like objects that do not support fileno() (e.g., BytesIO).

numpy.around(round)
    numpy.around(a, decimals=0, out=None): Evenly round to the given number of decimals. Parameters:   
a: array_like. Input data.
decimals: int, optional. Number of decimal places to round to (default: 0). If decimals is negative, it specifies the number of positions to the left of the decimal point.
out: ndarray, optional. Alternative output array in which to place the result. It must have the same shape as the expected output, but the type of the output values will be cast if necessary.
    An array of the same type as a, containing the rounded values. Unless out was specified, a new array is created. A reference to the result is returned.
    The real and imaginary parts of complex numbers are rounded separately. The result of rounding a float is a float.
    Notes: For values exactly halfway between rounded decimal values, NumPy rounds to the nearest even value. Thus 1.5 and 2.5 round to 2.0, -0.5 and 0.5 round to 0.0, etc. Results may also be surprising due to the inexact representation of decimal fractions in the IEEE floating point standard and errors introduced when scaling by powers of ten.
>>> np.around([0.37, 1.64])
array([ 0.,  2.])
>>> np.around([0.37, 1.64], decimals=1)
array([0.4, 1.6])
>>> np.around([.5, 1.5, 2.5, 3.5, 4.5]) # rounds to nearest even value
array([0., 2., 2., 4., 4.])
>>> np.around([1, 2, 3, 11], decimals=1) # ndarray of ints is returned
array([1, 2, 3, 11])
>>> np.around([1, 2, 3, 11], decimals=-1)
array([0, 0,  0, 10])

numpy.mod
    numpy.mod(x1, x2, /, out=None, *, where=True, casting='same_kind', order='K', dtype=None, subok=True[, signature, extobj]) = <ufunc 'remainder'>: Return element-wise remainder of division(返回输入数组中相应元素的除法余数). Computes the remainder complementary to the floor_divide function. It is equivalent to the Python modulus operator "x1 % x2" and has the same sign as the divisor x2. The MATLAB function equivalent to np.remainder is mod.
    Warning: This should not be confused with:
Python 3.7's math.remainder and C's remainder, which computes the IEEE remainder, which are the complement to round(x1 / x2).
The MATLAB rem function and or the C % operator which is the complement to int(x1 / x2).
    Parameters:   
x1: array_like. Dividend array.
x2: array_like. Divisor array.
out: ndarray, None, or tuple of ndarray and None, optional. A location into which the result is stored. If provided, it must have a shape that the inputs broadcast to. If not provided or None, a freshly-allocated array is returned. A tuple (possible only as a keyword argument) must have length equal to the number of outputs.
where: array_like, optional. Values of True indicate to calculate the ufunc at that position, values of False indicate to leave the value in the output alone.
**kwargs: For other keyword-only arguments, see the ufunc docs.
Returns: y : ndarray. The element-wise remainder of the quotient floor_divide(x1, x2). This is a scalar if both x1 and x2 are scalars. Notes: Returns 0 when x2 is 0 and both x1 and x2 are (arrays of) integers.
>>> np.remainder([4, 7], [2, 3])
array([0, 1])
>>> np.remainder(np.arange(7), 5)
array([0, 1, 2, 3, 4, 0, 1])

numpy.swapaxes
    有时需要对矩阵进行旋转维度操作，二维转置直接使用“.T”，高维矩阵则需要借助numpy.swapaxes函数：
>>> a = np.array([[1, 2, 3], [4, 5, 6]])
>>> a
array([[1, 2, 3],
       [4, 5, 6]])
>>> a.shape
(2, 3)
>>> a.swapaxes(0, 1)  # a.swapaxes(0,1)等价于np.swapaxes(a,0,1)
array([[1, 4],
       [2, 5],
       [3, 6]])
>>> a.swapaxes(0, 1).shape
(3, 2)
>>> a.T.shape  # 由于是二维矩阵，可以使用“.T”操作
(3, 2)

numpy.ascontiguousarray
    numpy.ascontiguousarray(a, dtype=None): Return a contiguous array in memory (C order). Parameters:   
a: array_like. Input array.
dtype: str or dtype object, optional. Data-type of returned array.
Returns: out : ndarray. Contiguous array of same shape and content as a, with type dtype if specified.
>>> x = np.arange(6).reshape(2,3)
>>> np.ascontiguousarray(x, dtype=np.float32)
array([[ 0.,  1.,  2.],
       [ 3.,  4.,  5.]], dtype=float32)
>>> x.flags['C_CONTIGUOUS']
True

numpy.greater_equal
    numpy.greater_equal(x1, x2, /, out=None, *, where=True, casting='same_kind', order='K', dtype=None, subok=True[, signature, extobj]): Return the truth value of (x1 >= x2) element-wise. Parameters:   
x1, x2 : array_like. Input arrays. If x1.shape != x2.shape, they must be broadcastable to a common shape (which may be the shape of one or the other).
out : ndarray, None, or tuple of ndarray and None, optional. A location into which the result is stored. If provided, it must have a shape that the inputs broadcast to. If not provided or None, a freshly-allocated array is returned. A tuple (possible only as a keyword argument) must have length equal to the number of outputs.
where : array_like, optional. Values of True indicate to calculate the ufunc at that position, values of False indicate to leave the value in the output alone.
    Returns: out : bool or ndarray of bool. Output array, element-wise comparison of x1 and x2. Typically of type bool, unless dtype=object is passed. This is a scalar if both x1 and x2 are scalars.
>>> np.greater_equal([4, 2, 1], [2, 2, 2])
array([ True, True, False])
