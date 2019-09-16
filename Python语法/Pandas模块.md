---
title: Pandas模块
date: 2019-02-11 10:03:42
categories: Python语法
---
### Pandas基本介绍

#### Numpy和Pandas

&emsp;&emsp;如果用`python`的列表和字典来作比较，那么可以说`Numpy`是列表形式的，没有数值标签；而`Pandas`就是字典形式。`Pandas`是基于`Numpy`构建的，让`Numpy`为中心的应用变得更加简单。
&emsp;&emsp;要使用`pandas`，首先需要了解它主要的两个数据结构：`Series`和`DataFrame`。

#### Series

&emsp;&emsp;代码如下：

``` python
import pandas as pd
import numpy as np

s = pd.Series([1, 3, 6, np.nan, 44, 1])
print(s)
```

执行结果：

``` python
0     1.0
1     3.0
2     6.0
3     NaN
4    44.0
5     1.0
dtype: float64
```

`Series`的字符串表现形式为：索引在左边，值在右边。由于我们没有为数据指定索引，于是会自动创建一个`0`到`N - 1`(`N`为长度)的整数型索引。

#### DataFrame

&emsp;&emsp;代码如下：

``` python
import pandas as pd
import numpy as np

dates = pd.date_range('20160101', periods=6)
df = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=['a', 'b', 'c', 'd'])
print(df)
```

执行结果：

``` python
                   a         b         c         d
2016-01-01 -1.615754 -1.572751  0.886368  0.639823
2016-01-02  0.592734 -1.439370 -0.880371  0.193510
2016-01-03  1.584482  0.016471  0.420065  1.685260
2016-01-04  0.434318 -1.079135  0.134948  1.038391
2016-01-05 -0.169911 -0.252141 -0.307869 -0.806533
2016-01-06  0.763159  0.038354 -0.827668 -0.031835
```

`DataFrame`是一个表格型的数据结构，它包含有一组有序的列，每列可以是不同的值类型(数值、字符串、布尔值等)。`DataFrame`既有行索引也有列索引，它可以被看做由`Series`组成的大字典。
&emsp;&emsp;我们可以根据每一个不同的索引来挑选数据，比如挑选`b`的元素：

``` python
print(df['b'])
```

执行结果：

``` python
2016-01-01    0.266451
2016-01-02   -0.714666
2016-01-03   -0.300667
2016-01-04   -0.365430
2016-01-05   -1.703521
2016-01-06   -0.903908
Freq: D, Name: b, dtype: float64
```

&emsp;&emsp;再创建一组没有给定行标签和列标签的数据`df1`：

``` python
import pandas as pd
import numpy as np

df1 = pd.DataFrame(np.arange(12).reshape((3, 4)))
print(df1)
```

执行结果：

``` python
   0  1   2   3
0  0  1   2   3
1  4  5   6   7
2  8  9  10  11
```

还有一种生成`df`的方法：

``` python
import pandas as pd
import numpy as np

df2 = pd.DataFrame(
    {'A': 1.,
     'B': pd.Timestamp('20130102'),
     'C': pd.Series(1, index=list(range(4)), dtype='float32'),
     'D': np.array([3] * 4, dtype='int32'),
     'E': pd.Categorical(["test", "train", "test", "train"]),
     'F': 'foo'})

print(df2)
```

执行结果：

``` python
     A          B    C  D      E    F
0  1.0 2013-01-02  1.0  3   test  foo
1  1.0 2013-01-02  1.0  3  train  foo
2  1.0 2013-01-02  1.0  3   test  foo
3  1.0 2013-01-02  1.0  3  train  foo
```

这种方法能对每一列的数据进行特殊对待。如果想要查看数据中的类型，我们可以用`dtype`这个属性：

``` python
print(df2.dtypes)
```

执行结果：

``` python
A           float64
B    datetime64[ns]
C           float32
D             int32
E          category
F            object
dtype: object
```

&emsp;&emsp;如果想看对列的序号：

``` python
print(df2.index)
```

执行结果：

``` python
Int64Index([0, 1, 2, 3], dtype='int64')
```

同样，每种数据的名称也能看到：

``` python
print(df2.columns)
```

执行结果：

``` python
Index(['A', 'B', 'C', 'D', 'E', 'F'], dtype='object')
```

如果只想看所有`df2`的值：

``` python
print(df2.values)
```

执行结果：

``` python
[[1.0 Timestamp('2013-01-02 00:00:00') 1.0 3 'test' 'foo']
 [1.0 Timestamp('2013-01-02 00:00:00') 1.0 3 'train' 'foo']
 [1.0 Timestamp('2013-01-02 00:00:00') 1.0 3 'test' 'foo']
 [1.0 Timestamp('2013-01-02 00:00:00') 1.0 3 'train' 'foo']]
```

想知道数据的总结，可以用`describe`：

``` python
print(df2.describe())
```

执行结果：

