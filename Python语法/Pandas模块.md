---
title: Pandas模块
categories: Python语法
date: 2019-02-11 10:03:42
---
### 数据结构

&emsp;&emsp;`pandas`主要有`2`个数据结构：`Series`和`DataFrame`。<!--more-->

#### Series

&emsp;&emsp;代码如下：

``` python
import pandas as pd
import numpy as np

s = pd.Series([1, 3, 6, np.nan])
print(s)
```

执行结果：

``` python
0    1.0
1    3.0
2    6.0
3    NaN
dtype: float64
```

#### DataFrame

&emsp;&emsp;代码如下：

``` python
import pandas as pd
import numpy as np

df2 = pd.DataFrame({
    'A': 1.,
    'B': pd.Timestamp('20130102'),
    'C': pd.Series(1, index=list(range(4)), dtype='float32'),
    'D': np.array([3] * 4, dtype='int32'),
    'E': pd.Categorical(["test", "train", "test", "train"]),
    'F': 'foo'
})

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

&emsp;&emsp;列的名称也能被查看：

``` python
print(df2.columns)
```

执行结果：

``` python
Index(['A', 'B', 'C', 'D', 'E', 'F'], dtype='object')
```

&emsp;&emsp;如果只想看所有`df2`的值：

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

### 选择数据

&emsp;&emsp;建立了一个`6 * 4`的矩阵数据：

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

&emsp;&emsp;如果想选取其中的数据，可以使用下面`2`种方法：

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

&emsp;&emsp;可以跨越多行或多列：

``` python
print(df[0:3])
```

执行结果：

``` python
            A  B   C   D
2013-01-01  0  1   2   3
2013-01-02  4  5   6   7
2013-01-03  8  9  10  11
```

#### 标签

&emsp;&emsp;使用标签来选择数据：

``` python
print(df.loc['20130102'])
print("-----------------------")
print(df.loc[:, ['A', 'B']])
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
```

#### 位置

&emsp;&emsp;使用位置选择数据：

``` python
print(df.iloc[3, 1])
print("-----------------------")
print(df.iloc[3:5, 1:3])
```

执行结果：

``` python
13
-----------------------
             B   C
2013-01-04  13  14
2013-01-05  17  18
```

#### 混合条件

&emsp;&emsp;可以采用采用混合选择的方式，例如选择`A`列和`C`列，并选择前三行的数据：

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

#### 判断筛选

&emsp;&emsp;可以采用判断的方式选择数据：

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

### 设置数值

&emsp;&emsp;建立了一个`6 * 4`的矩阵数据：

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

#### 根据位置

&emsp;&emsp;可以使用索引或者标签确定需要修改数值的位置：

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

#### 条件

&emsp;&emsp;对于`A`大于`4`的位置，将`B`在相应位置上的数值设为`0`：

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

#### 增加数据

&emsp;&emsp;加上列`F`，并将`F`列全部设为`NaN`：

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

### 处理NaN数据

&emsp;&emsp;建立了一个`6 * 4`的矩阵数据，并且把其中`2`个位置设为`NaN`：

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

#### dropna

&emsp;&emsp;如果想去掉有`NaN`的行或列，可以使用`dropna`：

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

#### fillna

&emsp;&emsp;如果想将让`NaN`被其他数值代替，则可以使用`fillna`：

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

#### isnull

&emsp;&emsp;`isnull`用于判断数据是否为`NaN`：

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

&emsp;&emsp;检测数据中是否存在`NaN`，如果存在，就返回`True`：

``` python
print(np.any(df.isnull()) == True)  # 结果为True
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

&emsp;&emsp;使用`get_dummies`进行`one-hot`编码：

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

### Categorical

&emsp;&emsp;该函数用于创建分组：

``` python
import pandas as pd

my_categories = pd.Categorical(['foo', 'bar', 'baz', 'foo', 'bar'])
print(my_categories)
categories = ['foo', 'bar', 'baz']
codes = [0, 1, 2, 0, 0, 1]
my_cats_2 = pd.Categorical.from_codes(codes, categories, ordered=True)
print(my_cats_2)
```

执行结果：

``` python
['foo', 'bar', 'baz', 'foo', 'bar']
Categories (3, object): ['bar', 'baz', 'foo']
['foo', 'bar', 'baz', 'foo', 'foo', 'bar']
Categories (3, object): ['foo' < 'bar' < 'baz']
```

### head

&emsp;&emsp;函数原型如下：

``` python
DataFrame.head(n=5)
```

该函数用于返回前`n`行数据：

``` python
import pandas as pd

df = pd.DataFrame({
    'animal': [
        'alligator', 'bee', 'falcon', 'lion',
        'monkey', 'parrot', 'shark', 'whale', 'zebra'
    ]
})

print(df.head(n=3))
```

执行结果：

``` python
      animal
0  alligator
1        bee
2     falcon
```

### factorize

&emsp;&emsp;`factorize`用于将数据映射为一组数字：

``` python
import pandas as pd

labels, uniques = pd.factorize(['b', 'b', 'a', 'c', 'b'])
print(labels)  # 输出“[0 0 1 2 0]”
print(uniques)  # 输出“['b' 'a' 'c']”
```

### crosstab

&emsp;&emsp;`crosstab`用于制作交叉表：

``` python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    '类别': ['水果', '水果', '水果', '蔬菜', '蔬菜', '肉类', '肉类'],
    '产地': ['美国', '中国', '中国', '中国', '新西兰', '新西兰', '美国'],
    '水果': ['苹果', '梨', '草莓', '番茄', '黄瓜', '羊肉', '牛肉'],
})

print(df)
print("-------------------")
print(pd.crosstab(df['类别'], df['产地']))
```

执行结果：

``` python
   类别   产地  水果
0  水果   美国  苹果
1  水果   中国   梨
2  水果   中国  草莓
3  蔬菜   中国  番茄
4  蔬菜  新西兰  黄瓜
5  肉类  新西兰  羊肉
6  肉类   美国  牛肉
-------------------
产地  中国  新西兰  美国
类别
水果   2    0   1
肉类   0    1   1
蔬菜   1    1   0
```