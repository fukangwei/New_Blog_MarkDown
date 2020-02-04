---
title: Qt之QListWidget
categories: Qt语法详解
abbrlink: e56241ba
date: 2019-03-17 20:00:33
---
&emsp;&emsp;`QListWidget`可以显示一个清单，清单中的每个项目是`QListWidgetItem`的一个实例，每个项目可以通过`QListWidgetItem`来操作。可以通过`QListWidgetItem`来设置每个项目的图像与文字。<!--more-->
&emsp;&emsp;示例`1`如下：

``` cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QListWidget>
#include <QListWidgetItem>
​
int main ( int argc, char **argv ) {
    QApplication app ( argc, argv );
    QWidget *widget = new QWidget;
    QListWidget *listWidget = new QListWidget;
    QVBoxLayout *layout = new QVBoxLayout;
    QListWidgetItem *lst1 = new QListWidgetItem ( "data", listWidget );
    QListWidgetItem *lst2 = new QListWidgetItem ( "decision", listWidget );
    QListWidgetItem *lst3 = new QListWidgetItem ( "document", listWidget );
    QListWidgetItem *lst4 = new QListWidgetItem ( "process", listWidget );
    QListWidgetItem *lst5 = new QListWidgetItem ( "printer", listWidget );
    listWidget->insertItem ( 1, lst1 );
    listWidget->insertItem ( 2, lst2 );
    listWidget->insertItem ( 3, lst3 );
    listWidget->insertItem ( 4, lst4 );
    listWidget->insertItem ( 5, lst5 );
    listWidget->show();
    layout->addWidget ( listWidget );
    widget->setLayout ( layout );
    widget->show();
    app.exec();
}
```

<img src="./Qt之QListWidget/1.png">

&emsp;&emsp;示例`2`如下：

``` cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QListWidget>
#include <QListWidgetItem>
​
int main ( int argc, char **argv ) {
    QApplication app ( argc, argv );
    QWidget *widget = new QWidget;
    QListWidget *listWidget = new QListWidget;
    QVBoxLayout *layout = new QVBoxLayout;
    QListWidgetItem *lst1 = new QListWidgetItem ( QIcon ( "images/data.png" ), "data", listWidget );
    QListWidgetItem *lst2 = new QListWidgetItem ( QIcon ( "images/decision.png" ), "decision", listWidget );
    QListWidgetItem *lst3 = new QListWidgetItem ( QIcon ( "images/document.png" ), "document", listWidget );
    QListWidgetItem *lst4 = new QListWidgetItem ( QIcon ( "images/process.png" ), "process", listWidget );
    QListWidgetItem *lst5 = new QListWidgetItem ( QIcon ( "images/printer.png" ), "printer", listWidget );
    listWidget->insertItem ( 1, lst1 );
    listWidget->insertItem ( 2, lst2 );
    listWidget->insertItem ( 3, lst3 );
    listWidget->insertItem ( 4, lst4 );
    listWidget->insertItem ( 5, lst5 );
    listWidget->show();
    layout->addWidget ( listWidget );
    widget->setLayout ( layout );
    widget->show();
    app.exec();
}
```

<img src="./Qt之QListWidget/2.png">

&emsp;&emsp;示例`3`如下：

``` cpp
#include <QApplication>
#include <QWidget>
#include <QHBoxLayout>
#include <QListWidget>
#include <QListWidgetItem>
#include <QLabel>
​
int main ( int argc, char **argv ) {
    QApplication app ( argc, argv );
    QWidget *widget = new QWidget;
    QListWidget *listWidget = new QListWidget;
    QHBoxLayout *layout = new QHBoxLayout;
    QLabel *label = new QLabel;
    label->setFixedWidth ( 100 );
    QListWidgetItem *lst1 = new QListWidgetItem ( QIcon ( "images/data.png" ), "data", listWidget );
    QListWidgetItem *lst2 = new QListWidgetItem ( QIcon ( "images/decision.png" ), "decision", listWidget );
    QListWidgetItem *lst3 = new QListWidgetItem ( QIcon ( "images/document.png" ), "document", listWidget );
    QListWidgetItem *lst4 = new QListWidgetItem ( QIcon ( "images/process.png" ), "process", listWidget );
    QListWidgetItem *lst5 = new QListWidgetItem ( QIcon ( "images/printer.png" ), "printer", listWidget );
    listWidget->insertItem ( 1, lst1 );
    listWidget->insertItem ( 2, lst2 );
    listWidget->insertItem ( 3, lst3 );
    listWidget->insertItem ( 4, lst4 );
    listWidget->insertItem ( 5, lst5 );
    QObject::connect (
        listWidget, SIGNAL ( currentTextChanged ( const QString & ) ),
        label, SLOT ( setText ( const QString & ) ) );
    listWidget->show();
    layout->addWidget ( listWidget );
    layout->addWidget ( label );
    widget->setLayout ( layout );
    widget->show();
    app.exec();
}
```

