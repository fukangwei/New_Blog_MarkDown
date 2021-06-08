---
title: Qt之QMessageBox
categories: Qt语法详解
date: 2019-03-17 19:44:36
---
### QMessageBox典型用法

&emsp;&emsp;弹出消息对话框在许多应用中是必需的。它可以提示用户，防止用户因为不小心而犯下错误。

``` cpp
QMessageBox msgBox;
msgBox.setText ( "The document has been modified." );
msgBox.setInformativeText ( "Do you want to save your changes?" );
msgBox.setStandardButtons ( QMessageBox::Save | QMessageBox::Discard | QMessageBox::Cancel );
msgBox.setDefaultButton ( QMessageBox::Save );
int ret = msgBox.exec();

switch ( ret ) {
    case QMessageBox::Save: /* Save was clicked */
        break;
    case QMessageBox::Discard: /* Don't Save was clicked */
        break;
    case QMessageBox::Cancel: /* Cancel was clicked */
        break;
    default: /* should never be reached */
        break;
}
```

&emsp;&emsp;在`QMessageBox`中添加按钮，判断哪一个按钮被按下：

``` cpp
QMessageBox msgBox;
QPushButton *connectButton = msgBox.addButton ( tr ( "Connect" ), QMessageBox::ActionRole );
QPushButton *abortButton = msgBox.addButton ( QMessageBox::Abort );

msgBox.exec();

if ( msgBox.clickedButton() == connectButton ) {
    /* connect */
} else if ( msgBox.clickedButton() == abortButton ) {
    /* abort */
}
```