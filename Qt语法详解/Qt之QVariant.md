---
title: Qt之QVariant
categories: Qt语法详解
abbrlink: c73556ae
date: 2019-01-03 08:16:15
---
&emsp;&emsp;`QVariant`类是一个最为普遍的`Qt`数据类型的联合。因为`C++`禁止没有构造函数和析构函数的联合体，许多继承的`Qt`类不能够在联合体当中使用(联合体当中的变量共用一个存储区)。没有了联合变量，我们在物体属性以及数据库的工作等方面受到很多的困扰。一个`QVariant`对象在一个时间内只保留一种类型的值。我们可以使用`canConvert`来查询是否能够转换当前的类型。转换类型一般以`toT`命名。
&emsp;&emsp;摘录了一个`example`来说明`QVariant`的使用方法：

``` cpp
QDataStream out ( ... );
QVariant v ( 123 );                    /* The variant now contains an int */
int x = v.toInt();                     /* x = 123 */
out << v;                              /* Writes a type tag and an int to out */
v = QVariant ( "hello" );              /* The variant now contains a QByteArray */
v = QVariant ( tr ( "hello" ) );       /* The variant now contains a QString */
int y = v.toInt();                     /* y = 0 since v cannot be converted to an int */
QString s = v.toString();              /* s = tr("hello") */
out << v;                              /* Writes a type tag and a QString to out */
...
QDataStream in ( ... );                /* opening the previously written stream */
in >> v;                               /* Reads an Int variant */
int z = v.toInt();                     /* z = 123 */
qDebug ( "Type is %s", v.typeName() ); /* prints "Type is int" */
v = v.toInt() + 100;                   /* The variant now hold the value 223 */
v = QVariant ( QStringList() );
```

你甚至可以存储`QList<QVariant>`和`QMap<QString, QVariant>`，所以可以构造任意复杂的数据类型，这是非常强大而且又有用的。`QVariant`也支持`null`值，可以定义一个没有任何值的类型，然而也要注意`QVariant`类型只能在它们有值的时候被强制转换：

``` cpp
QVariant x, y ( QString() ), z ( QString ( "" ) );
x.convert ( QVariant::Int );
// x.isNull() == true
// y.isNull() == true, z.isNull() == false
```

因为`QVariant`是`QtCore`库的一部分，它不能够提供定义在`QtGui`当中的类型的转换，例如`QColor`、`QImage`和`QPixmap`等。换句话说，没有`toColor`这样的函数。但是你可以使用`QVariant::value`或者`qVariantValue`这两个模板函数：

``` cpp
QVariant variant;
QColor color = variant.value<QColor>();
```

反向转换(例如把`QColor`转成`QVariant`)是自动完成的，这也包含了`GUI`相关的那些数据类型：

``` cpp
QColor color = palette().background().color();
QVariant variant = color;
```