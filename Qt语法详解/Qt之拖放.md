---
title: Qt之拖放
categories: Qt语法详解
date: 2019-02-01 18:27:48
---
&emsp;&emsp;拖放是应用程序内或者多个应用程序之间传递信息的一种直观的操作方式。除了为剪贴板提供支持之外，通常还提供数据的移动和复制功能。<!--more-->
&emsp;&emsp;拖放操作包括两个截然不同的动作，即拖动、放下。`Qt`窗口部件可以作为拖动点(`drag site`)、放下点(`drop site`)或者同时作为拖动点和放下点。
&emsp;&emsp;下面介绍如何让一个`Qt`应用程序接收一个拖动操作，当用户从桌面或者文件资源管理器中拖动一个文件到这个应用程序上放下时，该应用程序就会将文件的信息显示出来。

``` cpp
class TabelView : public QTableView {
public:
    explicit TabelView ( QWidget *parent = 0 );
protected:
    void dragEnterEvent ( QDragEnterEvent *event );
    void dragMoveEvent ( QDragMoveEvent *event );
    void dropEvent ( QDropEvent *event );
};

TabelView::TabelView ( QWidget *parent ) : QTableView ( parent ) {
    setAcceptDrops ( true );
    setDragDropMode ( QAbstractItemView::DragDrop );
    setDragEnabled ( true );
    setDropIndicatorShown ( true );
    this->setWindowTitle ( "TableView" );
}

void TabelView::dragEnterEvent ( QDragEnterEvent *event ) {
    if ( event->mimeData()->hasFormat ( "text/uri-list" ) ) {
        event->acceptProposedAction();
    }

    qDebug() << "drag enter";
}
```

&emsp;&emsp;以上通过自定义`QTableView`来实现拖放事件，首先需要设置`setAcceptDrops(true)`来接受放下事件，通过设置`setDropIndicatorShown(true)`则可以清晰地看到放下过程中的图标指示。然后实现`dragEnterEvent`、`dropEvent`方法，当用户把一个对象拖动到这个窗体上时，就会调用`dragEnterEvent`，如果对这个事件调用`acceptProposedAction`，就表明可以在这个窗体上拖放对象。默认情况下窗口部件是不接受拖动的，`Qt`会自动改变光标向用户说明这个窗口部件不是有效的放下点。
&emsp;&emsp;我们希望用户拖放的只能是文件，而非其他类型的东西。为了实现这一点，可以检查拖动的`MIME`类型。`MIME`类型中`text/uri-list`用于存储统一资源标识符，它们可以是文件名、统一资源定位器(例如`HTTP`、`FTP`路径)或者其它全局资源标识符。标准的`MIME`类型由国际因特网地址分配委员会`IANA`定义的，它们由类型、子类型信息以及分割两者的斜线组成。`MIME`类通常由剪贴板和拖放系统使用，以识别不同类型的数据。
&emsp;&emsp;当用户在窗口部件上放下一个对象时，就会调用`dropEvent`。我们调用函数`QMimeData::urls`来获得`QUrl`列表。通常情况下，用户一次只拖动一个选择区域来同时拖动多个文件也是可能的，如果要拖放的`URL`不止一个，或者要拖放的`URL`不是一个本地文件名，则会立即返回到原调用处。

``` cpp
void TabelView::dragMoveEvent ( QDragMoveEvent *event ) {
    qDebug() << "drag move";
}
```

&emsp;&emsp;`QWidget`也提供了`dragMoveEvent`和`dragLeveEvent`函数，但是绝大多数情况下并不需要重新实现，上面简单实现了`dragMoveEvent`函数。