<img src="./Qt之QListWidget/3.png">

---

&emsp;&emsp;`QListWidget`为我们展示一个`List`列表的视图。
&emsp;&emsp;`listwidget.h`如下：

``` cpp
#ifndef LISTWIDGET_H
#define LISTWIDGET_H
​
#include <QtGui>
​
class ListWidget : public QWidget {
public:
    ListWidget();
private:
    QLabel *label;
    QListWidget *list;
};
​
#endif // LISTWIDGET_H
```

&emsp;&emsp;`listwidget.cpp`如下：

``` cpp
#include "listwidget.h"
​
ListWidget::ListWidget() {
    label = new QLabel;
    label->setFixedWidth ( 70 );
    list = new QListWidget;
    list->addItem ( new QListWidgetItem ( QIcon ( ":/images/line.PNG" ), tr ( "Line" ) ) );
    list->addItem ( new QListWidgetItem ( QIcon ( ":/images/rect.PNG" ), tr ( "Rectangle" ) ) );
    list->addItem ( new QListWidgetItem ( QIcon ( ":/images/oval.PNG" ), tr ( "Oval" ) ) );
    list->addItem ( new QListWidgetItem ( QIcon ( ":/images/tri.PNG" ), tr ( "Triangle" ) ) );
    QHBoxLayout *layout = new QHBoxLayout;
    layout->addWidget ( label );
    layout->addWidget ( list );
    setLayout ( layout );
    connect ( list, SIGNAL ( currentTextChanged ( QString ) ), label, SLOT ( setText ( QString ) ) );
}
```

&emsp;&emsp;`main.cpp`如下：

``` cpp
#include <QtGui>
#include "listwidget.h"
​
int main ( int argc, char *argv[] ) {
    QApplication a ( argc, argv );
    ListWidget lw;
    lw.resize ( 400, 200 );
    lw.show();
    return a.exec();
}
```

`ListWidget`类中包含一个`QLabel`对象和一个`QListWidget`对象。创建这个`QListWidget`对象很简单，只需要使用`new`运算符创建出来，然后调用`addItem`函数即可将`item`添加到这个对象中。我们添加的对象是`QListWidgetItem`的指针，`addItem`有四个重载的函数，我们使用的是其中的一个：它接受两个参数，第一个是`QIcon`引用类型，作为`item`的图标；第二个是`QString`类型，作为这个`item`后面的文字说明。当然也可以使用`insertItem`函数在特定的位置动态地增加`item`。最后将这个`QListWidget`的`currentTextChanged`信号同`QLabel`的`setText`连接起来，这样在点击`item`时，`label`的文字就可以改变了。

<img src="./Qt之QListWidget/4.png">

我们还可以设置`viewModel`这个参数，来使用不同的视图进行显示：

``` cpp
list->setViewMode ( QListView::IconMode );
```

<img src="./Qt之QListWidget/5.png">

---

### QListWidget控件的使用

&emsp;&emsp;`Qt`提供了`QListWidget`类列表框控件，用来加载并显示多个列表项。`QListWidgetItem`类就是列表项类，一般列表框控件中的列表项有两种加载方式：一种是由用户手动添加的列表项，比如音乐播放器中加载音乐文件的文件列表，每一个音乐文件都是一个列表项。对于这种列表项，用户可以进行增加、删除、单击以及双击等操作。一种是由程序员事先编写好，写在程序中供用户选择的列表项，比如餐厅的电子菜单，每一道菜对应一个列表项。对于这种列表项，用户可以进行单机和双击操作(增加和删除操作也是可以进行的，但是一般的点菜系统会屏蔽掉这种功能)。
&emsp;&emsp;`QListWidget`类列表框控件支持两种列表项显示方式，即`QListView::IconMode`和`QListView::ListMode`。

<img src="./Qt之QListWidget/6.png" height="261" width="337">

