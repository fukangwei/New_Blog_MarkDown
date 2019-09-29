---
title: Qt之textEdit总结
categories: Qt语法详解
abbrlink: 90c3b44c
date: 2019-01-02 20:18:03
---
- `QString str = ui->textedit->toPlainText()`：获取普通文本。
- `QString str = ui->textedit->toHtml()`：获取富文本，即获取的是`HTML`字符串。
- `ui->textedit->setPlainText ( "123" )`：设置普通文本。
- `ui->textedit->setHtml ( "<b>123</b>" );`：设置富文本。
- `ui.messageTextEdit->textCursor().insertText ( message + "\n" )`：向`QTextEdit`当前光标位置添加一行字符串`message`。
- `QString content = ui.contentTextEdit->append ( message + "\n" )`：向`QTextEdit`末尾追加一行字符串`message`。
- `ui.textBrowser_2->moveCursor ( QTextCursor::End )`：光标移到末尾。
- `setVisible( bool )`：控件的显示和隐藏。
- `textEdit->document()->isModified()`：判断`textEdit`中的文本是否被改变。