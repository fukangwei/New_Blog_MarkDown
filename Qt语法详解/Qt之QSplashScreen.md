---
title: Qt之QSplashScreen
categories: Qt语法详解
abbrlink: 90f31620
date: 2019-01-27 14:56:49
---
&emsp;&emsp;The `QSplashScreen` widget provides a splash screen that can be shown during application startup. The header file is `QSplashScreen`.

### Public Functions

Return          | Function
----------------|---------
                | `QSplashScreen(const QPixmap & pixmap = QPixmap(), Qt::WindowFlags f = 0)`
                | `QSplashScreen(QWidget * parent, const QPixmap & pixmap = QPixmap(), Qt::WindowFlags f = 0)`
`virtual`       | `~QSplashScreen()`
`void`          | `finish(QWidget * mainWin)`
`const QPixmap` | `pixmap() const`
`void`          | `repaint()`
`void`          | `setPixmap(const QPixmap & pixmap)`

### Public Slots

Return | Function
-------|---------
`void` | `clearMessage()`
`void` | `showMessage(const QString & message, int alignment = Qt::AlignLeft, const QColor & color = Qt::black)`

### Signals

- `void messageChanged(const QString & message)`

### Protected Functions

- `virtual void drawContents(QPainter * painter)`

### Reimplemented Protected Functions

Return         | Function
---------------|----------
`virtual bool` | `event(QEvent * e)`
`virtual void` | `mousePressEvent(QMouseEvent *)`

### Detailed Description

&emsp;&emsp;The `QSplashScreen` widget provides a splash screen that can be shown during application startup.
&emsp;&emsp;A splash screen is a widget that is usually displayed when an application is being started. Splash screens are often used for applications that have long start up times (e.g. database or networking applications that take time to establish connections) to provide the user with feedback that the application is loading.
&emsp;&emsp;The splash screen appears in the center of the screen. It may be useful to add the `Qt::WindowStaysOnTopHint` to the splash widget's window flags if you want to keep it above all the other windows on the desktop.
&emsp;&emsp;Some `X11` window managers do not support the `stays on top` flag. A solution is to set up a timer that periodically calls `raise()` on the splash screen to simulate the `stays on top` effect.
&emsp;&emsp;The most common usage is to show a splash screen before the main widget is displayed on the screen. This is illustrated in the following code snippet in which a splash screen is displayed and some initialization tasks are performed before the application's main window is shown:

``` cpp
int main ( int argc, char *argv[] ) {
    QApplication app ( argc, argv );
    QPixmap pixmap ( ":/splash.png" );
    QSplashScreen splash ( pixmap );
    splash.show();
    app.processEvents();
    ...
    QMainWindow window;
    window.show();
    splash.finish ( &window );
    return app.exec();
}
```

&emsp;&emsp;The user can hide the splash screen by clicking on it with the mouse. Since the splash screen is typically displayed before the event loop has started running, it is necessary to periodically call `QApplication::processEvents()` to receive the mouse clicks.
&emsp;&emsp;It is sometimes useful to update the splash screen with messages, for example, announcing connections established or modules loaded as the application starts up:

``` cpp
QPixmap pixmap ( ":/splash.png" );
QSplashScreen *splash = new QSplashScreen ( pixmap );
splash->show();
... /* Loading some items */
splash->showMessage ( "Loaded modules" );
qApp->processEvents();
... /* Establishing connections */
splash->showMessage ( "Established connections" );
qApp->processEvents();
```

`QSplashScreen` supports this with the `showMessage()` function. If you wish to do your own drawing you can get a pointer to the pixmap used in the splash screen with `pixmap()`. Alternatively, you can subclass `QSplashScreen` and reimplement `drawContents()`.

### Member Function Documentation

- `QSplashScreen::QSplashScreen(const QPixmap & pixmap = QPixmap(), Qt::WindowFlags f = 0)`: Construct a splash screen that will display the `pixmap`. There should be no need to set the widget flags, `f`, except perhaps `Qt::WindowStaysOnTopHint`.
- `QSplashScreen::QSplashScreen(QWidget * parent, const QPixmap & pixmap = QPixmap(), Qt::WindowFlags f = 0)`: This is an overloaded function. This function allows you to specify a `parent` for your splashscreen. The typical use for this constructor is if you have a multiple screens and prefer to have the splash screen on a different screen than your primary one. In that case pass the proper `desktop()` as the `parent`.
- `QSplashScreen::~QSplashScreen() [virtual]`: Destructor.
- `void QSplashScreen::clearMessage() [slot]`: Removes the message being displayed on the splash screen.
- `void QSplashScreen::drawContents(QPainter * painter) [virtual protected]`: Draw the contents of the splash screen using `painter`. The default implementation draws the message passed by `showMessage()`. Reimplement this function if you want to do your own drawing on the splash screen.
- `bool QSplashScreen::event(QEvent * e) [virtual protected]`: Reimplemented from `QObject::event()`.
- `void QSplashScreen::finish(QWidget * mainWin)`: Makes the splash screen wait until the widget `mainWin` is displayed before calling `close()` on itself.
- `void QSplashScreen::messageChanged(const QString & message) [signal]`: This signal is emitted when the `message` on the splash screen changes. `message` is the new message and is a `null-string` when the message has been removed.
- `void QSplashScreen::mousePressEvent(QMouseEvent *) [virtual protected]`: Reimplemented from `QWidget::mousePressEvent()`.
- `const QPixmap QSplashScreen::pixmap() const`: Returns the pixmap that is used in the splash screen. The image does not have any of the text drawn by `showMessage()` calls.
- `void QSplashScreen::repaint()`: This overrides `QWidget::repaint()`. It differs from the standard repaint function in that it also calls `QApplication::flush()` to ensure the updates are displayed, even when there is no event loop present.
- `void QSplashScreen::setPixmap(const QPixmap & pixmap)`: Sets the `pixmap` that will be used as the splash screen's image to pixmap.
- `void QSplashScreen::showMessage(const QString & message, int alignment = Qt::AlignLeft, const QColor & color = Qt::black) [slot]`: Draws the `message` text onto the splash screen with `color` and aligns the text according to the flags in `alignment`. To make sure the splash screen is repainted immediately, you can call `QCoreApplication's` `processEvents()` after the call to `showMessage()`. You usually want this to make sure that the `message` is kept up to date with what your application is doing (e.g., loading files).