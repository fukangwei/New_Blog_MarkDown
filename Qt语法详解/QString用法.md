---
title: QString用法
date: 2019-02-01 16:01:27
categories: Qt语法详解
---
&emsp;&emsp;从字符串`one, two, three, four`中获取第二个由`,`分隔的子串，即`two`：

``` cpp
#include <QtCore/QCoreApplication>
#include <iostream>
​
using namespace std;
​
int main() {
    QString str = "one, two, three, four";
    cout << str.section ( ',', 1, 1 ).trimmed().toStdString() << endl;
    return 0;
}
```

函数`trimmed`用于去掉字符串前后的`\t`、`\n`、`\v`、`\f`、`\r`和`空格`，这些字符用`QChar::isSpace`判断都返回`true`。
&emsp;&emsp;从字符串`one, two* three / four / five ^ six`中获取第四个由`,`、`*`、`/`和`^`分隔的子串，即`four`：

``` cpp
#include <QtCore/QCoreApplication>
#include <QRegExp>
#include <iostream>
​
using namespace std;
​
int main() {
    QString str = "one, two* three / four / five ^ six";
    cout << str.section ( QRegExp ( "[,*/^]" ), 3, 3 ).trimmed().toStdString() << endl;
    return 0;
}
```

上面用到了一个简单的正则表达式，在`Qt`中可以由类`QRegExp`构造，函数`section`支持使用正则表达式。

---

&emsp;&emsp;`section`函数原型如下：

``` cpp
QString QString::section ( QChar sep, int start, int end = -1, \
                           SectionFlags flags = SectionDefault ) const
```

这个函数把字符串看成是几个块，这些块由`sep`分隔，`start`和`end`指定块号，返回的是`[start, end]`内的块组成的字符串。如果`start`和`end`都是负数，那么将从字符串的后面往前面数，返回`[-end, -start]`内的块组成的字符串。`SectionFlags`是一些标记，例如`SectionSkipEmpty`表示如果两个分隔符之间是空串，那么就会跳过。

``` cpp
QString str;
QString csv = "forename,middlename,surname,phone";
QString path = "/usr/local/bin/myapp"; // First field is empty
QString::SectionFlag flag = QString::SectionSkipEmpty;
​
str = csv.section ( ',', 2, 2 ); // str == "surname"
str = path.section ( '/', 3, 4 ); // str == "bin/myapp"
str = path.section ( '/', 3, 3, flag ); // str == "myapp"
​
str = csv.section ( ',', -3, -2 ); // str == "middlename,surname"
str = path.section ( '/', -1 ); // str == "myapp"
```

这个函数的另两个重载函数如下：

``` cpp
QString QString::section ( const QString &sep, int start, int end = -1, \
                           SectionFlags flags = SectionDefault ) const
QString QString::section ( const QRegExp &reg, int start, int end = -1, \
                           SectionFlags flags = SectionDefault ) const
```

&emsp;&emsp;`split`函数原型如下：

``` cpp
QStringList QString::split ( const QChar &sep, SplitBehavior behavior = KeepEmptyParts, \
                             Qt::CaseSensitivity cs = Qt::CaseSensitive ) const
```

这个函数把所有的由`sep`分隔的块装进一个`QStringList`中返回，这个函数同样有两个重载：

``` cpp
QStringList QString::split ( const QString &sep, SplitBehavior behavior = KeepEmptyParts, \
                             Qt::CaseSensitivity cs = Qt::CaseSensitive ) const
QStringList QString::split ( const QRegExp &rx, SplitBehavior behavior = KeepEmptyParts ) const
```

使用实例如下：

``` cpp
#include <QtCore/QCoreApplication>
#include <QRegExp>
#include <QStringList>
#include <iostream>
​
using namespace std;
​
int main() {
    QString str = "one, two* three / four / five ^ six";
    /* 把每一个块装进一个QStringList中 */
    QStringList sections = str.split ( QRegExp ( "[,*/^]" ) );
    cout << sections.at ( 3 ).trimmed().toStdString() << endl;
    return 0;
}
```