``` python
         A    C    D
count  4.0  4.0  4.0
mean   1.0  1.0  3.0
std    0.0  0.0  0.0
min    1.0  1.0  3.0
25%    1.0  1.0  3.0
50%    1.0  1.0  3.0
75%    1.0  1.0  3.0
max    1.0  1.0  3.0
```

如果想翻转数据，可以使用`transpose`：

``` python
print(df2.T)
```

执行结果：

``` python
                     0                    1                    2  \
A                    1                    1                    1
B  2013-01-02 00:00:00  2013-01-02 00:00:00  2013-01-02 00:00:00
C                    1                    1                    1
D                    3                    3                    3
E                 test                train                 test
F                  foo                  foo                  foo
                     3
A                    1
B  2013-01-02 00:00:00
C                    1
D                    3
E                train
F                  foo
```

如果想对数据的`index`进行排序并输出：

``` python
print(df2.sort_index(axis=1, ascending=False))
```

执行结果：

``` python
     F      E  D    C          B    A
0  foo   test  3  1.0 2013-01-02  1.0
1  foo  train  3  1.0 2013-01-02  1.0
2  foo   test  3  1.0 2013-01-02  1.0
3  foo  train  3  1.0 2013-01-02  1.0
```

如果是对数据值排序输出：

``` python
print(df2.sort_values(by='B'))
```

执行结果：

``` python
     A          B    C  D      E    F
0  1.0 2013-01-02  1.0  3   test  foo
1  1.0 2013-01-02  1.0  3  train  foo
2  1.0 2013-01-02  1.0  3   test  foo
3  1.0 2013-01-02  1.0  3  train  foo
```

### Pandas选择数据

&emsp;&emsp;我们建立了一个`6 * 4`的矩阵数据：

``` python
import pandas as pd
import numpy as np

dates = pd.date_range('20130101', periods=6)
df = pd.DataFrame(np.arange(24).reshape((6, 4)), index=dates, columns=['A', 'B', 'C', 'D'])
print(df)
```

执行结果：

``` python
             A   B   C   D
2013-01-01   0   1   2   3
2013-01-02   4   5   6   7
2013-01-03   8   9  10  11
2013-01-04  12  13  14  15
2013-01-05  16  17  18  19
2013-01-06  20  21  22  23
```

如果我们想选取`DataFrame`中的数据，可以使用下面两种方法：

``` python
print(df['A'])
print(df.A)
```

执行结果都是：

``` python
2013-01-01     0
2013-01-02     4
2013-01-03     8
2013-01-04    12
2013-01-05    16
2013-01-06    20
Freq: D, Name: A, dtype: int32
```

让选择跨越多行或多列：

``` python
print(df[0:3])
print("-----------------------")
print(df['20130102':'20130104'])
```

执行结果：

``` python
            A  B   C   D
2013-01-01  0  1   2   3
2013-01-02  4  5   6   7
2013-01-03  8  9  10  11
-----------------------
             A   B   C   D
2013-01-02   4   5   6   7
2013-01-03   8   9  10  11
2013-01-04  12  13  14  15
```

如果是`df[3:3]`，将会是一个空对象。后者选择`20130102`到`20130104`标签之间的数据，并且包括这两个标签。

#### 根据标签(loc)

&emsp;&emsp;同样我们可以使用标签来选择数据，本示例主要通过标签名字选择某一行数据，或者通过选择某行或者所有行，然后选其中某一列或几列数据：

``` python
print(df.loc['20130102'])
print("-----------------------")
print(df.loc[:, ['A', 'B']])
print("-----------------------")
print(df.loc['20130102', ['A', 'B']])
```

执行结果：

``` python
A    4
B    5
C    6
D    7
Name: 2013-01-02 00:00:00, dtype: int32
-----------------------
             A   B
2013-01-01   0   1
2013-01-02   4   5
2013-01-03   8   9
2013-01-04  12  13
2013-01-05  16  17
2013-01-06  20  21
-----------------------
A    4
B    5
Name: 2013-01-02 00:00:00, dtype: int32
```

#### 根据序列(iloc)

&emsp;&emsp;我们也可以使用位置选择数据，例如选取某一个、连续选择或者跨行选择等操作：

``` python
print(df.iloc[3, 1])
print("-----------------------")
print(df.iloc[3:5, 1:3])
print("-----------------------")
print(df.iloc[[1, 3, 5], 1:3])
```

执行结果：

``` python
13
-----------------------
             B   C
2013-01-04  13  14
2013-01-05  17  18
-----------------------
             B   C
2013-01-02   5   6
2013-01-04  13  14
2013-01-06  21  22
```

#### 根据混合条件(ix)

&emsp;&emsp;我们也可以采用采用混合选择的方式，例如选择`A`和`C`两列，并选择前三行的数据：

``` python
print(df.ix[:3, ['A', 'C']])
```

执行结果：

``` python
            A   C
2013-01-01  0   2
2013-01-02  4   6
2013-01-03  8  10
```

#### 通过判断的筛选

