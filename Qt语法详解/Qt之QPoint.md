---
title: Qt之QPoint
categories: Qt语法详解
abbrlink: 7ef3c5e6
date: 2019-01-23 19:45:45
---
&emsp;&emsp;The `QPoint` class defines a point in the plane using integer precision. The header file is `QPoint`.

### Public Functions

Return     | Function
-----------|----------
           | `QPoint()`
           | `QPoint(int x, int y)`
`bool`     | `isNull() const`
`int`      | `manhattanLength() const`
`int &`    | `rx()`
`int &`    | `ry()`
`void`     | `setX(int x)`
`void`     | `setY(int y)`
`int`      | `x() const`
`int`      | `y() const`
`QPoint &` | `operator*=(float factor)`
`QPoint &` | `operator*=(double factor)`
`QPoint &` | `operator*=(int factor)`
`QPoint &` | `operator+=(const QPoint & point)`
`QPoint &` | `operator-=(const QPoint & point)`
`QPoint &` | `operator/=(qreal divisor)`

### Related Non-Members

Return          | Function
----------------|---------
`bool`          | `operator!=(const QPoint & p1, const QPoint & p2)`
`const QPoint`  | `operator*(const QPoint & point, float factor)`
`const QPoint`  | `operator*(float factor, const QPoint & point)`
`const QPoint`  | `operator*(double factor, const QPoint & point)`
`const QPoint`  | `operator*(int factor, const QPoint & point)`
`const QPoint`  | `operator*(const QPoint & point, double factor)`
`const QPoint`  | `operator*(const QPoint & point, int factor)`
`const QPoint`  | `operator+(const QPoint & p1, const QPoint & p2)`
`const QPoint`  | `operator-(const QPoint & p1, const QPoint & p2)`
`const QPoint`  | `operator-(const QPoint & point)`
`const QPoint`  | `operator/(const QPoint & point, qreal divisor)`
`QDataStream &` | `operator<<(QDataStream & stream, const QPoint & point)`
`bool`          | `operator==(const QPoint & p1, const QPoint & p2)`
`QDataStream &` | `operator>>(QDataStream & stream, QPoint & point)`

### Detailed Description

&emsp;&emsp;The `QPoint` class defines a point in the plane using integer precision.
&emsp;&emsp;A point is specified by a `x` coordinate and an `y` coordinate which can be accessed using the `x()` and `y()` functions. The `isNull()` function returns `true` if both `x` and `y` are set to `0`. The coordinates can be set (or altered) using the `setX()` and `setY()` functions, or alternatively the `rx()` and `ry()` functions which return references to the coordinates (allowing direct manipulation).
&emsp;&emsp;Given a point `p`, the following statements are all equivalent:

``` cpp
QPoint p;

p.setX(p.x() + 1);
p += QPoint(1, 0);
p.rx()++;
```

&emsp;&emsp;A `QPoint` object can also be used as a vector: Addition and subtraction are defined as for vectors (each component is added separately). A `QPoint` object can also be divided or multiplied by an `int` or a `qreal`.
&emsp;&emsp;In addition, the `QPoint` class provides the `manhattanLength()` function which gives an inexpensive approximation of the length of the `QPoint` object interpreted as a vector. Finally, `QPoint` objects can be streamed as well as compared.

### Member Function Documentation

- `QPoint::QPoint()`: Constructs a null point, i.e. with coordinates `(0, 0)`.
- `QPoint::QPoint(int x, int y)`: Constructs a point with the given coordinates `(x, y)`.
- `bool QPoint::isNull() const`: Returns `true` if both the `x` and `y` coordinates are set to `0`, otherwise returns `false`.
- `int QPoint::manhattanLength() const`: Returns the sum of the absolute values of `x()` and `y()`, traditionally known as the `Manhattan length` of the vector from the origin to the point. For example:

``` cpp
QPoint oldPosition;
​
MyWidget::mouseMoveEvent ( QMouseEvent *event ) {
    QPoint point = event->pos() - oldPosition;
​
    if ( point.manhattanLength() > 3 )
        // the mouse has moved more than 3 pixels since the oldPosition
}
```

This is a useful, and quick to calculate, approximation to the true length:

``` cpp
double trueLength = sqrt ( pow ( x(), 2 ) + pow ( y(), 2 ) );
```

The tradition of `Manhattan length` arises because such distances apply to travelers who can only travel on a rectangular grid, like the streets of `Manhattan`.

- `int & QPoint::rx()`: Returns a reference to the `x` coordinate of this point. Using a reference makes it possible to directly manipulate `x`. For example:

``` cpp
QPoint p ( 1, 2 );
p.rx()--; /* p becomes (0, 2) */
```