---

### QString与数字的相互转化

#### 把QString转换为double类型

&emsp;&emsp;方法`1`如下：

``` cpp
QString str = "123.45";
double val = str.toDouble(); /* val = 123.45 */
```

&emsp;&emsp;方法`2`很适合科学计数法形式转换：

``` cpp
bool ok;
double d;
/* ok is true, d is 12.3456 */
d = QString ( "1234.56e-02" ).toDouble ( &ok );
```

#### 把QString转换为float型

&emsp;&emsp;方法如下：

``` cpp
/* 方法1 */
QString str = "123.45";
float d = str.toFloat(); /* d = 123.45 */
/* 方法2 */
QString str = "R2D2";
bool ok;
float d = str.toFloat ( &ok ); /* 转换失败返回0.0，ok为false */
```

&emsp;&emsp;把`double`型数据转换为`QString`类型：`double`类转换`QString`类型使用`QString::number`函数，第一个参数为需要转换的`double`数据；第二个参数为基数，即`10`、`2`、`8`等；第三个参数为精度。

``` cpp
double intResult;
QLabel *pornPropLabel;
pornPropLabel->setText ( QString::number ( intResult, 10, 4 ) );
```

### 把QString形转换为整型

&emsp;&emsp;1. 转换为十进制整型：基数默认为`10`。如果基数为`0`，若字符串是以`0x`开头的就会转换为`16`进制，若以`0`开头就转换为八进制，否则就转换为十进制。

``` cpp
Qstring str = "FF";
bool ok;
int dec = str.toInt ( &ok, 10 ); /* dec is 255, ok is true */
int hex = str.toInt ( &ok, 16 ); /* hex is 255, ok is true */
```

&emsp;&emsp;2. 常整型转换为`Qstring`型：

``` cpp
long a = 63;
QString str = QString::number ( a, 16 ); /* str = "3f" */
QString str = QString::number ( a, 16 ).toUpper(); /* str = "3F" */
```

&emsp;&emsp;`Qstring`转换为`char *`：

``` cpp
/* 方法一 */
QString qstr ( "hello,word" );
const char *p = qstr.toLocal8Bit().data();
/* 方法二 */
const char *p = qstr.toStdString().data(); /* 转换过来的是常量 */
```

&emsp;&emsp;把当前时间转化为`QString`：

``` cpp
QDateTime qdate = QDateTime::currentDateTime();
datetime = qdate.toString ( "yyyy年MM月dd日ddddhh:mm:ss" );
```

---

&emsp;&emsp;`QString`类提供了很方便的对字符串操作的接口。
&emsp;&emsp;1. 使某个字符填满字符串，也就是说字符串里的所有字符都用等长度的`ch`来代替：

``` cpp
QString::fill ( QChar ch, int size = -1 );
```

例如：

``` cpp
QString str = "Berlin";
str.fill ( 'z' ); /* str is "zzzzzz" */
str.fill ( 'A', 2 ); /* str is "AA" */
```

&emsp;&emsp;2. 从字符串里查找相同的某个字符串`str`：

``` cpp
int QString::indexOf ( const QString &str, int from = 0, \
                       Qt::CaseSensitivity cs = Qt::CaseSensitive ) const;
```

例如：

``` cpp
QString x = "sticky question";
QString y = "sti";
x.indexOf ( y );  /* returns 0 */
x.indexOf ( y, 1 ); /* returns 10 */
x.indexOf ( y, 10 ); /* returns 10 */
x.indexOf ( y, 11 ); /* returns -1 */
```

&emsp;&emsp;3. 指定位置插入字符串：

``` cpp
QString &QString::insert ( int position, const QString &str );
```

例如：

``` cpp
QString str = "Meal";
str.insert ( 1, QString ( "ontr" ) ); /* str is "Montreal" */
```

&emsp;&emsp;4. 判断字符串是否为空：

``` cpp
bool QString::isEmpty () const;
```

例如：

