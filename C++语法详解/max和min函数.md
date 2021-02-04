---
title: max和min函数
categories: C++语法详解
date: 2019-02-05 20:21:24
---
### max函数

&emsp;&emsp;This is a function template, in the header file `algorithm`:<!--more-->

``` cpp
template <class T> const T &max ( const T &a, const T &b );
template <class T, class Compare> const T &max ( const T &a, const T &b, Compare comp );
```

Returns the largest of `a` and `b`. If both are equivalent, `a` is returned.
&emsp;&emsp;The function uses `operator<` (or `comp`, if provided) to compare the values. The behavior of this function template is equivalent to:

``` cpp
template <class T> const T &max ( const T &a, const T &b ) {
    return ( a < b ) ? b : a; // or return comp(a, b) ? b : a;
}
```

Example:

``` cpp
#include <iostream> /* std::cout */
#include <algorithm> /* std::max */

int main() {
    std::cout << "max(1, 2) == " << std::max(1, 2) << '\n';
    std::cout << "max(2, 1) == " << std::max(2, 1) << '\n';
    std::cout << "max('a', 'z') == " << std::max('a', 'z') << '\n';
    std::cout << "max(3.14, 2.73) == " << std::max(3.14, 2.73) << '\n';
    return 0;
}
```

Output:

``` cpp
max(1, 2) == 2
max(2, 1) == 2
max('a', 'z') == z
max(3.14, 2.73) == 3.14
```

### min函数

&emsp;&emsp;This is a function template, in the header file `algorithm`:

``` cpp
template <class T> const T &min ( const T &a, const T &b );
template <class T, class Compare> const T &min ( const T &a, const T &b, Compare comp );
```

Returns the smallest of `a` and `b`. If both are equivalent, `a` is returned.
&emsp;&emsp;The function uses `operator<` (or `comp`, if provided) to compare the values. The behavior of this function template is equivalent to:

``` cpp
template <class T> const T &min ( const T &a, const T &b ) {
    return ! ( b < a ) ? a : b; // or return !comp(b, a) ? a : b;
}
```

Example:

``` cpp
#include <iostream> /* std::cout */
#include <algorithm> /* std::min */

int main() {
    std::cout << "min(1, 2) == " << std::min(1, 2) << '\n';
    std::cout << "min(2, 1) == " << std::min(2, 1) << '\n';
    std::cout << "min('a', 'z') == " << std::min('a', 'z') << '\n';
    std::cout << "min(3.14, 2.72) == " << std::min(3.14, 2.72) << '\n';
    return 0;
}
```

Output:

``` cpp
min(1, 2) == 1
min(2, 1) == 1
min('a', 'z') == a
min(3.14, 2.72) == 2.72
```