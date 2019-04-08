---
title: Qt之QAction
date: 2019-01-02 12:51:57
categories: Qt语法详解
---
&emsp;&emsp;`QAction::QAction ( const QString &text, QObject *parent )`：`QAction`类的构造函数之一，利用`text`、`parent`创建`QAction`对象。`QAction`对象一般为菜单中的菜单项，比如`文件`菜单中`新建`选项就是一个`QAction`对象，上述构造函数中`text`成员变量即为菜单项所表示的内容：

``` cpp
QAction *newAction = new QAction ( tr ( "&New" ), this );
```

&emsp;&emsp;`void QAction::setIcon ( const QIcon & icon )`：该函数可设置菜单项名称前的图标：

``` cpp
newAction->setIcon ( QIcon ( ":/images/new.png" ) );
```

&emsp;&emsp;`void QAction::setShortcut ( const QKeySequence & shortcut )`：设置`QAction`对象执行的快捷键：

``` cpp
newAction->setShortcut ( tr ( "Ctrl+N" ) );
```

&emsp;&emsp;`void QAction::setStatusTip ( const QString & statusTip )`：设置当鼠标移动到`动作`上时，状态栏显示的提示语。
&emsp;&emsp;`void QAction::setVisible ( bool )`：设置`动作`显示与否，当形参为`true`时，`动作`显示。
&emsp;&emsp;`void QAction::triggered ( bool checked = false ) [signal]`：此函数为信号，当用户触发此`动作`时，此信号发射，例如用户点击了菜单中的选项等。此函数的一般用法：在`QObject::connect`函数中作为信号参数，用于触发`动作`所对应执行的槽函数，即可实现`动作`的功能函数。例如`新建`按钮被用户按下，所需要的功能可能是新建一个文档，那么新建一个文档的动作就在这个槽函数中实现。至于此信号函数中的形参，暂时可以忽略。
&emsp;&emsp;`void QAction::setCheckable ( bool )`：此函数用于设置`QAction`类中的私有变量`bool checkable`，此属性用于提供`动作`是否为复选动作，例如`Qt Creator`中`控件`菜单的`全屏`菜单项即为复选动作菜单。