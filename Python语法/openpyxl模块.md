---
title: openpyxl模块
date: 2019-02-24 18:44:09
categories: Python语法
---
### 基本概念

&emsp;&emsp;在`openpyxl`中，主要用到三个概念：`Workbooks`、`Sheets`和`Cells`。`Workbook`就是一个`excel`工作表，`Sheet`是工作表中的一张表页，`Cell`就是简单的一个格。`openpyxl`就是围绕着这三个概念进行的：打开`Workbook`，定位`Sheet`，操作`Cell`。

### 读取xlsx

&emsp;&emsp;为了做实验，事先准备好了一个`excel`文档，里面有`Sheet1`、`Sheet2`和`Sheet3`这三个页，在`Sheet3`中填入了如下内容：

<img src="./openpyxl模块/1.png" height="198" width="247">

首先引入库：

``` python
from openpyxl import load_workbook
```

然后打开一个`xlsx`文件：

``` python
wb = load_workbook("template.xlsx")
```

可以看看打开的`excel`表里面有哪些`sheet`页：

``` python
print(wb.sheetnames)  # 执行结果为['Sheet1', 'Sheet2', 'Sheet3']
```

读取指定的`Sheet`页，打印其中的内容：

``` python
sheet = wb.get_sheet_by_name("Sheet3")
​
print(sheet["C"])  # (<Cell Sheet3.C1>, <Cell Sheet3.C2>, <Cell Sheet3.C3>, (省略) <- 第C列
print(sheet["4"])  # (<Cell Sheet3.A4>, <Cell Sheet3.B4>, <Cell Sheet3.C4>, (省略) <- 第4行
print(sheet["C4"].value)  # c4 <- 第C4格的值
print(sheet.max_row)  # 10 <- 最大行数
print(sheet.max_column)  # 5 <- 最大列数
​
for i in sheet["C"]:
    print(i.value, end=" ")  # c1 c2 c3 c4 c5 c6 c7 c8 c9 c10 <- C列中的所有值
```

### 写入xlsx

&emsp;&emsp;首先创建一个工作表：

``` python
from openpyxl import Workbook
wb = Workbook()
```

然后找到活动的`sheet`页：

``` python
sheet = wb.active
```

空的`excel`表默认的`sheet`页就叫做`Sheet`。如果想改名字，可以直接给`title`属性赋值(这个属性是可读可写的)：

``` python
sheet.title = "New Shit"
```

当然，这个只针对当前活动页，别的页的话，可以用`create_sheet`和`remove_sheet`进行添加和删除。
&emsp;&emsp;往`sheet`页里面写内容就比较简单了，和读取类似：

``` python
sheet['C3'] = 'Hello world!'
​
for i in range(10):
    sheet["A%d" % (i + 1)].value = i + 1
```

我们还可以进行其他操作，比如写公式：

``` python
sheet["E1"].value = "=SUM(A:A)"
```

最后记得保存：

``` python
wb.save('保存一个新的excel.xlsx')
```

<img src="./openpyxl模块/2.png" height="197" width="286">