&emsp;&emsp;`main.cpp`如下：

``` cpp
#include <QtGui/QApplication>
#include "mainwindow.h"
#include <QTextCodec>

int main ( int argc, char *argv[] ) {
    QApplication a ( argc, argv );
    /* Qt文本的国际化显示 */
    QTextCodec::setCodecForTr ( QTextCodec::codecForName ( "GB18030" ) );
    QTextCodec::setCodecForLocale ( QTextCodec::codecForName ( "GB18030" ) );
    QTextCodec::setCodecForCStrings ( QTextCodec::codecForName ( "GB18030" ) );
    MainWindow w;
    w.show();
    return a.exec();
}
```

&emsp;&emsp;`mainwindow.h`如下：

``` cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H
#include <QMainWindow>
#include <QtDebug>
#include <QMessageBox>
#include <QListWidgetItem> /* 列表框空间头文件 */
#include <QFileDialog> /* 文件对话框控件 */
#include <QStringList> /* 字符串容器 */
#include <QDir> /* 目录类头文件 */
#include <QString>
​
namespace Ui {
    class MainWindow;
}
​
class MainWindow : public QMainWindow {
    Q_OBJECT
public:
    explicit MainWindow ( QWidget *parent = 0 );
    ~MainWindow();
private:
    Ui::MainWindow *ui;
private slots:
    void addbtn();
    void deletebtn();
    void delallbtn();
    void addallbtn();
    void singleclicked ( QListWidgetItem *item );
    void doubleclicked ( QListWidgetItem *item );
};
#endif // MAINWINDOW_H
```

&emsp;&emsp;`mainwindow.cpp`如下：

``` cpp
#include "mainwindow.h"
#include "ui_mainwindow.h"
​
MainWindow::MainWindow ( QWidget *parent ) : QMainWindow ( parent ), ui ( new Ui::MainWindow ) {
    ui->setupUi ( this );
    this->setWindowTitle ( tr ( "listWidget学习" ) ); /* 设置标题框文本 */
    ui->listWidget->setViewMode ( QListView::IconMode ); /* 设置显示模式为图标模式 */
    // ui->listWidget->setViewMode ( QListView::ListMode ); /* 设置显示模式为列表模式 */
    QObject::connect ( ui->AddButton, SIGNAL ( clicked() ), this, SLOT ( addbtn() ) );
    QObject::connect ( ui->lineEdit, SIGNAL ( returnPressed() ), this, SLOT ( addbtn() ) );
    QObject::connect ( ui->DeleteButton, SIGNAL ( clicked() ), this, SLOT ( deletebtn() ) );
    QObject::connect ( ui->DelAllButton, SIGNAL ( clicked() ), this, SLOT ( delallbtn() ) );
    QObject::connect ( ui->ShowDirButton, SIGNAL ( clicked() ), this, SLOT ( addallbtn() ) );
    QObject::connect (
        ui->listWidget, SIGNAL ( itemDoubleClicked ( QListWidgetItem * ) ),
        this, SLOT ( doubleclicked ( QListWidgetItem * ) ) );
}

MainWindow::~MainWindow() {
    delete ui;
}
​
void MainWindow::addbtn() { /* 添加单个列表项 */
    QString str = ui->lineEdit->text(); /* 获取行编辑框文本 */
    QListWidgetItem *item = new QListWidgetItem;
    item->setText ( str ); /* 设置列表项的文本 */
    ui->listWidget->addItem ( item ); /* 加载列表项到列表框 */
    // delete item; /* 此处若解除注释，将无法添加到列表框 */
    // item = NULL;
    ui->lineEdit->clear(); /* 清空行编辑框 */
}
​
void MainWindow::deletebtn() { /* 删除单个列表项 */
    /* 获取列表项的指针 */
    QListWidgetItem *item = ui->listWidget->takeItem ( ui->listWidget->currentRow() );
    delete item; /* 释放指针所指向的列表项 */
}
​
void MainWindow::delallbtn() { /* 删除多个列表项 */
    int num = ui->listWidget->count(); /* 获取列表项的总数目 */
    /* 将光标设置到列表框上，若注释该语句，则删除时，要手动将焦点设置到列表框，即点击列表项 */
    ui->listWidget->setFocus();
​
    for ( int i = 0; i < num; i++ ) { /* 逐个获取列表项的指针，并删除 */
        QListWidgetItem *item = ui->listWidget->takeItem ( ui->listWidget->currentRow() );
        delete item;
    }
}
​
void MainWindow::addallbtn() { /* 添加多个列表项 */
    QStringList FileNames = QFileDialog::getOpenFileNames (
        this, "打开", QDir::currentPath(), "所有文件(*.*);;文本文档(*.txt)" );
    //ui->listWidget->addItems ( FileNames ); /* 方法1：整体添加 */
    /* 方法2：逐个添加 */
    int index = 0, count = 0;
    count = FileNames.count(); /* 获取打开文件的总数目 */
​
    // for(index = 0; index < count; index++) /* 这样会报错，无法先取出栈底元素 */
    for ( index = count - 1; index >= 0; index-- ) { /* QList<QString>的数据结构是栈，只能从栈顶取元素 */
        QListWidgetItem *item = new QListWidgetItem;
        item->setText ( FileNames.takeAt ( index ) ); /* 逐个设置列表项的文本 */
        // qDebug() << FileNames.takeAt( index );
        ui->listWidget->addItem ( item ); /* 加载列表项到列表框 */
    }
}
​
void MainWindow::singleclicked ( QListWidgetItem *item ) { /* 列表项单击操作 */
    QMessageBox::information ( this, "单击消息", "单击" + item->text() );
}
​
void MainWindow::doubleclicked ( QListWidgetItem *item ) { /* 列表项双击操作 */
    QMessageBox::information ( this, "双击消息", "双击" + item->text() );
}
```

