---
title: Qt之QFileDialog
date: 2019-01-24 14:05:36
categories: Qt语法详解
---
&emsp;&emsp;`QFileDialog`是`Qt`中的文件对话框，以下资料来源于`Qt`官方文档。使用QFileDialog可以调用当前系统的文件对话框，需要包含头文件`QFileDialog`。最简单的方法是调用静态函数来对话框获取文件：

``` cpp
QString file = QFileDialog::getOpenFileName ( "/home/foxman", "Images (*.png *.xpm *.jpg)", this );
```

这段代码可以建立一个取文件对话框，选择`OK`后将文件路径返回给`file`。可以一次性打开多个文件，使用`QStringList`来保存打开的文件的路径，例如打开一些音乐文件：

``` cpp
QStringList files = QFileDialog::getOpenFileNames (
    this, tr ( "Select Music Files" ),
    QDesktopServices::storageLocation ( QDesktopServices::MusicLocation ) );
```

&emsp;&emsp;常见的用法如下所示：

``` cpp
/* 指定父窗口、设置对话框标题、指定默认打开的目录路径、设置文件类型过滤器 */
QStringList fileNames = QFileDialog::getOpenFileNames ( this, tr ( "文件对话框" ), \
                                                        "F:", tr ( "图片文件(*png *jpg)" ) );
qDebug() << "fileNames:" << fileNames;
```

&emsp;&emsp;一般的文件对话框的使用：

``` cpp
QFileDialog *fd = new QFileDialog ( this, "file dlg", TRUE );
​
if ( fd->exec() == QFileDialog::Accepted ) { /* ok */
    QString file = fd->selectedFile();
    qWarning ( s );
}
```

&emsp;&emsp;设定选项如下：
&emsp;&emsp;1. 设定显示模式：

``` cpp
/* Detail显示文件详细的日期大小，List为一般情况 */
fd->setViewMode ( QFileDialog::Detail );
```

&emsp;&emsp;2. 设定过滤器：

``` cpp
fd->setFilter ( "Images (*.png *.xpm *.jpg)" );
```

下面是设定多个过滤器，一定要以`;;`隔开：

``` cpp
QString filters = "C file(*.c *.cpp *.h);;pic(*.png *.xpm)";
fd->setFilters ( filters );
```

设定对话框返回值类型：

``` cpp
fd->setMode ( QFileDialog::ExistingFile );
```

- `AnyFile`：一般用于`save as`对话框。
- `ExistingFile`：存在的一个文件。
- `ExistingFiles`：存在的`0`个或多个文件(可用于选择多个文件)。
- `Directory`：返回目录。
- `DirectoryOnly`：返回目录(选取文件的时候只选中目录)。

&emsp;&emsp;读取`QFileDialog`返回值：

- 返回选择中的一个文件(夹)名字：

``` cpp
QString s = fd->selectedFile();
```

- 选取多个文件(一定要设定`ExistingFiles`模式)：

``` cpp
QStringList slist = fd->selectedFiles();
​
for ( QStringList::Iterator it = slist.begin(); it != slist.end(); it++ ) {
    qWarning ( *it );
}
```

---

### 文件打开对话框

``` cpp
QString getOpenFileName ( QWidget *parent = 0, const QString &caption = QString(), \
                          const QString &dir = QString(), const QString &filter = QString(), \
                          QString *selectedFilter = 0, Options options = 0 );
```

- `parent`：用于指定父组件。注意，很多`Qt`组件的构造函数都会有这么一个parent参数，并提供一个默认值0。
- `caption`：对话框的标题。
- `dir`：对话框显示时默认打开的目录，`.`代表程序运行目录，`/`代表当前盘符的根目录。也可以是平台相关的目录，例如`C:\\`。
- `filter`：对话框的后缀名过滤器。多个文件使用空格分隔，例如使用`Image Files(*.jpg *.png)`就让它只能显示后缀名是`jpg`或者`png`的文件；多个过滤使用两个分号分隔，如果需要使用多个过滤器，使用`;;`分割，例如`JPEG Files(*.jpg);;PNG Files(*.png)`。
- `selectedFilter`：默认选择的过滤器。
- `options`：对话框的一些参数设定，例如只显示文件夹等，它的取值是`enum QFileDialog::Option`，每个选项可以使用`|`运算组合起来。

### 文件保存对话框

``` cpp
QString getSaveFileName ( QWidget *parent = 0, const QString &caption = QString(), \
                          const QString &dir = QString(), const QString &filter = QString(), \
                          QString *selectedFilter = 0, Options options = 0 );
```

代码如下：

``` cpp
QString fileName = QFileDialog::getSaveFileName ( this, tr ( "Open Config" ), \
                                                  "", tr ( "Config Files (*.ifg)" ) );
​
if ( !fileName.isNull() ) {
}
else { /* 点击取消按钮 */
}
```