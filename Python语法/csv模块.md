---
title: csv模块
categories: Python语法
date: 2019-02-24 13:21:56
---
&emsp;&emsp;有以下表格：<!--more-->

年     | 制造商   | 型号                                     | 说明            | 价值
-------|---------|------------------------------------------|-----------------|----
`1997` | `Ford`  | `E350`                                   | `ac, abs, moon` | `3000.00`
`1999` | `Chevy` | `Venture "Extended Edition"`             |                 | `4900.00`
`1999` | `Chevy` | `Venture "Extended Edition, Very Large"` |                 | `5000.00`
`1996` | `Jeep`  | `Grand Cherokee`                         | `MUST SELL!`    | `4799.00`

上面表格内容若以`CSV`格式表示，就会像下列：

``` cpp
年,制造商,型号,说明,价值
1997,Ford,E350,"ac, abs, moon",3000.00
1999,Chevy,"Venture ""Extended Edition""","",4900.00
1999,Chevy,"Venture ""Extended Edition, Very Large""","",5000.00
1996,Jeep,Grand Cherokee,"MUST SELL!",4799.00
```

&emsp;&emsp;读取`csv`文件的所有内容：

``` python
import csv

with open('some.csv', newline='') as f:
    reader = csv.reader(f)

    for row in reader:
        print(row)
```

执行结果：

``` cpp
['年', '制造商', '型号', '说明', '价值']
['1997', 'Ford', 'E350', 'ac, abs, moon', '3000']
['1999', 'Chevy', 'Venture "Extended Edition"', '', '4900']
['1999', 'Chevy', 'Venture "Extended Edition, Very Large"', '', '5000']
['1996', 'Jeep', 'Grand Cherokee', 'MUST SELL!', '4799']
```

&emsp;&emsp;读取`csv`文件的特定列：

``` cpp
import csv

with open('test.csv', newline='') as csvfile:
    reader = csv.DictReader(csvfile)
    for row in reader:
        print(row['年'], row['价值'])
```

&emsp;&emsp;写入`csv`文件：

``` cpp
import csv

with open('names.csv', 'w', newline='') as csvfile:
    fieldnames = ['first_name', 'last_name']
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
    writer.writeheader()
    writer.writerow({'first_name': 'Baked', 'last_name': 'Beans'})
    writer.writerow({'first_name': 'Lovely', 'last_name': 'Spam'})
    writer.writerow({'first_name': 'Wonderful', 'last_name': 'Spam'})
```