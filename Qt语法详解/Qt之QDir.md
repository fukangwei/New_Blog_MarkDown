---
title: Qt之QDir
categories: Qt语法详解
abbrlink: a93012ee
date: 2019-01-24 19:36:49
---
&emsp;&emsp;类`QDir`提供了对目录结构和其内容的访问方式。`QDir`用来操作路径名、访问关于路径和文件的信息、操作基础的文件系统，还可以用来访问`Qt`的资源系统。
&emsp;&emsp;`Qt`可以使用相对路径和绝对路径指向一个文件，`isRelative`和`isAbsolute`函数可以判断`QDir`对象使用的是相对路径还是绝对路径。将相对路径转换为绝对路径使用`makeAbsolute`函数。目录路径可以通过`path`函数返回，通过`setPath`函数设置新路径，绝对路径使用`absolutePath`返回。目录名可以使用`dirName`返回。目录的路径可以通过`cd`、`cdUp`改变，可以使`mkdir`创建目录，`rename`改变目录名。判断目录是否存在可以使用`exists`，目录的属性可以使用`isReadable`、`isAbsolute`、`isRelative`和`isRoot`。目录下有很多条目，包括文件、目录和符号链接，总的条目数可以使用`count`来统计。`entryList`可以返回目录下所有条目组成的字符串链表，文件可以使用`remove`函数删除，`rmdir`删除目录。在这里简单说一下几个类似的方法的区别：

- `entryInfoList`与`entryList`：第一个函数会返回此文件加下所有文件及目录的完整信息，包括用户组、大小、访问时间、权限等等所有与文件有关的信息；而第二个方法只是返回此目录下的所有文件及目录的名字。
- `absoluteFilePath`与`absolutePath`：`absoluteFilePath`返回带本文件名的路径信息，`absolutePath`返回不带本文件名的路径信息。

&emsp;&emsp;`Qt`使用`/`来作为通用的目录分隔符，这一点和在`URLs`中的路径分割符的用法一致。如果你使用`/`作为文件分隔符，`Qt`会自动地转换你的路径来匹配你的基础的操作系统。
&emsp;&emsp;绝对路径的用法：

``` cpp
QDir ( "/home/user/Documents" );
QDir ( "C:/Documents and Settings" );
```

&emsp;&emsp;相对路径的用法：

``` cpp
QDir ( "images/landscape.png" );
```

&emsp;&emsp;使用示例如下：

``` cpp
#include <QtCore>
#include <QDebug>
​
int main ( int argc, char *argv[] ) {
    QCoreApplication a ( argc, argv );
    QDir mDir ( "D:/qttest" ); /* 或者用“D:\\qttest”来代替 */
    QDir nDir;
    qDebug() << mDir.exists(); /* 测试路径是否存在 */
    /* 返回指定目录下指定文件的绝对路径 */
    qDebug() << mDir.absoluteFilePath ( "main.cpp" );
    qDebug() << mDir.dirName(); /* 剥离掉路径，只返回目录的名字 */
    QFileInfo fi ( "C:/Documents and Settings/Administrator/pcmscan.cfg" );
    qDebug() << fi.absoluteFilePath(); /* 返回文件的绝对路径 */
    qDebug() << fi.filePath(); /* 返回文件的路径 */
    qDebug() << fi.fileName(); /* 剥离掉路径，只返回文件的名字 */
​
    /* driver返回系统根目录下的目录列表 */
    foreach ( QFileInfo mItem, nDir.drives() ) {
        qDebug() << mItem.absolutePath();
    }
​
    /* entryInfoList根据名字或属性顺序返回指定目录下所有的文件和目录的QFileInfo对象 */
    foreach ( QFileInfo nItem, nDir.entryInfoList() ) {
        qDebug() << nItem.absoluteFilePath();
    }
​
    QString mPath = "D:/test/ZZZ";
    QDir kDir;
​
    if ( !kDir.exists ( mPath ) ) { /* 判断指定目录下是否存在指定目录 */
        kDir.mkpath ( mPath ); /* 生成指定目录 */
        qDebug() << "Create";
    } else {
        qDebug() << "Already exits";
    }
​
    return a.exec();
}
```

执行结果：

``` cpp
true
"D:/qttest/main.cpp"
"qttest"
"C:/Documents and Settings/Administrator/pcmscan.cfg"
"C:/Documents and Settings/Administrator/pcmscan.cfg"
"pcmscan.cfg"
"C:/"
"D:/"
"E:/"
"F:/"
"G:/"
"H:/"
"D:/qttest/QtDir-build-desktop"
"D:/qttest"
"D:/qttest/QtDir-build-desktop/debug"
"D:/qttest/QtDir-build-desktop/Makefile"
"D:/qttest/QtDir-build-desktop/Makefile.Debug"
"D:/qttest/QtDir-build-desktop/Makefile.Release"
"D:/qttest/QtDir-build-desktop/release"
Already exits
```

---

- `QString QDir::homePath() [static]`: Returns the absolute path of the user's home directory. Under `Windows`, this function will return the directory of the current user's profile. Typically, this is:

``` cpp
C:/Documents and Settings/Username
```

Use the `toNativeSeparators()` function to convert the separators to the ones that are appropriate for the underlying operating system.
&emsp;&emsp;If the directory of the current user's profile does not exist or cannot be retrieved, the following alternatives will be checked (in the given order) until an existing and available path is found:

- The path specified by the `USERPROFILE` environment variable.
- The path formed by concatenating the `HOMEDRIVE` and `HOMEPATH` environment variables.
- The path specified by the `HOME` environment variable.
- The path returned by the `rootPath()` function (which uses the `SystemDrive` environment variable)
- The `C:/` directory.

Under `non-Windows` operating systems the `HOME` environment variable is used if it exists, otherwise the path returned by the `rootPath()`.

- `QString QDir::rootPath() [static]`: Returns the absolute path of the root directory. For `Unix` operating systems, this returns `/`. For `Windows`, this normally returns `c:/`. I.E. the root of the system drive.
- `QString QDir::tempPath()`: Returns the absolute path of the system's temporary directory. On `Unix/Linux` systems, this is the path in the `TMPDIR` environment variable or `/tmp` if `TMPDIR` is not defined. On `Windows`, this is usually the path in the `TEMP` or `TMP` environment variable. Whether a directory separator is added to the end or not, depends on the operating system.