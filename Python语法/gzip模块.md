---
title: gzip模块
categories: Python语法
date: 2019-02-09 15:04:04
---
&emsp;&emsp;`Gzip`模块为`python`的压缩和解压缩模块。<!--more-->
&emsp;&emsp;创建`gz`压缩文件的方法如下：

``` python
import gzip
import shutil

with open('test.csv', 'rb') as f_in:
    with gzip.open('test.csv.gz', 'wb') as f_out:
        shutil.copyfileobj(f_in, f_out)
```

&emsp;&emsp;压缩与解压缩数据的方法如下：

``` python
import gzip

test_data = b'gzip test data'
gzip_data = gzip.compress(test_data)  # 压缩数据
ungz_data = gzip.decompress(gzip_data)  # 解压缩数据
print(gzip_data)
print(ungz_data)
```

执行结果：

``` python
b'\x1f\x8b\x08\x00\xbez\xe3`\x02\xffK\xaf\xca,P(I-.QHI,I\x04\x00*\xfb\x85\x89\x0e\x00\x00\x00'
b'gzip test data'
```