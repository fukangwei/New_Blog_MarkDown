---
title: Qt之QColorDialog
categories: Qt语法详解
abbrlink: bb606c52
date: 2019-01-02 18:26:25
---
&emsp;&emsp;这是`Qt`提供的颜色选择对话框，使用头文件`QColorDialog`。`Qt`提供了`getColor`函数，可以直接获得选择的颜色：

``` cpp
QColor color = QColorDialog::getColor ( Qt::white, this );
QString msg = QString ( "r: %1, g: %2, b: %3" ).arg (
    QString::number ( color.red() ),
    QString::number ( color.green() ),
    QString::number ( color.blue() ) );
QMessageBox::information ( NULL, "Selected color", msg );
```

&emsp;&emsp;`QColorDialog`的`getColor`函数有两个参数，第一个是`QColor`类型，是对话框打开时默认选择的颜色；第二个是它的`parent`。代码第二行是把`QColor`的`R`、`G`、`B`三个值转换为字符串；最后一行代码使用消息对话框把拼接的字符串输出。
&emsp;&emsp;`getColor`还有一个重载函数：

``` cpp
QColorDialog::getColor (
    const QColor &initial, QWidget *parent,
    const QString &title, ColorDialogOptions options = 0 );
```

- `initial`：对话框打开时的默认选中的颜色。
- `parent`：设置对话框的父组件。
- `title`：设置对话框的`title`。
- `options`：它是`QColorDialog::ColorDialogOptions`类型，可以设置对话框的一些属性，例如是否显示`Alpha`值。