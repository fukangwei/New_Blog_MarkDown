---
title: QStringList用法
categories: Qt语法详解
abbrlink: '584081e5'
date: 2019-01-24 13:25:08
---
&emsp;&emsp;`QStringList`类提供了一个字符串列表，从`QList <QString>`继承而来，它提供快速索引为基础的接入以及快速插入和清除。其成员函数用于操作这个字符串列表，例如`append`、`insert`、`replace`、`removeAll`、`removeAt`、`removeFirst`、`removeLast`和`removeOne`(这些函数只能用来清除`QStringList`中的某一个元素)等。
&emsp;&emsp;增加字符串可以使用`append`或者`<<`：

``` cpp
QStringList fonts;
/* fonts is ["Arial", "Helvetica", "Times", "Courier"] */
fonts << "Arial" << "Helvetica" << "Times" << "Courier";
```

&emsp;&emsp;合并字符串使用`join`：

``` cpp
/* str is "Arial,Helvetica,Times,Courier" */
QString str = fonts.join ( "," );
```

&emsp;&emsp;拆分字符串：

``` cpp
QString str = "Arial,Helvetica,Times,Courier";
/* list is ["Arial", "Helvetica", "Times", "Courier"] */
QStringList list = str.split ( "," );
```

&emsp;&emsp;`IndexOf`函数返回给定字符串的第一个出现的索引，而`lastIndexOf`函数返回字符串的最后一次出现的索引。
&emsp;&emsp;替换字符串：

``` cpp
QStringList files;
files << "$QTDIR/src/moc/moc.y"
      << "$QTDIR/src/moc/moc.l"
      << "$QTDIR/include/qconfig.h";
/* files is ["/usr/lib/qt/src/moc/moc.y", ...] */
files.replaceInStrings ( "$QTDIR", "/usr/lib/qt" );
```

&emsp;&emsp;过滤字符串：

``` cpp
QStringList list;
list << "Bill Murray" << "John Doe" << "Bill Clinton";
QStringList result;
/* result is ["Bill Murray", "Bill Clinton"] */
result = list.filter ( "Bill" );
```

&emsp;&emsp;打印`QStringList`每一个元素的方法：

``` cpp
QString str = "1,2,3,4,5,6,7,8,9";
QStringList strList;
​
strList = str.split ( "," );
​
cout << "String list item count: " << strList.size() << endl;
​
for ( int i = 0; i < strList.size(); i++ ) {
    /* 或者“cout << strList[i] << endl;” */
    cout << strList.at ( i ).toLocal8Bit().constData() << endl;
}
```

&emsp;&emsp;清空`QStringList`变量中所有元素的方法：

``` cpp
QStringList list;
list << "a" << "b";
list.clear(); /* 清空 */
list = QStringList(); /* 清空 */
```