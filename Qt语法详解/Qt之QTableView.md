---
title: Qt之QTableView
categories: Qt语法详解
abbrlink: e8baa847
date: 2019-01-22 15:15:18
---
&emsp;&emsp;`QTableView`常用于实现数据的表格显示。
&emsp;&emsp;1. 添加表头：

``` cpp
/* 准备数据模型 */
QStandardItemModel *student_model = new QStandardItemModel();
student_model->setHorizontalHeaderItem ( 0, new QStandardItem ( QObject::tr ( "Name" ) ) );
student_model->setHorizontalHeaderItem ( 1, new QStandardItem ( QObject::tr ( "NO." ) ) );
student_model->setHorizontalHeaderItem ( 2, new QStandardItem ( QObject::tr ( "Sex" ) ) );
student_model->setHorizontalHeaderItem ( 3, new QStandardItem ( QObject::tr ( "Age" ) ) );
student_model->setHorizontalHeaderItem ( 4, new QStandardItem ( QObject::tr ( "College" ) ) );
/* 利用setModel方法将数据模型与QTableView绑定 */
ui->student_tableview->setModel ( student_model );
```

&emsp;&emsp;2. 设置表格属性：

``` cpp
/* 设置列宽不可变动，即不能通过鼠标拖动增加列宽 */
ui->student_tableview->horizontalHeader()->setResizeMode ( 0, QHeaderView::Fixed );
ui->student_tableview->horizontalHeader()->setResizeMode ( 1, QHeaderView::Fixed );
ui->student_tableview->horizontalHeader()->setResizeMode ( 2, QHeaderView::Fixed );
ui->student_tableview->horizontalHeader()->setResizeMode ( 3, QHeaderView::Fixed );
ui->student_tableview->horizontalHeader()->setResizeMode ( 4, QHeaderView::Fixed );
/* 设置表格的各列的宽度值 */
ui->student_tableview->setColumnWidth ( 0, 100 );
ui->student_tableview->setColumnWidth ( 1, 100 );
ui->student_tableview->setColumnWidth ( 2, 100 );
ui->student_tableview->setColumnWidth ( 3, 100 );
ui->student_tableview->setColumnWidth ( 4, 100 );
/* 默认显示行头，如果你觉得不美观的话，我们可以将隐藏 */
ui->student_tableview->verticalHeader()->hide();
/* 设置选中时为整行选中 */
ui->student_tableview->setSelectionBehavior ( QAbstractItemView::SelectRows );
/* 设置表格的单元为只读属性，即不能编辑 */
ui->student_tableview->setEditTriggers ( QAbstractItemView::NoEditTriggers );
/* 如果你用在QTableView中使用右键菜单，需启用该属性 */
ui->tstudent_tableview->setContextMenuPolicy ( Qt::CustomContextMenu );
```

&emsp;&emsp;3. 动态添加行：在表格中添加行时，我们只需要在`model`中插入数据即可，一旦`model`中的数据发生变化，`QTabelView`显示就会做相应的变动：

``` cpp
/* 在第一行添加学生张三的个人信息(setItem函数的第一个参数
   表示行号，第二个表示列号，第三个为要显示的数据) */
student_model->setItem ( 0, 0, new QStandardItem ( "张三" ) );
student_model->setItem ( 0, 1, new QStandardItem ( "20120202" ) );
student_model->setItem ( 0, 2, new QStandardItem ( "男" ) );
student_model->setItem ( 0, 3, new QStandardItem ( "18" ) );
student_model->setItem ( 0, 4, new QStandardItem ( "土木学院" ) );
```

&emsp;&emsp;4. 设置数据显示的样式：

``` cpp
/* 设置单元格文本居中，张三的数据设置为居中显示 */
student_model->item ( 0, 0 )->setTextAlignment ( Qt::AlignCenter );
student_model->item ( 0, 1 )->setTextAlignment ( Qt::AlignCenter );
student_model->item ( 0, 2 )->setTextAlignment ( Qt::AlignCenter );
student_model->item ( 0, 3 )->setTextAlignment ( Qt::AlignCenter );
student_model->item ( 0, 4 )->setTextAlignment ( Qt::AlignCenter );
/* 设置单元格文本颜色，张三的数据设置为红色 */
student_model->item ( 0, 0 )->setForeground ( QBrush ( QColor ( 255, 0, 0 ) ) );
student_model->item ( 0, 1 )->setForeground ( QBrush ( QColor ( 255, 0, 0 ) ) );
student_model->item ( 0, 2 )->setForeground ( QBrush ( QColor ( 255, 0, 0 ) ) );
student_model->item ( 0, 3 )->setForeground ( QBrush ( QColor ( 255, 0, 0 ) ) );
student_model->item ( 0, 4 )->setForeground ( QBrush ( QColor ( 255, 0, 0 ) ) );
/* 将字体加粗 */
student_model->item ( 0, 0 )->setFont ( QFont ( "Times", 10, QFont::Black ) );
student_model->item ( 0, 1 )->setFont ( QFont ( "Times", 10, QFont::Black ) );
student_model->item ( 0, 2 )->setFont ( QFont ( "Times", 10, QFont::Black ) );
student_model->item ( 0, 3 )->setFont ( QFont ( "Times", 10, QFont::Black ) );
student_model->item ( 0, 4 )->setFont ( QFont ( "Times", 10, QFont::Black ) );
/* 设置排序方式，按年龄降序显示 */
student_model->sort ( 3, Qt::DescendingOrder );
```