- `int & QPoint::ry()`: Returns a reference to the `y` coordinate of this point. Using a reference makes it possible to directly manipulate `y`. For example:

``` cpp
QPoint p ( 1, 2 );
p.ry()++; /* p becomes (1, 3) */
```

- `void QPoint::setX(int x)`: Sets the `x` coordinate of this point to the given `x` coordinate.
- `void QPoint::setY(int y)`: Sets the `y` coordinate of this point to the given `y` coordinate.
- `int QPoint::x() const`: Returns the `x` coordinate of this point.
- `int QPoint::y() const`: Returns the `y` coordinate of this point.
- `QPoint & QPoint::operator*=(float factor)`: Multiplies this point's coordinates by the given `factor`, and returns a reference to this point. Note that the result is rounded to the nearest integer as points are held as integers. Use `QPointF` for floating point accuracy.
- `QPoint & QPoint::operator*=(double factor)`: Multiplies this point's coordinates by the given `factor`, and returns a reference to this point. For example:

``` cpp
QPoint p ( -1, 4 );
p *= 2.5; /* p becomes (-3, 10) */
```

Note that the result is rounded to the nearest integer as points are held as integers. Use `QPointF` for floating point accuracy.

- `QPoint & QPoint::operator*=(int factor)`: Multiplies this point's coordinates by the given `factor`, and returns a reference to this point.
- `QPoint & QPoint::operator+=(const QPoint & point)`: Adds the given `point` to this `point` and returns a reference to this `point`. For example:

``` cpp
QPoint p ( 3, 7 );
QPoint q ( -1, 4 );
p += q; /* p becomes (2, 11) */
```

- `QPoint & QPoint::operator-=(const QPoint & point)`: Subtracts the given `point` from this `point` and returns a reference to this `point`. For example:

``` cpp
QPoint p ( 3, 7 );
QPoint q ( -1, 4 );
p -= q; /* p becomes (4, 3) */
```

- `QPoint & QPoint::operator/=(qreal divisor)`: This is an overloaded function. Divides both `x` and `y` by the given `divisor`, and returns a reference to this point. For example:

``` cpp
QPoint p ( -3, 10 );
p /= 2.5; /* p becomes (-1, 4) */
```

Note that the result is rounded to the nearest integer as points are held as integers. Use `QPointF` for floating point accuracy.

### Related Non-Members

- `bool operator!=(const QPoint & p1, const QPoint & p2)`: Returns `true` if `p1` and `p2` are not equal; otherwise returns `false`.
- `const QPoint operator*(const QPoint & point, float factor)`: Returns a copy of the given `point` multiplied by the given `factor`. Note that the result is rounded to the nearest integer as points are held as integers. Use `QPointF` for floating `point` accuracy.
- `const QPoint operator*(float factor, const QPoint & point)`: This is an overloaded function. Returns a copy of the given `point` multiplied by the given `factor`.
- `const QPoint operator*(double factor, const QPoint & point)`: This is an overloaded function. Returns a copy of the given `point` multiplied by the given `factor`.
- `const QPoint operator*(int factor, const QPoint & point)`: This is an overloaded function. Returns a copy of the given `point` multiplied by the given `factor`.
- `const QPoint operator*(const QPoint & point, double factor)`: Returns a copy of the given `point` multiplied by the given `factor`. Note that the result is rounded to the nearest integer as points are held as integers. Use `QPointF` for floating point accuracy.
- `const QPoint operator*(const QPoint & point, int factor)`: Returns a copy of the given `point` multiplied by the given `factor`.
- `const QPoint operator+(const QPoint & p1, const QPoint & p2)`: Returns a `QPoint` object that is the sum of the given points, `p1` and `p2`; each component is added separately.
- `const QPoint operator-(const QPoint & p1, const QPoint & p2)`: Returns a `QPoint` object that is formed by subtracting `p2` from `p1`; each component is subtracted separately.
- `const QPoint operator-(const QPoint & point)`: This is an overloaded function. Returns a `QPoint` object that is formed by changing the sign of both components of the given `point`. Equivalent to `QPoint(0,0) - point`.
- `const QPoint operator/(const QPoint & point, qreal divisor)`: Returns the `QPoint` formed by dividing both components of the given `point` by the given `divisor`. Note that the result is rounded to the nearest integer as points are held as integers. Use `QPointF` for floating point accuracy.
- `QDataStream & operator<<(QDataStream & stream, const QPoint & point)`: Writes the given `point` to the given `stream` and returns a reference to the `stream`.
- `bool operator==(const QPoint & p1, const QPoint & p2)`: Returns `true` if `p1` and `p2` are equal; otherwise returns `false`.
- `QDataStream & operator>>(QDataStream & stream, QPoint & point)`: Reads a `point` from the given `stream` into the given `point` and returns a reference to the `stream`.