### 添加操作

&emsp;&emsp;添加操作又可以分为单列表项操作和多列表项操作。

- `void QListWidget::addItems ( const QStringList & labels )`：该函数用来将字符串列表中的全部字符串作为列表项添加到列表框中。
- `void QListWidget::addItem ( QListWidgetItem * item )`：该函数用来将一个列表项添加到列表框当中。注意，一个列表项只能被添加到列表框中一次，如果多次添加同一个列表项到列表框中，将导致不可预期的结果。
- `void QListWidget::addItem ( const QString & label )`：重载函数，用来将参数`label`所引用的字符串作为一个列表项，添加到列表框中。
- `int QList::count ()const and int QList::size () const`：上述两个函数的功能等价，都是用来返回列表中存储的对象元素的个数。
- `T QList::takeAt ( int i )`：该函数按照参数i指定的索引位置，将存储在列表中对应的对象元素移除并返回。返回类型为模板类型，由存储的数据的类型决定。索引值的大小范围为`0 <= i <= size()`。

注意，`QList<QString>`的数据结构是栈，只能从栈顶取元素。

### 删除操作

&emsp;&emsp;删除操作又可以分为单文件操作和多文件操作。删除单个列表项(删除列表框中的单个列表项)：

- `QListWidgetItem *QListWidget::takeItem ( int row )`：该函数用来将索引号为`row`的列表项从列表框移除，并返回该列表项的指针。
- `int currentRow() const`：该常成员函数用来获取当前列表项的索引号，并返回它。

---

&emsp;&emsp;The `QListWidget` class provides an `item-based` list widget.

Header        | Inherits
--------------|----------
`QListWidget` | `QListView`

### Public Functions

Return                     | Function
---------------------------|---------
                           | `QListWidget(QWidget * parent = 0)`
                           | `~QListWidget()`
`void`                     | `addItem(const QString & label)`
`void`                     | `addItem(QListWidgetItem * item)`
`void`                     | `addItems(const QStringList & labels)`
`void`                     | `closePersistentEditor(QListWidgetItem * item)`
`int`                      | `count() const`
`QListWidgetItem *`        | `currentItem() const`
`int`                      | `currentRow() const`
`void`                     | `editItem(QListWidgetItem * item)`
`QList<QListWidgetItem *>` | `findItems(const QString & text, Qt::MatchFlags flags) const`
`void`                     | `insertItem(int row, QListWidgetItem * item)`
`void`                     | `insertItem(int row, const QString & label)`
`void`                     | `insertItems(int row, const QStringList & labels)`
`bool`                     | `isSortingEnabled() const`
`QListWidgetItem *`        | `item(int row) const`
`QListWidgetItem *`        | `itemAt(const QPoint & p) const`
`QListWidgetItem *`        | `itemAt(int x, int y) const`
`QWidget *`                | `itemWidget(QListWidgetItem * item) const`
`void`                     | `openPersistentEditor(QListWidgetItem * item)`
`void`                     | `removeItemWidget(QListWidgetItem * item)`
`int`                      | `row(const QListWidgetItem * item) const`
`QList<QListWidgetItem *>` | `selectedItems() const`
`void`                     | `setCurrentItem(QListWidgetItem * item)`
`void`                     | `setCurrentItem(QListWidgetItem * item, QItemSelectionModel::SelectionFlags command)`
`void`                     | `setCurrentRow(int row)`
`void`                     | `setCurrentRow(int row, QItemSelectionModel::SelectionFlags command)`
`void`                     | `setItemWidget(QListWidgetItem * item, QWidget * widget)`
`void`                     | `setSortingEnabled(bool enable)`
`void`                     | `sortItems(Qt::SortOrder order = Qt::AscendingOrder)`
`QListWidgetItem *`        | `takeItem(int row)`
`QRect`                    | `visualItemRect(const QListWidgetItem * item) const`