&emsp;&emsp;最后可以采用判断指令(`Boolean indexing`)进行选择，可以约束某项条件，然后选择所有符合该条件的数据：

``` python
print(df[df.A > 8])
```

执行结果：

``` python
             A   B   C   D
2013-01-04  12  13  14  15
2013-01-05  16  17  18  19
2013-01-06  20  21  22  23
```

### Pandas设置值

#### 创建数据

&emsp;&emsp;我们可以根据自己的需求，用`pandas`更改数据里面的值，或者加上一些空的或者有数值的列。首先建立了一个`6 * 4`的矩阵数据：

``` python
import pandas as pd
import numpy as np

dates = pd.date_range('20130101', periods=6)
df = pd.DataFrame(np.arange(24).reshape((6, 4)), index=dates, columns=['A', 'B', 'C', 'D'])
print(df)
```

执行结果：

``` python
             A   B   C   D
2013-01-01   0   1   2   3
2013-01-02   4   5   6   7
2013-01-03   8   9  10  11
2013-01-04  12  13  14  15
2013-01-05  16  17  18  19
2013-01-06  20  21  22  23
```

#### 根据位置设置(loc和iloc)

&emsp;&emsp;我们可以利用索引或者标签确定需要修改值的位置：

``` python
df.iloc[2, 2] = 1111
df.loc['20130101', 'B'] = 2222
print(df)
```

执行结果：

``` python
             A     B     C   D
2013-01-01   0  2222     2   3
2013-01-02   4     5     6   7
2013-01-03   8     9  1111  11
2013-01-04  12    13    14  15
2013-01-05  16    17    18  19
2013-01-06  20    21    22  23
```

#### 根据条件设置

&emsp;&emsp;我们想要更改`B`中的数值，而更改的位置是取决于`A`的。对于`A`大于`4`的位置，更改`B`在相应位置上的数为`0`：

``` python
df.B[df.A > 4] = 0
print(df)
```

执行结果：

``` python
             A  B   C   D
2013-01-01   0  1   2   3
2013-01-02   4  5   6   7
2013-01-03   8  0  10  11
2013-01-04  12  0  14  15
2013-01-05  16  0  18  19
2013-01-06  20  0  22  23
```

#### 按行或列设置

&emsp;&emsp;如果需要对整列做批处理，例如加上一列`F`，并将`F`列全改为`NaN`：

``` python
df['F'] = np.nan
print(df)
```

执行结果：

``` python
             A   B   C   D   F
2013-01-01   0   1   2   3 NaN
2013-01-02   4   5   6   7 NaN
2013-01-03   8   9  10  11 NaN
2013-01-04  12  13  14  15 NaN
2013-01-05  16  17  18  19 NaN
2013-01-06  20  21  22  23 NaN
```

#### 添加数据

&emsp;&emsp;用上面的方法也可以加上`Series`序列，但是长度必须对齐：

``` python
df['E'] = pd.Series([1, 2, 3, 4, 5, 6], index=pd.date_range('20130101', periods=6))
print(df)
```

执行结果：

``` python
             A   B   C   D  E
2013-01-01   0   1   2   3  1
2013-01-02   4   5   6   7  2
2013-01-03   8   9  10  11  3
2013-01-04  12  13  14  15  4
2013-01-05  16  17  18  19  5
2013-01-06  20  21  22  23  6
```

### Pandas处理丢失数据

#### 创建含NaN的矩阵

&emsp;&emsp;有时我们导入或处理数据，会产生一些空的或者是`NaN`数据，如何删除或者是填补这些`NaN`数据就是今天所要提到的内容。建立了一个`6 * 4`的矩阵数据，并且把两个位置设置为空：

``` python
import pandas as pd
import numpy as np

dates = pd.date_range('20130101', periods=6)
df = pd.DataFrame(np.arange(24).reshape((6, 4)), index=dates, columns=['A', 'B', 'C', 'D'])
df.iloc[0, 1] = np.nan
df.iloc[1, 2] = np.nan
print(df)
```

执行结果：

``` python
             A     B     C   D
2013-01-01   0   NaN   2.0   3
2013-01-02   4   5.0   NaN   7
2013-01-03   8   9.0  10.0  11
2013-01-04  12  13.0  14.0  15
2013-01-05  16  17.0  18.0  19
2013-01-06  20  21.0  22.0  23
```

#### pd.dropna

&emsp;&emsp;如果想直接去掉有`NaN`的行或列，可以使用`dropna`：

``` python
result = df.dropna(
    axis=0,  # 0：对行进行操作；1：对列进行操作
    how='any'  # any：只要存在NaN就drop掉；all：必须全部是NaN才drop掉
)
print(result)
```

执行结果：

``` python
             A     B     C   D
2013-01-03   8   9.0  10.0  11
2013-01-04  12  13.0  14.0  15
2013-01-05  16  17.0  18.0  19
2013-01-06  20  21.0  22.0  23
```

#### pd.fillna

&emsp;&emsp;如果是将`NaN`用其他值代替(比如代替成`0`)，则可以使用`fillna`函数：

