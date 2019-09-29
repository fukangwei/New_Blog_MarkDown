---
title: QT信号和槽的原理
categories: Qt语法详解
abbrlink: 7bd944bb
date: 2019-01-27 18:19:25
---
&emsp;&emsp;信号(`SIGNAL`)和槽(`SLOT`)是`Qt`编程的一个重要部分。这个机制可以在对象之间彼此并不了解的情况下将它们的行为联系起来。
&emsp;&emsp;槽和普通的`C++`成员函数很像，它可以像任何`C++`成员函数一样被调用，可以传递任何类型的参数。不同之处在于一个槽函数能和一个信号相连接，只要信号发出了，这个槽函数就会自动被调用，这个任务是由`connect`函数来实现的。
&emsp;&emsp;`connect`函数语法如下：

``` cpp
connect ( sender, SIGNAL ( signal ), receiver, SLOT ( slot ) );
```

`sender`和`receiver`是`QObject`对象指针，`signal`和`slot`是不带参数的函数原型。宏`SIGNAL`和`SLOT`的作用是把它们转换成字符串。
&emsp;&emsp;信号和槽的一些使用规则如下：

- 一个信号可以连接到多个槽，当信号发出后，槽函数都会被调用，但是调用的顺序是随机的、不确定的：

``` cpp
connect ( slider, SIGNAL ( valueChanged ( int ) ), \
          spinBox, SLOT ( setValue ( int ) ) );
connect ( slider, SIGNAL ( valueChanged ( int ) ), \
          this, SLOT ( updateStatusBarIndicator ( int ) ) );
```

- 多个信号可以连接到一个槽，任何一个信号发出，槽函数都会执行：

``` cpp
connect ( lcd, SIGNAL ( overflow() ), this, SLOT ( handleMathError() ) );
connect ( calculator, SIGNAL ( divisionByZero() ), this, SLOT ( handleMathError() ) );
```

- 一个信号可以和另一个信号相连，第一个信号发出后，第二个信号也同时发送：

``` cpp
connect ( lineEdit, SIGNAL ( textChanged ( const QString & ) ), \
          this, SIGNAL ( updateRecord ( const QString & ) ) );
```

- 连接可以被删除，这个函数很少使用，一个对象删除后，`Qt`自动删除这个对象的所有连接：

``` cpp
disconnect ( lcd, SIGNAL ( overflow() ), this, SLOT ( handleMathError() ) );
```

- 信号和槽函数必须有着相同的参数类型，这样信号和槽函数才能成功连接：

``` cpp
connect ( ftp, SIGNAL ( rawCommandReply ( int, const QString & ) ), \
          this, SLOT ( processReply ( int, const QString & ) ) );
```

- 如果信号里的参数个数多于槽函数的参数，多余的参数被忽略：

``` cpp
connect ( ftp, SIGNAL ( rawCommandReply ( int, const QString & ) ), \
          this, SLOT ( checkErrorCode ( int ) ) );
```

&emsp;&emsp;大多数情况下使用控件的信号和槽，实际上信号和槽机制在`QObject`中就实现了，也可以实现在任何从`QObject`继承的子类中：

``` cpp
class Employee : public QObject {
    Q_OBJECT
public:
    Employee() {
        mySalary = 0;
    }

    int salary() const {
        return mySalary;
    }
public slots:
    void setSalary ( int newSalary );
signals:
    void salaryChanged ( int newSalary );
private:
    int mySalary;
};

void Employee::setSalary ( int newSalary ) {
    if ( newSalary != mySalary ) {
        mySalary = newSalary;
        emit salaryChanged ( mySalary );
    }
}
```

注意，只有`newSalary != mySalary`时才发出`salaryChanged`信号，这样避免了死循环的出现。

---

### 概述

&emsp;&emsp;信号和槽是一种高级接口，应用于对象之间的通信，它是`QT`的核心特性。它独立于标准的`C/C++`语言，因此要正确的处理信号和槽，必须借助一个称为`moc(Meta Object Compiler)`的`QT`工具，该工具是一个`C++`预处理程序，它为高层次的事件处理自动生成所需要的附加代码。
&emsp;&emsp;在我们所熟知的很多`GUI`工具包中，窗口小部件(`widget`)都有一个回调函数用于响应它们能触发的每个动作，这个回调函数通常是一个指向某个函数的指针。但是`QT`的信号和槽取代了这些凌乱的函数指针，使得编写这些通信程序更为简单。
&emsp;&emsp;所有从`QObject`或其子类(例如`Qwidget`)派生的类都能够包含信号和槽。当对象改变其状态时，信号就由该对象发射(`emit`)出去，这就是对象所要做的全部事情，它不知道另一端是谁在接收这个信号。这就是真正的信息封装，它确保对象被当作一个真正的软件组件来使用。槽用于接收信号，但它们是普通的对象成员函数。一个槽并不知道是否有任何信号与自己相连接。，而且对象并不了解具体的通信机制。
&emsp;&emsp;你可以将很多信号与单个的槽进行连接，也可以将单个的信号与很多的槽进行连接，甚至于将一个信号与另外一个信号相连接也是可能的，这时无论第一个信号什么时候发射，系统都将立刻发射第二个信号。总之，信号与槽构造了一个强大的部件编程机制。

