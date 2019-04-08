---
title: QT模态与非模态对话框
date: 2019-01-02 13:49:42
categories: Qt语法详解
---
&emsp;&emsp;模态对话框(`Modal Dialog`)与非模态对话框(`Modeless Dialog`)的概念不是`Qt`所独有的，在各种不同的平台下都存在。
&emsp;&emsp;所谓的`模态对话框`，就是在其没有被关闭之前，用户不能与同一个应用程序的其他窗口进行交互，直到该对话框关闭；对于`非模态对话框`，当被打开时，用户既可选择和该对话框进行交互，也可以选择同应用程序的其他窗口交互。
&emsp;&emsp;在`Qt`中，显示一个对话框一般有两种方式，一种是使用`exec`方法，它总是以模态来显示对话框；另一种是使用`show`方法，它使得对话框既可以模态显示，也可以非模态显示，决定它是模态还是非模态的是对话框的`modal`属性。`modal`属性的定义如下所示：

``` cpp
modal : bool
```

默认情况下，对话框的该属性值是`false`，通过`show`方法显示的对话框就是非模态的。而如果将该属性值设置为`true`，就设置成了模态对话框，其作用相当于把`QWidget::windowModality`属性设置为`Qt::ApplicationModal`。而使用`exec`方法显示对话框的话，将忽略`modal`属性值的设置，并把对话框设置为模态对话框。
&emsp;&emsp;一般使用`setModal`方法来设置对话框的`modal`属性。如果要设置为模态对话框，最简单的就是使用`exec`方法：

``` cpp
MyDialog myDlg;
myDlg.exec();
```

也可以使用`show`方法：

``` cpp
MyDialog myDlg;
myDlg.setModal ( true );
myDlg.show();
```

如果要设置为非模态对话框，必须使用`show`方法：

``` cpp
MyDialog myDlg;
myDlg.setModal ( false );
myDlg.show();
```

&emsp;&emsp;有时需要一个对话框以非模态的形式显示，但又需要它总在所有窗口的最前面，可以通过如下代码设置：

``` cpp
MyDialog myDlg;
myDlg.setModal ( false );
myDlg.show();
​
/* 关键是这一行 */
myDlg.setWindowFlags ( Qt::WindowStaysOnTopHint );
```