``` cpp
QString().isEmpty(); /* returns true */
QString ( "" ).isEmpty(); /* returns true */
QString ( "x" ).isEmpty(); /* returns false */
QString ( "abc" ).isEmpty(); /* returns false */
```

&emsp;&emsp;5. 判断字符串是否存在：

``` cpp
bool QString::isNull () const;
```

例如：

``` cpp
QString().isNull(); /* returns true */
QString ( "" ).isNull(); /* returns false */
QString ( "abc" ).isNull(); /* returns false */
```

&emsp;&emsp;6. 从左向右截取字符串：

``` cpp
QString QString::left ( int n ) const;
```

例如：

``` cpp
QString x = "Pineapple";
QString y = x.left ( 4 ); /* y is "Pine" */
```

&emsp;&emsp;7. 从中间截取字符串：

``` cpp
QString QString::mid ( int position, int n = -1 ) const;
```

例如：

``` cpp
QString x = "Nine pineapples";
QString y = x.mid ( 5, 4 ); /* y is "pine" */
QString z = x.mid ( 5 ); /* z is "pineapples" */
```

&emsp;&emsp;8. 删除字符串中的某个字符：

``` cpp
QString &QString::remove ( int position, int n );
```

例如：

``` cpp
QString s = "Montreal";
s.remove ( 1, 4 ); /* s is "Meal" */
```

&emsp;&emsp;9. 替换字符串中的某些字符：

``` cpp
QString &QString::replace ( int position, int n, const QString &after );
```

例如：

``` cpp
QString x = "Say yes!";
QString y = "no";
x.replace ( 4, 3, y ); /* x is "Say no!" */
```

&emsp;&emsp;10. 把整型、浮点型或其他类型转化为`QString`：

``` cpp
QString &QString::setNum ( uint n, int base = 10 );
```

---

### QString与“char *”之间的转换

&emsp;&emsp;1. `QString`转换为`char *`。
&emsp;&emsp;先将`QString`转换为`QByteArray`，再将`QByteArray`转换为`char *`。注意，不能用下面的转换形式：

``` cpp
char *mm = str.toLatin1().data();
```

&emsp;&emsp;2. `char *`转换为`QString`。
&emsp;&emsp;可以使用`QString`的构造函数进行转换，即`QString(const QLatin1String &str);`，`QLatin1String`的构造函数为`QLatin1String(const char *str);`。如下语句是将`char * mm`转换为`QString str`：

``` cpp
str = QString ( QLatin1String ( mm ) );
```

示例代码如下：

``` cpp
#include <QtGui/QApplication>
#include <QtDebug>
#include <QString>
​
int main ( int argc, char *argv[] ) {
    QApplication a ( argc, argv );
    QString str = “hello”; /* QString转“char *” */
    QByteArray ba = str.toLatin1();
    char *mm = ba.data();
    qDebug() << mm << endl; /* 调试时，在console中输出 */
    QString nn = QString ( QLatin1String ( mm ) ); /* “char *”转QString */
    qDebug() << nn << endl; /* 调试时，在console中输出 */
    return a.exec();
}
```

---

### 如何使用QString::arg？

&emsp;&emsp;`QString::arg`函数原型如下：

``` cpp
QString QString::arg ( const QString &a, int fieldWidth = 0, \
                       const QChar &fillChar = QLatin1Char ( ' ' ) ) const
```

功能为`Returns a copy of this string with the lowest numbered place marker replaced by string a, i.e., %1, %2, ..., %99.`。

``` cpp
QString i = "5"; /* current file's number */
QString total = "10"; /* number of files to process */
QString fileName = "lyc.txt"; /* current file's name */
QString status = QString ( "Processing file %1 of %2: %3" ).arg ( i ).arg ( total ).arg ( fileName );
qDebug() << "status:" << status;
```

执行结果：

``` cpp
status: "Processing file 5 of 10: lyc.txt"  
```

---

### String和QString之间的转化

&emsp;&emsp;代码如下：

``` cpp
QString qstr;
string str;
str = qstr.toStdString();
qstr = QString::fromStdString ( str );
```