### 信号

&emsp;&emsp;当某个信号对其客户或所有者的内部状态发生改变时，信号就会被一个对象发射，只有定义过这个信号的类及其派生类才能够发射这个信号。当一个信号被发射时，与其相关联的槽将被立刻执行，就像一个正常的函数调用一样。`信号-槽`机制完全独立于任何`GUI`事件循环。只有当所有的槽返回以后，发射函数(`emit`)才返回。如果存在多个槽与某个信号相关联，那么当这个信号被发射时，这些槽将会一个接一个地执行，但是它们执行的顺序将会是随机的、不确定的。
&emsp;&emsp;信号的声明是在头文件中进行的，`QT`的`signals`关键字指出进入了信号声明区，随后即可声明自己的信号。例如下面定义了三个信号：

``` cpp
signals:
    void mySignal();
    void mySignal ( int x );
    void mySignalParam ( int x, int y );
```

其中`signals`是`QT`的关键字，`void mySignal()`定义了信号`mySignal`，这个信号没有携带参数；`void mySignal(int x)`定义了重名信号`mySignal`，但是它携带一个整型参数，有点类似于`C++`中的重载函数。从形式上，信号的声明与普通的`C++`函数是一样的，但是信号却没有函数体定义。另外，信号的返回类型都是`void`，不要指望能从信号返回什么有用信息。信号由`moc`自动产生，它们不应该在`.cpp`文件中实现。

### 槽

&emsp;&emsp;槽是普通的`C++`成员函数，可以被正常调用，它们唯一的特殊性就是很多信号可以与其相关联。当与其关联的信号被发射时，这个槽就会被调用。槽可以有参数，但槽的参数不能有缺省值。
&emsp;&emsp;既然槽是普通的成员函数，因此与其它的函数一样，它们也有权限，槽的权限决定了谁能够与其相关联。同普通的`C++`成员函数一样，槽函数也分为三种类型，即`public slots`、`private slots`和`protected slots`。

- `public slots`：在这个区内声明的槽意味着任何对象都可将信号与之相连接。
- `protected slots`：在这个区内声明的槽意味着当前类及其子类可以将信号与之相连接。
- `private slots`：在这个区内声明的槽意味着只有类自己可以将信号与之相连接。

槽也能够声明为虚函数，这也是非常有用的。槽的声明也是在头文件中进行的，例如下面声明了三个槽：

``` cpp
public slots:
    void mySlot();
    void mySlot ( int x );
    void mySignalParam ( int x, int y );
```

### 信号与槽的关联

&emsp;&emsp;通过调用`QObject`对象的`connect`函数来将某个对象的信号与另外一个对象的槽函数相关联，这样当发射者发射信号时，接收者的槽函数将被调用。该函数的定义如下：

``` cpp
bool QObject::connect ( const QObject *sender, const char *signal, \
                        const QObject *receiver, const char *member ) [static]
```

这个函数的作用就是将发射者`sender`对象中的信号`signal`与接收者`receiver`中的`member`槽函数联系起来。指定信号`signal`时必须使用`QT`的宏`SIGNAL`，指定槽函数时必须使用宏`SLOT`。如果发射者与接收者属于同一个对象的话，那么在`connect`调用中接收者参数可以省略。例如下面定义了两个对象即标签对象`label`和滚动条对象`scroll`，并将`valueChanged`信号与标签对象的`setNum`相关联，信号还携带了一个整型参数，这样标签总是显示滚动条所处位置的值。

``` cpp
QLabel *label = new QLabel;
QScrollBar *scroll = new QScrollBar;
QObject::connect ( scroll, SIGNAL ( valueChanged ( int ) ), label, SLOT ( setNum ( int ) ) );
```

一个信号甚至能够与另一个信号相关联：

``` cpp
class MyWidget : public QWidget {
public:
    MyWidget();
signals:
    void aSignal();​
private:
    QPushButton *aButton;
};

MyWidget::MyWidget() {
    aButton = new QPushButton ( this );
    connect ( aButton, SIGNAL ( clicked() ), this, SIGNAL ( aSignal() ) );
}
```