``` python
result = df.fillna(value=0)
print(result)
```

执行结果：

``` python
             A     B     C   D
2013-01-01   0   0.0   2.0   3
2013-01-02   4   5.0   0.0   7
2013-01-03   8   9.0  10.0  11
2013-01-04  12  13.0  14.0  15
2013-01-05  16  17.0  18.0  19
2013-01-06  20  21.0  22.0  23
```

#### pd.isnull

&emsp;&emsp;判断是否有缺失数据`NaN`，为`True`表示缺失数据：

``` python
print(df.isnull())
```

执行结果：

``` python
                A      B      C      D
2013-01-01  False   True  False  False
2013-01-02  False  False   True  False
2013-01-03  False  False  False  False
2013-01-04  False  False  False  False
2013-01-05  False  False  False  False
2013-01-06  False  False  False  False
```

检测数据中是否存在`NaN`，如果存在就返回`True`：

``` python
print(np.any(df.isnull()) == True)  # 结果为True
```

### Pandas合并concat

&emsp;&emsp;在处理多组数据时，往往会对数据进行合并处理。`concat`是一种基本的合并方式，而且`concat`中有很多参数可以调整，合并成你想要的数据形式：

#### axis(合并方向)

&emsp;&emsp;`axis = 0`是预设值，因此未设定任何参数时，函数默认`axis`为`0`：

``` python
import pandas as pd
import numpy as np

df1 = pd.DataFrame(np.ones((3, 4)) * 0, columns=['a', 'b', 'c', 'd'])
df2 = pd.DataFrame(np.ones((3, 4)) * 1, columns=['a', 'b', 'c', 'd'])
df3 = pd.DataFrame(np.ones((3, 4)) * 2, columns=['a', 'b', 'c', 'd'])
res = pd.concat([df1, df2, df3], axis=0)  # concat纵向合并
print(res)
```

执行结果：

``` python
     a    b    c    d
0  0.0  0.0  0.0  0.0
1  0.0  0.0  0.0  0.0
2  0.0  0.0  0.0  0.0
0  1.0  1.0  1.0  1.0
1  1.0  1.0  1.0  1.0
2  1.0  1.0  1.0  1.0
0  2.0  2.0  2.0  2.0
1  2.0  2.0  2.0  2.0
2  2.0  2.0  2.0  2.0
```

#### ignore_index(重置index)

&emsp;&emsp;仔细观察会发现结果的`index`是`[0, 1, 2, 0, 1, 2, 0, 1, 2]`，若要将`index`重置，需要使用`ignore_index`函数：

``` python
res = pd.concat([df1, df2, df3], axis=0, ignore_index=True)  # 将index_ignore设定为True
print(res)
```

执行结果：

``` python
     a    b    c    d
0  0.0  0.0  0.0  0.0
1  0.0  0.0  0.0  0.0
2  0.0  0.0  0.0  0.0
3  1.0  1.0  1.0  1.0
4  1.0  1.0  1.0  1.0
5  1.0  1.0  1.0  1.0
6  2.0  2.0  2.0  2.0
7  2.0  2.0  2.0  2.0
8  2.0  2.0  2.0  2.0
```

#### join(合并方式)

&emsp;&emsp;`join='outer'`为预设值，因此未设定任何参数时，函数默认`join`是`outer`。此方式是依照`column`来做纵向合并，有相同的`column`则上下合并在一起，其他独自的`column`各自成列，原本没有值的位置皆以`NaN`填充：

``` python
import pandas as pd
import numpy as np

df1 = pd.DataFrame(np.ones((3, 4)) * 0, columns=['a', 'b', 'c', 'd'], index=[1, 2, 3])
df2 = pd.DataFrame(np.ones((3, 4)) * 1, columns=['b', 'c', 'd', 'e'], index=[2, 3, 4])
res = pd.concat([df1, df2], axis=0, join='outer')  # 纵向“外”合并df1与df2
print(res)
```

执行结果：

``` python
     a    b    c    d    e
1  0.0  0.0  0.0  0.0  NaN
2  0.0  0.0  0.0  0.0  NaN
3  0.0  0.0  0.0  0.0  NaN
2  NaN  1.0  1.0  1.0  1.0
3  NaN  1.0  1.0  1.0  1.0
4  NaN  1.0  1.0  1.0  1.0
```

对于内合并，只有相同的`column`合并在一起，其他的会被抛弃：

``` python
res = pd.concat([df1, df2], axis=0, join='inner')  # 纵向“内”合并df1与df2
print(res)
print("-----------------")
res = pd.concat([df1, df2], axis=0, join='inner', ignore_index=True)  # 重置index并打印结果
print(res)
```

执行结果：

``` python
     b    c    d
1  0.0  0.0  0.0
2  0.0  0.0  0.0
3  0.0  0.0  0.0
2  1.0  1.0  1.0
3  1.0  1.0  1.0
4  1.0  1.0  1.0
-----------------
     b    c    d
0  0.0  0.0  0.0
1  0.0  0.0  0.0
2  0.0  0.0  0.0
3  1.0  1.0  1.0
4  1.0  1.0  1.0
5  1.0  1.0  1.0
```

