---
title: Qt之QGridLayout
categories: Qt语法详解
abbrlink: 671a2fe
date: 2019-02-19 17:52:20
---
### 简述

&emsp;&emsp;`QGridLayout`是格栅布局，也被称作网格布局(多行多列)。栅格布局将位于其中的窗口部件放入一个网状的栅格之中。`QGridLayout`需要将提供给它的空间划分成的行和列，并把每个窗口部件插入并管理到正确的单元格。<!--more-->
&emsp;&emsp;栅格布局是这样工作的：它计算了位于其中的空间，然后将它们合理的划分成若干个行(`row`)和列(`column`)，并把每个由它管理的窗口部件放置在合适的单元之中，这里所指的单元(`cell`)即是指由行和列交叉所划分出来的空间。在栅格布局中，行和列本质上是相同的，只是叫法不同而已。下面将重点讨论列，这些内容当然也适用于行。
&emsp;&emsp;在栅格布局中，每个列(以及行)都有一个最小宽度(使用`setColumnMinimumWidth`设置)以及一个伸缩因子(使用`setColumnStretch`设置)。最小宽度指的是位于该列中的窗口部件的最小的宽度，而伸缩因子决定了该列内的窗口部件能够获得多少空间。

### 详细描述

&emsp;&emsp;一般情况下我们都是把某个窗口部件放进栅格布局的一个单元中，但窗口部件有时也可能会需要占用多个单元。这时就需要用到`addWidget`方法的一个重载版本：

``` cpp
void addWidget ( QWidget *, int row, int column, int rowSpan, int columnSpan, Qt::Alignment = 0 );
```

这个单元将从`row`和`column`开始，扩展到`rowSpan`和`columnSpan`指定的倍数的行和列。如果`rowSpan`或`columnSpan`的值为`-1`，则窗口部件将扩展到布局的底部或者右边边缘处。
&emsp;&emsp;在创建栅格布局完成后，就可以使用`addWidget`、`addItem`或者`addLayout`方法向其中加入窗口部件，以及其它的布局。

### 使用

&emsp;&emsp;下面以登录界面为例，来讲解如何使用`QGridLayout`：

<img src="./Qt之QGridLayout/1.png" height="152" width="255">

``` cpp
/* 构建控件，即头像、用户名、密码输入框等 */
QLabel *pImageLabel = new QLabel ( this );
QLineEdit *pUserLineEdit = new QLineEdit ( this );
QLineEdit *pPasswordLineEdit = new QLineEdit ( this );
QCheckBox *pRememberCheckBox = new QCheckBox ( this );
QCheckBox *pAutoLoginCheckBox = new QCheckBox ( this );
QPushButton *pLoginButton = new QPushButton ( this );
QPushButton *pRegisterButton = new QPushButton ( this );
QPushButton *pForgotButton = new QPushButton ( this );
​
pLoginButton->setFixedHeight ( 30 );
pUserLineEdit->setFixedWidth ( 200 );
​
/* 设置头像 */
QPixmap pixmap ( ":/Images/logo" );
pImageLabel->setFixedSize ( 90, 90 );
pImageLabel->setPixmap ( pixmap );
pImageLabel->setScaledContents ( true );
​
/* 设置文本 */
pUserLineEdit->setPlaceholderText ( QStringLiteral ( "QQ号码/手机/邮箱" ) );
pPasswordLineEdit->setPlaceholderText ( QStringLiteral ( "密码" ) );
pPasswordLineEdit->setEchoMode ( QLineEdit::Password );
pRememberCheckBox->setText ( QStringLiteral ( "记住密码" ) );
pAutoLoginCheckBox->setText ( QStringLiteral ( "自动登录" ) );
pLoginButton->setText ( QStringLiteral ( "登录" ) );
pRegisterButton->setText ( QStringLiteral ( "注册账号" ) );
pForgotButton->setText ( QStringLiteral ( "找回密码" ) );
​
QGridLayout *pLayout = new QGridLayout();
/* 头像从第0行第0列开始，占3行1列 */
pLayout->addWidget ( pImageLabel, 0, 0, 3, 1 );
/* 用户名输入框从第0行，第1列开始，占1行2列 */
pLayout->addWidget ( pUserLineEdit, 0, 1, 1, 2 );
pLayout->addWidget ( pRegisterButton, 0, 4 );
/* 密码输入框从第1行，第1列开始，占1行2列 */
pLayout->addWidget ( pPasswordLineEdit, 1, 1, 1, 2 );
pLayout->addWidget ( pForgotButton, 1, 4 );
/* 记住密码从第2行第1列开始，占1行1列，水平居左，垂直居中 */
pLayout->addWidget ( pRememberCheckBox, 2, 1, 1, 1, Qt::AlignLeft | Qt::AlignVCenter );
/* 自动登录从第2行第2列开始，占1行1列，水平居右，垂直居中 */
pLayout->addWidget ( pAutoLoginCheckBox, 2, 2, 1, 1, Qt::AlignRight | Qt::AlignVCenter );
pLayout->addWidget ( pLoginButton, 3, 1, 1, 2 ); /* 登录按钮从第3行第1列开始，占1行2列 */
pLayout->setHorizontalSpacing ( 10 ); /* 设置水平间距 */
pLayout->setVerticalSpacing ( 10 ); /* 设置垂直间距 */
pLayout->setContentsMargins ( 10, 10, 10, 10 ); /* 设置外间距 */
setLayout ( pLayout );
```

### 常用接口

- `addWidget ( QWidget *, int row, int column, Qt::Alignment = 0 );`
- `addWidget ( QWidget *, int row, int column, int rowSpan, int columnSpan, Qt::Alignment = 0 );`

添加窗口部件至布局。这个单元将从`row`和`column`开始，扩展到`rowSpan`和`columnSpan`指定的倍数的行和列。如果`rowSpan`或`columnSpan`的值为`-1`，则窗口部件将扩展到布局的底部或者右边边缘处，`Qt::Alignment`为对齐方式。

- `addLayout ( QLayout *, int row, int column, Qt::Alignment = 0 );`
- `addLayout ( QLayout *, int row, int column, int rowSpan, int columnSpan, Qt::Alignment = 0 );`

和`addWidget`类似，这个是添加布局。

- `setRowStretch ( int row, int stretch );`
- `setColumnStretch ( int column, int stretch );`

设置`行/列`的伸缩空间，和`QBoxLayout`的`addStretch`功能类似。

- `setSpacing ( int spacing );`
- `setHorizontalSpacing ( int spacing );`
- `setVerticalSpacing ( int spacing );`

设置间距。`setSpacing`可以同时设置水平、垂直间距，设置之后，水平、垂直间距相同。`setHorizontalSpacing`、`setVerticalSpacing`可以分别设置水平间距、垂直间距。

- `setRowMinimumHeight(int row, int minSize);`：设置行最小高度。
- `setColumnMinimumWidth(int column, int minSize);`：设置列最小宽度。
- `columnCount();`：获取列数。
- `rowCount();`：获取行数。
- `setOriginCorner(Qt::Corner);`：设置原始方向，和`QBoxLayout`的`setDirection`功能类似。