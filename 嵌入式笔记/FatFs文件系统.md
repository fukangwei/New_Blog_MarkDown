---
title: FatFs文件系统
categories: 嵌入式笔记
date: 2019-02-24 21:10:05
---
&emsp;&emsp;`FatFS`是一个为小型嵌入式系统设计的通用`FAT`文件系统。<!--more-->

### f_mount

&emsp;&emsp;在`FatFs`模块上注册或注销一个工作区：

``` cpp
FRESULT f_mount ( BYTE Drive, FATFS *FileSystemObject );
```

- `Drive`为工作区的逻辑驱动器号，范围是`0`至`9`。
- `FileSystemObject`为工作区指针。

``` cpp
FATFS fs;
f_mount ( 0, &fs ); /* 注册工作区 */
f_mount ( 0, NULL ); /* 注销工作区 */
```

### f_open

&emsp;&emsp;创建或打开一个用于访问文件的文件对象：

``` cpp
FRESULT f_open ( FIL *FileObject, const XCHAR *FileName, BYTE ModeFlags );
```

- `FileObject`为将被创建的文件对象结构的指针。
- `FileName`指定了将被创建或打开的文件名。
- `ModeFlags`指定文件的访问类型和打开方法，它有以下选项：

模式               | 描述                                    | 模式               | 描述
-------------------|----------------------------------------|--------------------|-----
`FA_READ`          | 指定读访问对象，可以从文件中读取数据      | `FA_WRITE`         | 指定写访问对象，可以向文件中写入数据
`FA_OPEN_EXISTING` | 打开文件，如果文件不存在，则打开失败      | `FA_OPEN_ALWAYS`   | 如果文件存在，则打开；否则，创建一个新文件
`FA_CREATE_NEW`    | 创建一个新文件，如果文件已存在，则创建失败 | `FA_CREATE_ALWAYS` | 创建一个新文件，如果文件已存在，则它将被截断并覆盖

### f_close

&emsp;&emsp;关闭一个打开的文件：

``` cpp
FRESULT f_close ( FIL *FileObject );
```

- `FileObject`为将被关闭的文件对象结构指针。

### f_read

&emsp;&emsp;从一个文件读取数据：

``` cpp
FRESULT f_read ( FIL *FileObject, void *Buffer, UINT ByteToRead, UINT *ByteRead );
```

- `FileObject`为将被读取的文件对象结构指针。
- `Buffer`为存储读取数据的缓冲区指针。
- `ByteToRead`为要读取的字节数。
- `ByteRead`为返回已读取字节数变量的指针。

&emsp;&emsp;函数执行成功后，如果`*ByteRead < ByteToRead`，则读写指针到达了文件结束位置。

### f_write

&emsp;&emsp;写入数据到一个文件：

``` cpp
FRESULT f_write ( FIL *FileObject, const void *Buffer, UINT ByteToWrite, UINT *ByteWritten );
```

- `FileObject`为将被写入的文件对象结构指针。
- `Buffer`为存储写入数据的缓冲区的指针。
- `ByteToRead`为要写入的字节数。
- `ByteRead`为返回已写入字节数变量的指针。

&emsp;&emsp;函数执行成功后，如果`*ByteWritten < *ByteToWritten`，则意味着该卷已满。

### f_lseek

&emsp;&emsp;移动文件读写指针：

``` cpp
FRESULT f_lseek ( FIL *FileObject, DWORD Offset );
```

- `FileObject`是打开的文件对象的指针。
- `Offset`是相对于文件起始处的字节数。

### f_truncate

&emsp;&emsp;截断文件大小：

``` cpp
FRESULT f_truncate ( FIL *FileObject );
```

- `FileObject`为待截断的文件对象的指针。

&emsp;&emsp;以下程序运行后，文件大小变成了`60`字节：

``` cpp
res = f_open ( &fsrc, "news/news.txt", FA_WRITE );
res = f_lseek ( &fsrc, 60 ); /* 指针指向第60个字节 */
res = f_truncate ( &fsrc );  /* 将文件在此截断     */
res = f_sync ( &fsrc );      /* 关闭文件           */
```

### f_sync

&emsp;&emsp;冲洗一个文件的缓存信息：

``` cpp
FRESULT f_sync ( FIL *FileObject );
```

- `FileObject`为待冲洗的文件对象的指针。

### f_opendir

&emsp;&emsp;打开一个目录：