在上面的构造函数中，`MyWidget`创建了一个私有的按钮`aButton`，按钮的单击事件产生的信号`clicked`与另外一个信号`aSignal`进行了关联。当信号`clicked`被发射时，信号`aSignal`也接着被发射。当然也可以直接将单击事件与某个私有的槽函数相关联，然后在槽中发射`aSignal`信号。
&emsp;&emsp;当信号与槽没有必要继续保持关联时，可以使用`disconnect`函数来断开连接：

``` cpp
bool QObject::disconnect ( const QObject *sender, const char *signal, \
                           const Object *receiver, const char *member ) [static]
```

### 元对象工具

&emsp;&emsp;元对象编译器`moc`对`C++`文件中的类声明进行分析，并产生用于初始化元对象的`C++`代码，元对象包含全部信号和槽的名字以及指向这些函数的指针。
&emsp;&emsp;`moc`读取`C++`源文件，如果发现有`Q_OBJECT`宏声明的类，它就会生成另外一个`C++`源文件，这个新生成的文件中包含有该类的元对象代码。假设有一个头文件`mysignal.h`，在这个文件中包含有信号或槽的声明，那么在编译之前，`moc`工具就会根据该文件自动生成一个名为`mysignal.moc.h`的`C++`源文件，并将其提交给编译器；类似地，对应于`mysignal.cpp`文件，`moc`工具将自动生成一个名为`mysignal.moc.cpp`文件，并提交给编译器。

### 程序样例

&emsp;&emsp;信号和槽函数的声明一般位于头文件中，同时在类声明的开始位置必须加上`Q_OBJECT`语句。这条语句是不可缺少的，它将告诉编译器在编译之前必须先应用`moc`工具进行扩展。关键字`signals`指出随后开始信号的声明，`siganls`没有`public`、`private`、`protected`等属性，这点不同于`slots`。另外，`signals`、`slots`关键字是`QT`自己定义的，不是`C++`中的关键字。
&emsp;&emsp;信号的声明类似于函数的声明而非变量的声明，左边要有类型，右边要有括号。如果要向槽中传递参数的话，在括号中指定每个形参的类型，当然形参的个数可以多于一个。
&emsp;&emsp;关键字`slots`指出随后开始槽的声明，槽的声明与普通函数的声明一样，可以携带零或多个形参。既然信号的声明类似于普通`C++`函数的声明，那么信号也可采用`C++`中重载函数的形式进行声明。例如第一次定义的`void mySignal()`没有带参数，而第二次定义的却带有参数，从这里可以看到`QT`的信号机制是非常灵活的。

``` cpp
/* tsignal.h */
class TsignalApp: public QMainWindow {
    Q_OBJECT
    ...
signals: /* 信号声明区 */
    void mySignal();                     /* 声明信号mySignal()              */
    void mySignal ( int x );             /* 声明信号mySignal(int)           */
    void mySignalParam ( int x, int y ); /* 声明信号mySignalParam(int, int) */
public slots: /* 槽声明区 */
    void mySlot();                       /* 声明槽函数mySlot()                 */
    void mySlot ( int x );               /* 声明槽函数mySlot(int)              */
    void mySignalParam ( int x, int y ); /* 声明槽函数mySignalParam (int, int) */
}
​
/* tsignal.cpp */
TsignalApp::TsignalApp() {
    /* 将信号mySignal与槽mySlot相关联 */
    connect ( this, SIGNAL ( mySignal() ), this, SLOT ( mySlot() ) );
    /* 将信号mySignal(int)与槽mySlot(int)相关联 */
    connect ( this, SIGNAL ( mySignal ( int ) ), \
              this, SLOT ( mySlot ( int ) ) );
    /* 将信号mySignalParam(int, int)与槽mySlotParam(int, int)相关联 */
    connect ( this, SIGNAL ( mySignalParam ( int, int ) ), \
              this, SLOT ( mySlotParam ( int, int ) ) );
}
​
void TsignalApp::mySlot() { /* 定义槽函数mySlot */
    QMessageBox::about ( this, "Tsignal", "signal/slot sample without parameter." );
}
​
void TsignalApp::mySlot ( int x ) { /* 定义槽函数mySlot(int) */
    QMessageBox::about ( this, "Tsignal", "signal/slot sample with one parameter." );
}
​
void TsignalApp::mySlotParam ( int x, int y ) { /* 定义槽函数mySlotParam(int, int) */
    char s[256];
    sprintf ( s, "x:%d y:%d", x, y );
    QMessageBox::about ( this, "Tsignal", s );
}

void TsignalApp::slotFileNew() {
    emit mySignal();               /* 发射信号mySignal                */
    emit mySignal ( 5 );           /* 发射信号mySignal(int)           */
    emit mySignalParam ( 5, 100 ); /* 发射信号mySignalParam(int, int) */
}
```

