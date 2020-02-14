---
title: Qt设置字体和更改字体颜色
categories: Qt语法详解
abbrlink: 23b15004
date: 2019-01-02 11:05:22
---
&emsp;&emsp;对文本框`lineEdit`设置字体，首先建立一个按钮`setFontButton`，并定义了它的槽函数`setFont`，将`setFontButton`添加到布局中，通过`connect`与`setFont`关联。定义的`setFont`如下：<!--more-->

``` cpp
void FindDialog::setFont() {
    bool ok;
    const QFont &font = QFontDialog::getFont ( &ok, lineEdit->font(), this, tr ( "fontDialog" ) );
​
    if ( ok ) {
        lineEdit->setFont ( font );
    }
}
```

这样就可以设置字体了。对于改变字体或按钮的颜色，先加入色板类`Qpalette`，例如要设置`lineEdit`里字体的颜色为红色：

``` cpp
QPalette pal = lineEdit->palette();
pal->setColor ( QPalette::Text, QColor ( 255, 0, 0 );
lintEdit->setPalette ( pal );
```

如果要通过调用色板来选择字体颜色，可以添加下面的代码，其中的按钮创建、连接等步骤省略：

``` cpp
void FindDialog::setColor {
    QPalette palette = lineEdit->palette();
    const QColor &color = QColorDialog::getColor ( palette.color ( QPalette::Base ), this );
​
    if ( color.isValid() ) {
        palette.setColor ( QPalette::Text, color );
        lineEdit->setPalette ( palette );
    }
}
```