``` cpp
FRESULT f_opendir ( DIR *DirObject, const XCHAR *DirName );
```

- `DirObject`为待创建的空白目录对象的指针。
- `DirName`指定了将被打开的目录名。

### f_readdir

&emsp;&emsp;读取目录项：

``` cpp
FRESULT f_readdir ( DIR *DirObject, FILINFO *FileInfo );
```

- `DirObject`为打开的目录对象的指针。
- `FileInfo`为文件信息结构指针。

### f_stat

&emsp;&emsp;获取文件状态：

``` cpp
FRESULT f_stat ( const XCHAR *FileName, FILINFO *FileInfo );
```

- `FileName`指定了待获取其信息的文件或目录。
- `FileInfo`为文件信息结构指针。

### f_mkdir

&emsp;&emsp;创建一个目录：

``` cpp
FRESULT f_mkdir (
    const XCHAR *DirName /* 目录名的指针 */
);
```

- `DirName`指定了待创建的目录名。

### f_unlink

&emsp;&emsp;移除一个对象：

``` cpp
FRESULT f_unlink ( const XCHAR *FileName );
```

- `FileName`指定了一个待移除的对象。

### f_chmod

&emsp;&emsp;修改一个文件或目录的属性：

``` cpp
FRESULT f_chmod ( const XCHAR *FileName, BYTE Attribute, BYTE AttributeMask );
```

- `FileName`指定了一个待被修改属性的文件或目录。
- `Attribute`是待设置的属性标志，可以是下列标志的一个或任意组合：

属性     | 描述  | 属性     | 描述
---------|------|----------|-----
`AM_RDO` | 只读 | `AM_ARC` | 存档
`AM_SYS` | 系统 | `AM_HID` | 隐藏

### f_utime

&emsp;&emsp;修改一个文件或目录的时间戳：

``` cpp
FRESULT f_utime ( const XCHAR *FileName, const FILINFO *TimeDate );
```

- `FileName`指定了一个待修改时间戳的文件或目录。
- `TimeDate`是文件信息结构指针，其中成员`ftime`和`fdata`存储了一个待被设置的的时间戳。

### f_rename

&emsp;&emsp;重命名一个对象：

``` cpp
FRESULT f_rename ( const XCHAR *OldName, const XCHAR *NewName );
```

- `OldName`指定了待被重命名的原对象名。
- `NewName`指定了重命名后的新对象名，不能包含驱动器号。

``` cpp
f_rename ( "oldname.txt", "newname.txt" ); /* 重命名一个对象 */
f_rename ( "oldname.txt", "dir1/newname.txt" ); /* 重命名并且移动一个对象到另一个目录 */
```

### f_chdir

&emsp;&emsp;改变一个驱动器的当前目录：

``` cpp
FRESULT f_chdir ( const XCHAR *Path );
```

- `Path`指定了将要进去的目录。

### f_chdrive

&emsp;&emsp;改变当前驱动器：

``` cpp
FRESULT f_chdrive ( BYTE Drive );
```

- `Drive`指定将被设置为当前驱动器的逻辑驱动器号。

### f_gets

&emsp;&emsp;从文件中读取一个字符串：

``` cpp
char *f_gets ( char *Str, int Size, FIL *FileObject );
```

- `Str`为读取字符串的读缓冲区指针。
- `Size`为读缓冲区大小。
- `FileObject`为打开的文件对象结构指针。

当函数执行成功后，`Str`将被返回。

### f_putc

&emsp;&emsp;向文件中写入一个字符：

``` cpp
int f_putc ( int Chr, FIL *FileObject );
```

- `Chr`为待写入的字符。
- `FileObject`为打开的文件对象结构的指针。

当字符被成功地写入后，函数返回该字符。

### f_puts

&emsp;&emsp;向文件中写入一个字符串：

``` cpp
int f_puts ( const char *Str, FIL *FileObject );
```

- `Str`为待写入的字符串的指针，`\0`字符不会被写入。
- `FileObject`为打开的文件对象结构的指针。

函数执行成功后，将返回写入的字符数。

### f_printf

&emsp;&emsp;向文件中写入一个格式化字符串：

``` cpp
int f_printf ( FIL *FileObject, const char *Foramt, ... );
```

- `FileObject`为已打开的文件对象结构的指针。
- `Format`是格式化字符串指针。
- `...`是可选参数。

函数执行成功后，将返回写入的字符数。