#### join_axes(依照axes合并)

&emsp;&emsp;代码如下：

``` python
import pandas as pd
import numpy as np

df1 = pd.DataFrame(np.ones((3, 4)) * 0, columns=['a', 'b', 'c', 'd'], index=[1, 2, 3])
df2 = pd.DataFrame(np.ones((3, 4)) * 1, columns=['b', 'c', 'd', 'e'], index=[2, 3, 4])
res = pd.concat([df1, df2], axis=1, join_axes=[df1.index])  # 依照df1.index进行横向合并
print(res)
print("--------------------------")
res = pd.concat([df1, df2], axis=1)  # 移除join_axes，并打印结果
print(res)
```

执行结果：

``` python
     a    b    c    d    b    c    d    e
1  0.0  0.0  0.0  0.0  NaN  NaN  NaN  NaN
2  0.0  0.0  0.0  0.0  1.0  1.0  1.0  1.0
3  0.0  0.0  0.0  0.0  1.0  1.0  1.0  1.0
--------------------------
     a    b    c    d    b    c    d    e
1  0.0  0.0  0.0  0.0  NaN  NaN  NaN  NaN
2  0.0  0.0  0.0  0.0  1.0  1.0  1.0  1.0
3  0.0  0.0  0.0  0.0  1.0  1.0  1.0  1.0
4  NaN  NaN  NaN  NaN  1.0  1.0  1.0  1.0
```

#### append(添加数据)

&emsp;&emsp;`append`只有纵向合并，没有横向合并：

``` python
import pandas as pd
import numpy as np

df1 = pd.DataFrame(np.ones((3, 4)) * 0, columns=['a', 'b', 'c', 'd'])
df2 = pd.DataFrame(np.ones((3, 4)) * 1, columns=['a', 'b', 'c', 'd'])
df3 = pd.DataFrame(np.ones((3, 4)) * 1, columns=['a', 'b', 'c', 'd'])
s1 = pd.Series([1, 2, 3, 4], index=['a', 'b', 'c', 'd'])
res = df1.append(df2, ignore_index=True)  # 将df2合并到df1的下面，以及重置index
print(res)
print("-----------------------")
res = df1.append([df2, df3], ignore_index=True)  # 合并多个df，将df2与df3合并至df1的下面，以及重置index
print(res)
print("-----------------------")
res = df1.append(s1, ignore_index=True)  # 合并series，将s1合并至df1，以及重置index
print(res)
```

执行结果：

``` python
     a    b    c    d
0  0.0  0.0  0.0  0.0
1  0.0  0.0  0.0  0.0
2  0.0  0.0  0.0  0.0
3  1.0  1.0  1.0  1.0
4  1.0  1.0  1.0  1.0
5  1.0  1.0  1.0  1.0
-----------------------
     a    b    c    d
0  0.0  0.0  0.0  0.0
1  0.0  0.0  0.0  0.0
2  0.0  0.0  0.0  0.0
3  1.0  1.0  1.0  1.0
4  1.0  1.0  1.0  1.0
5  1.0  1.0  1.0  1.0
6  1.0  1.0  1.0  1.0
7  1.0  1.0  1.0  1.0
8  1.0  1.0  1.0  1.0
-----------------------
     a    b    c    d
0  0.0  0.0  0.0  0.0
1  0.0  0.0  0.0  0.0
2  0.0  0.0  0.0  0.0
3  1.0  2.0  3.0  4.0
```

### Pandas合并merge

&emsp;&emsp;`pandas`中的`merge`和`concat`类似，但主要是用于两组有`key column`的数据，通常也被用在`Database`的处理当中。

#### 依据一组key合并

&emsp;&emsp;代码如下：

``` python
import pandas as pd

left = pd.DataFrame(
    {'key': ['K0', 'K1', 'K2', 'K3'],
     'A': ['A0', 'A1', 'A2', 'A3'],
     'B': ['B0', 'B1', 'B2', 'B3']})
right = pd.DataFrame(
    {'key': ['K0', 'K1', 'K2', 'K3'],
     'C': ['C0', 'C1', 'C2', 'C3'],
     'D': ['D0', 'D1', 'D2', 'D3']})

print(left)
print("---------------")
print(right)
print("---------------")
res = pd.merge(left, right, on='key')  # 依据“key column”合并
print(res)
```

执行结果：

``` python
    A   B key
0  A0  B0  K0
1  A1  B1  K1
2  A2  B2  K2
3  A3  B3  K3
---------------
    C   D key
0  C0  D0  K0
1  C1  D1  K1
2  C2  D2  K2
3  C3  D3  K3
---------------
    A   B key   C   D
0  A0  B0  K0  C0  D0
1  A1  B1  K1  C1  D1
2  A2  B2  K2  C2  D2
3  A3  B3  K3  C3  D3
```

