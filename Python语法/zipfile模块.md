---
title: zipfile模块
categories: Python语法
date: 2018-12-27 18:27:46
---
&emsp;&emsp;`zipfile`模块用于压缩与解压文件。<!--more-->

### 压缩文件

&emsp;&emsp;代码实例：

``` python
import zipfile

# 将data.h5和myh5py.hdf5压缩至文件test.zip
with zipfile.ZipFile('test.zip', mode='w') as zipf:
    zipf.write('data.h5')
    zipf.write('myh5py.hdf5')

zipf = zipfile.ZipFile('test.zip')
print(zipf.namelist())
```

### 解压文件

&emsp;&emsp;代码实例：

``` python
import zipfile

zipf = zipfile.ZipFile('test.zip')
zipf.extractall('./channel1')  # 将所有文件解压到channel1目录下
```