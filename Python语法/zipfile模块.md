---
title: zipfile模块
categories: Python语法
abbrlink: c6c57f67
date: 2018-12-27 18:27:46
---
&emsp;&emsp;`zipfile`模块里有两个非常重要的`class`，分别是`ZipFile`和`ZipInfo`。`ZipFile`是主要的类，用来创建和读取`zip`文件，而`ZipInfo`是存储的`zip`文件的每个文件的信息的。<!--more-->
&emsp;&emsp;`zipFile`的原型为：

``` python
class zipfile.ZipFile(file [, mode [, compression [, allowZip64]]])
```

- `file`：`zip`文件名(包含路径)。
- `mode`：`r`(解压)、`w`(压缩)和`a`(追加压缩，不会清空原来的`zip`)。
- `compression`：`zipfile.ZIP_STORED`(不压缩)、`zipfile.ZIP_DEFLATED`(压缩)。
- `allowZip64`：为`True`时，表示支持`64`位的压缩，一般而言，在所压缩的文件大于`2G`时，会用到这个选项(默认情况下，该值为`False`，因为`Unix`系统不支持)。

&emsp;&emsp;`ZipFile.namelist`：获取`zip`文档内所有文件的名称列表(名称是字符串)。
&emsp;&emsp;`ZipFile.getinfo(name)`：获取`zip`文档内指定文件的信息。返回一个`zipfile.ZipInfo`对象，它包括文件的详细信息。
&emsp;&emsp;`ZipFile.infolist`：获取`zip`文档内所有文件的信息，返回一个`zipfile.ZipInfo`的列表。
&emsp;&emsp;`ZipFile.extract(member [, path [, pwd]])`：将`zip`文档内的指定文件解压到指定目录。参数`member`指定要解压的文件名称(字符串)或对应的`ZipInfo`对象，`path`指定了解压文件保存的文件夹，`pwd`为解压密码。
&emsp;&emsp;`ZipFile.extractall([path [, members [, pwd]]])`：解压`zip`文档中的所有文件到指定目录。参数`members`的默认值为`zip`文档内的所有文件名称列表，也可以自己设置，选择要解压的文件名称。
&emsp;&emsp;`ZipFile.printdir`：将`zip`文档内的信息打印到控制台上，包含三列信息，即`File Name`、`Modified`和`Size`。
&emsp;&emsp;`ZipFile.read(name [, pwd])`：获取`zip`文档内指定文件的二进制数据。
&emsp;&emsp;`ZipFile.write(filename [, arcname [, compress_type]])`：将指定文件添加到`zip`文档中。参数`filename`为文件路径；`arcname`为添加到`zip`文档之后保存的名称；`compress_type`表示压缩方法，它的值可以是`zipfile.ZIP_STORED`或`zipfile.ZIP_DEFLATED`。
&emsp;&emsp;`zipInfo`的属性如下：

- `ZipInfo.compress_type`：压缩类型。
- `ZipInfo.comment`：文档说明。
- `ZipInfo.extr`：扩展项数据。
- `ZipInfo.create_system`：获取创建该`zip`文档的系统。
- `ZipInfo.create_version`：获取创建`zip`文档的`PKZIP`版本。
- `ZipInfo.extract_version`：获取解压`zip`文档所需的`PKZIP`版本。
- `ZipInfo.reserved`：预留字段，当前实现总是返回`0`。
- `ZipInfo.flag_bits`：`zip`标志位。
- `ZipInfo.volume`：文件头的卷标。
- `ZipInfo.internal_attr`：内部属性。
- `ZipInfo.external_attr`：外部属性。
- `ZipInfo.header_offset`：文件头偏移位。
- `ZipInfo.CRC`：未压缩文件的`CRC-32`。
- `ZipInfo.compress_size`：获取压缩后的大小。
- `ZipInfo.file_size`：获取未压缩的文件大小。

&emsp;&emsp;压缩文件的代码如下：

``` python
import zipfile
​
# 将data.h5和myh5py.hdf5压缩至文件test.zip
with zipfile.ZipFile('test.zip', mode='w') as zipf:
    zipf.write('data.h5')
    zipf.write('myh5py.hdf5')
​
zipf = zipfile.ZipFile('test.zip')
print(zipf.namelist())
```

&emsp;&emsp;解压文件的代码如下：

``` python
import zipfile
​
zipf = zipfile.ZipFile('test.zip')
zipf.extractall('./channel1')  # 将所有文件解压到channel1目录下
```