### Reimplemented Public Functions

- `virtual void dropEvent(QDropEvent * event)`

### Public Slots

Return | Function
-------|---------
`void` | `clear()`
`void` | `scrollToItem(const QListWidgetItem * item, QAbstractItemView::ScrollHint hint = EnsureVisible)`

### Signals

Return | Function
-------|---------
`void` | `currentItemChanged(QListWidgetItem * current, QListWidgetItem * previous)`
`void` | `currentRowChanged(int currentRow)`
`void` | `currentTextChanged(const QString & currentText)`
`void` | `itemActivated(QListWidgetItem * item)`
`void` | `itemChanged(QListWidgetItem * item)`
`void` | `itemClicked(QListWidgetItem * item)`
`void` | `itemDoubleClicked(QListWidgetItem * item)`
`void` | `itemEntered(QListWidgetItem * item)`
`void` | `itemPressed(QListWidgetItem * item)`
`void` | `itemSelectionChanged()`

### Protected Functions

Return                     | Function
---------------------------|---------
`virtual bool`             | `dropMimeData(int index, const QMimeData * data, Qt::DropAction action)`
`QModelIndex`              | `indexFromItem(QListWidgetItem * item) const`
`QListWidgetItem *`        | `itemFromIndex(const QModelIndex & index) const`
`QList<QListWidgetItem *>` | `items(const QMimeData * data) const`
`virtual QMimeData *`      | `mimeData(const QList<QListWidgetItem *> items) const`
`virtual QStringList`      | `mimeTypes() const`
`virtual Qt::DropActions`  | `supportedDropActions() const`

### Reimplemented Protected Functions

- `virtual bool event(QEvent * e)`

### Detailed Description

&emsp;&emsp;The `QListWidget` class provides an `item-based` list widget.
&emsp;&emsp;`QListWidget` is a convenience class that provides a list view similar to the one supplied by `QListView`, but with a classic `item-based` interface for adding and removing items. `QListWidget` uses an internal model to manage each `QListWidgetItem` in the list.
&emsp;&emsp;For a more flexible list view widget, use the `QListView` class with a standard model.
&emsp;&emsp;List widgets are constructed in the same way as other widgets:

``` cpp
QListWidget *listWidget = new QListWidget ( this );
```

&emsp;&emsp;The `selectionMode()` of a list widget determines how many of the items in the list can be selected at the same time, and whether complex selections of items can be created. This can be set with the `setSelectionMode()` function.
&emsp;&emsp;There are two ways to add items to the list: they can be constructed with the list widget as their parent widget, or they can be constructed with no parent widget and added to the list later. If a list widget already exists when the items are constructed, the first method is easier to use:

``` cpp
new QListWidgetItem ( tr ( "Oak" ), listWidget );
new QListWidgetItem ( tr ( "Fir" ), listWidget );
new QListWidgetItem ( tr ( "Pine" ), listWidget );
```

&emsp;&emsp;If you need to insert a new item into the list at a particular position, then it should be constructed without a parent widget. The `insertItem()` function should then be used to place it within the list. The list widget will take ownership of the item.

``` cpp
QListWidgetItem *newItem = new QListWidgetItem;
newItem->setText ( itemText );
listWidget->insertItem ( row, newItem );
```

&emsp;&emsp;For multiple items, `insertItems()` can be used instead. The number of items in the list is found with the `count()` function. To remove items from the list, use `takeItem()`.
&emsp;&emsp;The current item in the list can be found with `currentItem()`, and changed with `setCurrentItem()`. The user can also change the current item by navigating with the keyboard or clicking on a different item. When the current item changes, the `currentItemChanged()` signal is emitted with the new current item and the item that was previously current.