#### 依据两组key合并

&emsp;&emsp;合并时有`4`种方法，即`how = ['left', 'right', 'outer', 'inner']`，预设值`how`为`inner`：

``` python
import pandas as pd
left = pd.DataFrame({
    'key1': ['K0', 'K0', 'K1', 'K2'],
    'key2': ['K0', 'K1', 'K0', 'K1'],
       'A': ['A0', 'A1', 'A2', 'A3'],
       'B': ['B0', 'B1', 'B2', 'B3']})
right = pd.DataFrame(
    {'key1': ['K0', 'K1', 'K1', 'K2'],
     'key2': ['K0', 'K0', 'K0', 'K0'],
        'C': ['C0', 'C1', 'C2', 'C3'],
        'D': ['D0', 'D1', 'D2', 'D3']})
print("--------left------")
print(left)
print("--------right------")
print(right)
print("--------inner合并------")
# 依据key1与key2的columns进行合并
res = pd.merge(left, right, on=['key1', 'key2'], how='inner')
print(res)
print("--------outer合并------")
res = pd.merge(left, right, on=['key1', 'key2'], how='outer')
print(res)
print("--------left合并------")
res = pd.merge(left, right, on=['key1', 'key2'], how='left')
print(res)
print("--------right合并------")
res = pd.merge(left, right, on=['key1', 'key2'], how='right')
print(res)
```

执行结果：

``` python
--------left------
    A   B key1 key2
0  A0  B0   K0   K0
1  A1  B1   K0   K1
2  A2  B2   K1   K0
3  A3  B3   K2   K1
--------right------
    C   D key1 key2
0  C0  D0   K0   K0
1  C1  D1   K1   K0
2  C2  D2   K1   K0
3  C3  D3   K2   K0
--------inner合并------
    A   B key1 key2   C   D
0  A0  B0   K0   K0  C0  D0
1  A2  B2   K1   K0  C1  D1
2  A2  B2   K1   K0  C2  D2
--------outer合并------
     A    B key1 key2    C    D
0   A0   B0   K0   K0   C0   D0
1   A1   B1   K0   K1  NaN  NaN
2   A2   B2   K1   K0   C1   D1
3   A2   B2   K1   K0   C2   D2
4   A3   B3   K2   K1  NaN  NaN
5  NaN  NaN   K2   K0   C3   D3
--------left合并------
    A   B key1 key2    C    D
0  A0  B0   K0   K0   C0   D0
1  A1  B1   K0   K1  NaN  NaN
2  A2  B2   K1   K0   C1   D1
3  A2  B2   K1   K0   C2   D2
4  A3  B3   K2   K1  NaN  NaN
--------right合并------
     A    B key1 key2   C   D
0   A0   B0   K0   K0  C0  D0
1   A2   B2   K1   K0  C1  D1
2   A2   B2   K1   K0  C2  D2
3  NaN  NaN   K2   K0  C3  D3
```

#### Indicator

&emsp;&emsp;`indicator = True`会将合并的记录放在新的一列：

``` python
import pandas as pd

df1 = pd.DataFrame({'col1': [0, 1], 'col_left': ['a', 'b']})
df2 = pd.DataFrame({'col1': [1, 2, 2], 'col_right': [2, 2, 2]})
print("-------df1-------")
print(df1)
print("-------df2-------")
print(df2)
print("-------启用indicator-------")
# 依据col1进行合并，并启用“indicator = True”
res = pd.merge(df1, df2, on='col1', how='outer', indicator=True)
print(res)
print("-------自定义名称-------")
# 自定义“indicator column”的名称
res = pd.merge(df1, df2, on='col1', how='outer', indicator='indicator_column')
print(res)
```

执行结果：

``` python
-------df1-------
   col1 col_left
0     0        a
1     1        b
-------df2-------
   col1  col_right
0     1          2
1     2          2
2     2          2
-------启用indicator-------
   col1 col_left  col_right      _merge
0     0        a        NaN   left_only
1     1        b        2.0        both
2     2      NaN        2.0  right_only
3     2      NaN        2.0  right_only
-------自定义名称-------
   col1 col_left  col_right indicator_column
0     0        a        NaN        left_only
1     1        b        2.0             both
2     2      NaN        2.0       right_only
3     2      NaN        2.0       right_only
```

#### 依据index合并

&emsp;&emsp;代码如下：

``` python
import pandas as pd

left = pd.DataFrame(
    {'A': ['A0', 'A1', 'A2'],
     'B': ['B0', 'B1', 'B2']},
    index=['K0', 'K1', 'K2'])
right = pd.DataFrame(
    {'C': ['C0', 'C2', 'C3'],
     'D': ['D0', 'D2', 'D3']},
    index=['K0', 'K2', 'K3'])

print("-----left-----")
print(left)
print("-----right-----")
print(right)
print("-----outer合并-----")
res = pd.merge(left, right, left_index=True, right_index=True, how='outer')
print(res)
print("-----inner合并-----")
res = pd.merge(left, right, left_index=True, right_index=True, how='inner')
print(res)
```