### 应注意的问题

&emsp;&emsp;信号与槽机制是比较灵活的，但有些局限性必须要了解，这样在实际使用过程中避免产生错误。
&emsp;&emsp;1. 信号与槽的效率是非常高的，但是同真正的回调函数比较起来，由于增加了灵活性，因此在速度上还是有所损失。当然这种损失相对来说是比较小的，在一台`i586`的机器上测试是`10`微秒，可见这种机制所提供的简洁性、灵活性还是值得的。但如果要追求高效率的话，比如在实时系统中就要尽可能地少用这种机制。
&emsp;&emsp;2. 信号与槽机制与普通函数的调用一样，如果使用不当的话，在程序执行时也有可能产生死循环。因此在定义槽函数时一定要注意避免间接形成无限循环，即在槽中再次发射所接收到的同样信号。例如，如果在`mySlot()`槽函数中加上语句`emit mySignal()`即可形成死循环。
&emsp;&emsp;3. 如果一个信号与多个槽相联系的话，那么当这个信号被发射时，与之相关的槽被激活的顺序将是随机的。
&emsp;&emsp;4. 宏定义不能用在`signal`和`slot`的参数中。既然`moc`工具不扩展`define`，因此在`signals`和`slots`中携带参数的宏就不能正确地工作，如果不带参数则是可以的。例如，下面的例子中将带有参数的宏`SIGNEDNESS(a)`作为信号的参数是不合语法的：

``` cpp
#ifdef ultrix
    #define SIGNEDNESS(a) unsigned a
#else
    #define SIGNEDNESS(a) a
#endif
​
class Whatever : public QObject {
signals:
    void someSignal ( SIGNEDNESS ( a ) );
};
```

&emsp;&emsp;5. 构造函数不能用在`signals`或者`slots`声明区域内。下面的用法是不符合语法要求的：

``` cpp
class SomeClass : public QObject {
    Q_OBJECT
public slots:
    SomeClass ( QObject *parent, const char *name ) : QObject ( parent, name ) {}
};
```

&emsp;&emsp;6. 函数指针不能作为信号或槽的参数。下面的例子中将`void (* applyFunction)(QList *, void *)`作为参数是不符合语法的：

``` cpp
class someClass : public QObject {
    Q_OBJECT
public slots:
    void apply ( void ( *applyFunction ) ( QList *, void * ), char * );
};
```

你可以采用下面的方法绕过这个限制：

``` cpp
typedef void ( *ApplyFunctionType ) ( QList *, void * );
​
class someClass : public QObject {
    Q_OBJECT
public slots:
    void apply ( ApplyFunctionType, char * );
};
```

&emsp;&emsp;7. 信号与槽不能有缺省参数。下面的用法是不合理的：

``` cpp
class SomeClass : public QObject {
    Q_OBJECT
public slots:
    /* 将x的缺省值定义成100，在槽函数声明中使用是错误的 */
    void someSlot ( int x = 100 );
};
```

&emsp;&emsp;8. 信号与槽也不能携带模板类参数。如果将信号、槽声明为模板类参数的话，即使`moc`工具不报告错误，也不可能得到预期的结果。下面的例子中，当信号发射时，槽函数不会被正确调用：

``` cpp
public slots:
    void MyWidget::setLocation ( pair<int, int> location );
public signals:
    void MyObject::moved ( pair<int, int> location );
```

但是可以使用`typedef`语句来绕过这个限制：

``` cpp
typedef pair<int, int> IntPair;
​
public slots:
    void MyWidget::setLocation ( IntPair location );
public signals:
    void MyObject::moved ( IntPair location );
```

&emsp;&emsp;9. 嵌套的类不能位于信号或槽区域内，也不能有信号或者槽。下面的例子中，在`class B`中声明槽`b`是不符合语法的，在信号区内声明槽`b`也是不符合语法的：

``` cpp
class A {
    Q_OBJECT
public:
    class B {
    public slots: /* 在嵌套类中声明槽不合语法 */
        void b();
    };
signals:
    class B {
        void b(); /* 在信号区内声明嵌套类不合语法 */
    }:
};
```

&emsp;&emsp;10. 友元声明不能位于信号或者槽声明区内，它们应该在普通`C++`的`private`、`protected`或者`public`区内进行声明。下面的例子是不符合语法规范的：

``` cpp
class someClass : public QObject {
    Q_OBJECT
signals: /* 信号定义区 */
    friend class ClassTemplate; /* 此处定义不合语法 */
};
```