<img src="./Qt之QListWidget/7.png">

### Property Documentation

- `count : const int`: This property holds the number of items in the list including any hidden items. Access functions:

``` cpp
int count() const
```

- `currentRow : int`: This property holds the row of the current item. Depending on the current selection mode, the row may also be selected. Access functions:

``` cpp
int currentRow() const
void setCurrentRow ( int row )
void setCurrentRow ( int row, QItemSelectionModel::SelectionFlags command )
```

Notifier signal:

``` cpp
void currentRowChanged ( int currentRow )
```

- `sortingEnabled : bool`: This property holds whether sorting is enabled. If this property is `true`, sorting is enabled for the list; if the property is `false`, sorting is not enabled. The default value is `false`. Access functions:

``` cpp
bool isSortingEnabled() const
void setSortingEnabled ( bool enable )
```

### Member Function Documentation

- `QListWidget::QListWidget(QWidget * parent = 0)`: Constructs an empty `QListWidget` with the given `parent`.
- `QListWidget::~QListWidget()`: Destroys the list widget and all its items.
- `void QListWidget::addItem(const QString & label)`: Inserts an item with the text `label` at the end of the list widget.
- `void QListWidget::addItem(QListWidgetItem * item)`: Inserts the `item` at the end of the list widget. **Warning**: A `QListWidgetItem` can only be added to a `QListWidget` once. Adding the same `QListWidgetItem` multiple times to a `QListWidget` will result in undefined behavior.
- `void QListWidget::addItems(const QStringList & labels)`: Inserts items with the text `labels` at the end of the list widget.
- `void QListWidget::clear() [slot]`: Removes all items and selections in the view. **Warning**: All items will be permanently deleted.
- `void QListWidget::closePersistentEditor(QListWidgetItem * item)`: Closes the persistent editor for the given `item`.
- `QListWidgetItem * QListWidget::currentItem() const`: Returns the current item.
- `void QListWidget::currentItemChanged(QListWidgetItem * current, QListWidgetItem * previous) [signal]`: This signal is emitted whenever the `current` item changes. `previous` is the item that previously had the focus; `current` is the new current item.
- `void QListWidget::currentTextChanged(const QString & currentText) [signal]`: This signal is emitted whenever the current item changes. `currentText` is the text data in the current item. If there is no current item, the `currentText` is invalid.
- `void QListWidget::dropEvent(QDropEvent * event) [virtual]`: Reimplemented from `QWidget::dropEvent()`.
- `bool QListWidget::dropMimeData(int index, const QMimeData * data, Qt::DropAction action) [virtual protected]`: Handles `data` supplied by an external drag and drop operation that ended with the given `action` in the given `index`. Returns `true` if `data` and `action` can be handled by the model; otherwise returns `false`.
- `void QListWidget::editItem(QListWidgetItem * item)`: Starts editing the `item` if it is editable.
- `bool QListWidget::event(QEvent * e) [virtual protected]`: Reimplemented from `QObject::event()`.
- `QList<QListWidgetItem *> QListWidget::findItems(const QString & text, Qt::MatchFlags flags) const`: Finds items with the `text` that matches the string text using the given `flags`.
- `QModelIndex QListWidget::indexFromItem(QListWidgetItem * item) const [protected]`: Returns the `QModelIndex` assocated with the given `item`.
- `void QListWidget::insertItem(int row, QListWidgetItem * item)`: Inserts the `item` at the position in the list given by `row`.
- `void QListWidget::insertItem(int row, const QString & label)`: Inserts an item with the text `label` in the list widget at the position given by `row`.
- `void QListWidget::insertItems(int row, const QStringList & labels)`: Inserts items from the list of `labels` into the list, starting at the given `row`.
- `QListWidgetItem * QListWidget::item(int row) const`: Returns the item that occupies the given `row` in the list if one has been set; otherwise returns `0`.
- `void QListWidget::itemActivated(QListWidgetItem * item) [signal]`: This signal is emitted when the `item` is activated. The item is activated when the user clicks or double clicks on it, depending on the system configuration. It is also activated when the user presses the activation key (on `Windows` and `X11` this is the `Return` key, on `Mac OS X` it is `Ctrl + 0`).
- `QListWidgetItem * QListWidget::itemAt(const QPoint & p) const`: Returns a pointer to the item at the coordinates `p`. The coordinates are relative to the list widget's `viewport()`.
- `QListWidgetItem * QListWidget::itemAt(int x, int y) const`: This is an overloaded function. Returns a pointer to the item at the coordinates `(x, y)`. The coordinates are relative to the list widget's `viewport()`.
- `void QListWidget::itemChanged(QListWidgetItem * item) [signal]`: This signal is emitted whenever the data of `item` has changed.
- `void QListWidget::itemClicked(QListWidgetItem * item) [signal]`: This signal is emitted with the specified `item` when a mouse button is clicked on an item in the widget.
- `void QListWidget::itemDoubleClicked(QListWidgetItem * item) [signal]`: This signal is emitted with the specified `item` when a mouse button is double clicked on an item in the widget.
- `void QListWidget::itemEntered(QListWidgetItem * item) [signal]`: This signal is emitted when the mouse cursor enters an `item`. The `item` is the item entered. This signal is only emitted when `mouseTracking` is turned on, or when a mouse button is pressed while moving into an item.
- `QListWidgetItem * QListWidget::itemFromIndex(const QModelIndex & index) const [protected]`: Returns a pointer to the `QListWidgetItem` assocated with the given `index`.
- `void QListWidget::itemPressed(QListWidgetItem * item) [signal]`: This signal is emitted with the specified `item` when a mouse button is pressed on an item in the widget.
- `void QListWidget::itemSelectionChanged() [signal]`: This signal is emitted whenever the selection changes.
- `QWidget * QListWidget::itemWidget(QListWidgetItem * item) const`: Returns the widget displayed in the given `item`.
- `QList<QListWidgetItem *> QListWidget::items(const QMimeData * data) const [protected]`: Returns a list of pointers to the items contained in the `data` object. If the object was not created by a `QListWidget` in the same process, the list is empty.
- `QMimeData * QListWidget::mimeData(const QList<QListWidgetItem *> items) const [virtual protected]`: Returns an object that contains a serialized description of the specified `items`. The format used to describe the items is obtained from the `mimeTypes()` function. If the list of `items` is empty, `0` is returned instead of a serialized empty list.
- `QStringList QListWidget::mimeTypes() const [virtual protected]`: Returns a list of `MIME` types that can be used to describe a list of listwidget items.
- `void QListWidget::openPersistentEditor(QListWidgetItem * item)`: Opens an editor for the given `item`. The editor remains open after editing.
- `void QListWidget::removeItemWidget(QListWidgetItem * item)`: Removes the widget set on the given `item`.
- `int QListWidget::row(const QListWidgetItem * item) const`: Returns the row containing the given `item`.
- `void QListWidget::scrollToItem(const QListWidgetItem * item, QAbstractItemView::ScrollHint hint = EnsureVisible) [slot]`: Scrolls the view if necessary to ensure that the `item` is visible. `hint` specifies where the item should be located after the operation.
- `QList<QListWidgetItem *> QListWidget::selectedItems() const`: Returns a list of all selected items in the list widget.
- `void QListWidget::setCurrentItem(QListWidgetItem * item)`: Sets the current item to `item`. Unless the selection mode is `NoSelection`, the `item` is also be selected.
- `void QListWidget::setCurrentItem(QListWidgetItem * item, QItemSelectionModel::SelectionFlags command)`: Set the current item to `item`, using the given `command`.
- `void QListWidget::setItemWidget(QListWidgetItem * item, QWidget * widget)`: Sets the `widget` to be displayed in the give `item`. This function should only be used to display static content in the place of a list widget item. If you want to display custom dynamic content or implement a custom editor widget, use `QListView` and subclass `QItemDelegate` instead.
- `void QListWidget::sortItems(Qt::SortOrder order = Qt::AscendingOrder)`: Sorts all the items in the list widget according to the specified `order`.
- `Qt::DropActions QListWidget::supportedDropActions() const [virtual protected]`: Returns the drop actions supported by this view.
- `QListWidgetItem * QListWidget::takeItem(int row)`: Removes and returns the item from the given `row` in the list widget; otherwise returns `0`. Items removed from a list widget will not be managed by `Qt`, and will need to be deleted manually.
- `QRect QListWidget::visualItemRect(const QListWidgetItem * item) const`: Returns the rectangle on the viewport occupied by the item at `item`.