### 解决overlapping的问题

&emsp;&emsp;代码如下：

``` python
import pandas as pd

boys = pd.DataFrame({'k': ['K0', 'K1', 'K2'], 'age': [1, 2, 3]})
girls = pd.DataFrame({'k': ['K0', 'K0', 'K3'], 'age': [4, 5, 6]})
# 使用suffixes解决overlapping的问题
res = pd.merge(boys, girls, on='k', suffixes=['_boy', '_girl'], how='inner')
print(res)
```

执行结果：

``` python
   age_boy   k  age_girl
0        1  K0         4
1        1  K0         5
```

### 读取csv文件

&emsp;&emsp;假设`csv`文件内容如下：

``` python
name,physics,python,math,english
Google,100,100,25,12
Facebook,45,54,44,88
Twitter,54,76,13,91
Yahoo,54,452,26,100
```

使用`Pandas`进行读取：

``` python
import pandas as pd

marks = pd.read_csv("test.csv")
print(marks)
```

执行结果：

``` python
       name  physics  python  math  english
0    Google      100     100    25       12
1  Facebook       45      54    44       88
2   Twitter       54      76    13       91
3     Yahoo       54     452    26      100
```

### get_dummies

&emsp;&emsp;`pandas`使用`get_dummies`进行`one-hot`编码：

``` python
import pandas as pd

xiaoming = pd.DataFrame([1, 2, 3], index=['yellow', 'red', 'blue'], columns=['hat'])
print(xiaoming)
print("--------------")
hat_ranks = pd.get_dummies(xiaoming['hat'], prefix='hat')
print(hat_ranks.head())
```

执行结果：

``` python
        hat
yellow    1
red       2
blue      3
--------------
        hat_1  hat_2  hat_3
yellow      1      0      0
red         0      1      0
blue        0      0      1
```

### keys函数

&emsp;&emsp;Return a list of column names.

### Categorical函数

&emsp;&emsp;该函数用于创建分组，主要有两种方式：

``` python
my_categories = pd.Categorical(['foo', 'bar', 'baz', 'foo', 'bar'])
print(my_categories)  # 输出“[foo, bar, baz, foo, bar]”
categories = ['foo', 'bar', 'baz']
codes = [0, 1, 2, 0, 0, 1]
my_cats_2 = pd.Categorical.from_codes(codes, categories, ordered=True)
print(my_cats_2)  # 输出“[foo, bar, baz, foo, foo, bar]”
```

### pandas.DataFrame.head

&emsp;&emsp;`DataFrame.head(n=5)`: Return the first `n` rows. This function returns the first `n` rows for the object based on position. It is useful for quickly testing if your object has the right type of data in it.

``` python
>>> import pandas as pd
>>> df = pd.DataFrame({'animal':['alligator', 'bee', 'falcon', 'lion',
...                    'monkey', 'parrot', 'shark', 'whale', 'zebra']})
>>> df
      animal
0  alligator
1        bee
2     falcon
3       lion
4     monkey
5     parrot
6      shark
7      whale
8      zebra
```

Viewing the first `5` lines:

``` python
>>> df.head()
      animal
0  alligator
1        bee
2     falcon
3       lion
4     monkey
```

Viewing the first `n` lines (three in this case):

``` python
>>> df.head(3)
      animal
0  alligator
1        bee
2     falcon
```

### pandas.factorize

&emsp;&emsp;`pandas.factorize(values, sort=False, order=None, na_sentinel=-1, size_hint=None)`: Encode the object as an enumerated type or categorical variable.
&emsp;&emsp;This method is useful for obtaining a numeric representation of an array when all that matters is identifying distinct `values`. `factorize` is available as both a `top-level` function `pandas.factorize()`, and as a method `Series.factorize()` and `Index.factorize()`. Parameters:

- `values`: `sequence`. A `1-D` `seqeunce`. Sequences that aren't pandas objects are coereced to `ndarrays` before factorization.
- `sort`: `bool`, default `False`. Sort uniques and shuffle labels to maintain the relationship.
- `order`: Deprecated since `version 0.23.0`: This parameter has no effect and is deprecated.
- `na_sentinel`: `int`, default `-1`. Value to mark `not found`.
- `size_hint`: `int`, optional. Hint to the hashtable sizer.

&emsp;&emsp;Returns:

- `labels`: `ndarray`. An integer `ndarray` that's an indexer into uniques. `uniques.take(labels)` will have the same values as `values`.
- `uniques`: `ndarray`, `Index`, or `Categorical`. The unique valid `values`. When `values` is `Categorical`, `uniques` is a `Categorical`. When `values` is some other pandas object, an `Index` is returned. Otherwise, a `1-D` `ndarray` is returned.

&emsp;&emsp;**Note**: Even if there's a missing value in `values`, `uniques` will not contain an entry for it.
&emsp;&emsp;**Examples**: These examples all show `factorize` as a `top-level` method like `pd.factorize(values)`. The results are identical for methods like `Series.factorize()`.

``` python
>>> labels, uniques = pd.factorize(['b', 'b', 'a', 'c', 'b'])
>>> labels
array([0, 0, 1, 2, 0])
>>> uniques
array(['b', 'a', 'c'], dtype=object)
```

With `sort=True`, the uniques will be sorted, and labels will be shuffled so that the relationship is the maintained.

``` python
>>> labels, uniques = pd.factorize(['b', 'b', 'a', 'c', 'b'], sort=True)
>>> labels
array([1, 1, 0, 2, 1])
>>> uniques
array(['a', 'b', 'c'], dtype=object)
```

Missing values are indicated in labels with `na_sentinel` (`-1` by default). Note that missing values are never included in uniques.

``` python
>>> labels, uniques = pd.factorize(['b', None, 'a', 'c', 'b'])
>>> labels
array([ 0, -1,  1,  2,  0])
>>> uniques
array(['b', 'a', 'c'], dtype=object)
```

Thus far, we've only factorized lists (which are internally coerced to `NumPy` arrays). When factorizing `pandas` objects, the type of uniques will differ. For `Categoricals`, a `Categorical` is returned.

``` python
>>> cat = pd.Categorical(['a', 'a', 'c'], categories=['a', 'b', 'c'])
>>> labels, uniques = pd.factorize(cat)
>>> labels
array([0, 0, 1])
>>> uniques
[a, c]
Categories (3, object): [a, b, c]
```

Notice that `b` is in `uniques.categories`, desipite not being present in `cat.values`. For all other pandas objects, an `Index` of the appropriate type is returned.

``` python
>>> cat = pd.Series(['a', 'a', 'c'])
>>> labels, uniques = pd.factorize(cat)
>>> labels
array([0, 0, 1])
>>> uniques
Index(['a', 'c'], dtype='object')
```

### pandas.crosstab

&emsp;&emsp;`pandas.crosstab(index, columns, values=None, rownames=None, colnames=None, aggfunc=None, margins=False, margins_name='All', dropna=True, normalize=False)`: Compute a simple `cross-tabulation` of two (or more) factors. By default computes a frequency table of the factors unless an array of `values` and an aggregation function are passed. Parameters:

- `index`: `array-like`, `Series`, or list of `arrays/Series`. Values to group by in the rows.
- `columns`: `array-like`, `Series`, or list of `arrays/Series`. Values to group by in the columns.
- `values`: `array-like`, optional. Array of `values` to aggregate according to the factors. Requires `aggfunc` be specified.
- `aggfunc`: function, optional. If specified, requires `values` be specified as well.
- `rownames`: `sequence`, default `None`. If passed, must match number of row arrays passed.
- `colnames`: `sequence`, default `None`. If passed, must match number of column arrays passed.
- `margins`: `boolean`, default `False`. Add `row/column` margins (`subtotals`).
- `margins_name`: `string`, default `All`. Name of the `row/column` that will contain the totals when margins is `True`.
- `dropna`: `boolean`, default `True`. Do not include columns whose entries are all `NaN`.
- `normalize`: `boolean`, {`all`, `index`, `columns`}, or {`0`, `1`}, default `False`. Normalize by dividing all values by the sum of `values`.

1. If passed `all` or `True`, will normalize over all values.
2. If passed `index` will normalize over each row.
3. If passed `columns` will normalize over each column.
4. If `margins` is `True`, will also normalize margin values.

&emsp;&emsp;Any `Series` passed will have their name attributes used unless row or column names for the `cross-tabulation` are specified.
&emsp;&emsp;Any input passed containing `Categorical` data will have all of its categories included in the `cross-tabulation`, even if the actual data does not contain any instances of a particular category.
&emsp;&emsp;In the event that there aren't overlapping indexes, an empty `DataFrame` will be returned.

``` python
>>> a = np.array(["foo", "foo", "foo", "foo", "bar", "bar",
...               "bar", "bar", "foo", "foo", "foo"], dtype=object)
>>> b = np.array(["one", "one", "one", "two", "one", "one",
...               "one", "two", "two", "two", "one"], dtype=object)
>>> c = np.array(["dull", "dull", "shiny", "dull", "dull", "shiny",
...               "shiny", "dull", "shiny", "shiny", "shiny"], dtype=object)
>>> pd.crosstab(a, [b, c], rownames=['a'], colnames=['b', 'c'])
b   one        two
c   dull shiny dull shiny
a
bar    1     2    1     0
foo    2     2    1     2
# ---------------------------------------------------------------
>>> foo = pd.Categorical(['a', 'b'], categories=['a', 'b', 'c'])
>>> bar = pd.Categorical(['d', 'e'], categories=['d', 'e', 'f'])
# 'c' and 'f' are not represented in the data,
# but they still will be counted in the output
>>> crosstab(foo, bar)
col_0  d  e  f
row_0
a      1  0  0
b      0  1  0
c      0  0  0
```