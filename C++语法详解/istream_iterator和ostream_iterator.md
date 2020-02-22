---
title: istream_iterator和ostream_iterator
categories: C++语法详解
date: 2019-03-11 12:10:03
---
### std::istream_iterator

&emsp;&emsp;It is a lass template and it is included in `iterator`.<!--more-->

``` cpp
template <class T, class charT = char, class traits = char_traits<charT>, class Distance = ptrdiff_t>
class istream_iterator;
```

#### Istream iterator

&emsp;&emsp;Istream iterators are input iterators that read successive elements from an input stream (such as `cin`).
&emsp;&emsp;They are constructed from a `basic_istream` object, to which they become associated, so that whenever `operator++` is used on the iterator, it extracts an element from the stream (using `operator>>`).
&emsp;&emsp;This kind of iterator has a special state as an `end-of-stream` iterator, which is acquired if an input operations fails (as returned by fail after an operation with the associated stream), and is also the resulting value of a `default-constructed` object.

#### Template parameters

- `T`: Element type for the iterator: The type of elements extracted from the stream.
- `charT`: First template parameter of the associated `basic_istream` object: The type of elements the stream handles (char for `istream`).
- `traits`: Second template parameter of the associated `basic_istream`: Character traits for the elements the stream handles.
- `Distance`: Type to represent the difference between two iterators (generally a signed integral type, such as `ptrdiff_t`).

**Note**: The default template arguments correspond to an instantiation that uses an `istream` object as associated stream.

#### Member types

member              | definition in istream_iterator | description
--------------------|--------------------------------|--------------
`istream_type`      | `basic_istream<charT, traits>` | Type of the associated input stream
`iterator_category` | `input_iterator_tag`           | Input iterator
`value_type`        | `T`                            | Type of the elements pointed by the iterator
`char_type`         | `charT`                        | Type of the characters handled by the associated stream
`traits_type`       | `traits`                       | Character traits for associated stream
`difference_type`   | `Distance`                     | Distance type
`pointer`           | `const T*`                     |
`reference`         | `const T&`                     |

#### Member functions

Return          | Function
----------------|---------
`(constructor)` | Construct istream iterator (public member function)
`operator*`     | Dereference iterator (public member function)
`operator->`    | Dereference iterator (public member function)
`operator++`    | Increment iterator position (public member function)

#### Example

``` cpp
#include <iostream> // std::cin, std::cout
#include <iterator> // std::istream_iterator

int main () {
    double value1, value2;
    std::cout << "Please, insert two values: ";
    std::istream_iterator<double> eos;              // end-of-stream iterator
    std::istream_iterator<double> iit ( std::cin ); // stdin iterator

    if ( iit != eos ) {
        value1 = *iit;
    }

    ++iit;

    if ( iit != eos ) {
        value2 = *iit;
    }

    std::cout << value1 << "*" << value2 << "=" << ( value1 * value2 ) << '\n';
    return 0;
}
```

Possible output:

``` cpp
Please, insert two values: 2 32
2*32=64
```

---

### std::ostream_iterator

&emsp;&emsp;It is a class template and it is included in `iterator`.

``` cpp
template <class T, class charT = char, class traits = char_traits<charT> >
class ostream_iterator;
```

#### Ostream iterator

&emsp;&emsp;Ostream iterators are output iterators that write sequentially to an output stream (such as `cout`).
&emsp;&emsp;They are constructed from a `basic_ostream` object, to which they become associated, so that whenever an assignment operator `=` is used on the `ostream_iterator` (dereferenced or not) it inserts a new element into the stream.
&emsp;&emsp;Optionally, a delimiter can be specified on construction. This delimiter is written to the stream after each element is inserted.

#### Template parameters

- `T`: Element type for the iterator: The type of elements inserted into the stream.
- `charT`: First template parameter of the associated `basic_ostream` object: The type of elements the stream handles (`char` for `ostream`).
- `traits`: Second template parameter of the associated `basic_ostream`: Character traits for the elements the stream handles.

**Note**: The default template arguments correspond to an instantiation that uses an ostream object as associated stream.

#### Member types

member              | definition in istream_iterator | description
--------------------|--------------------------------|--------------
`ostream_type`      | `basic_ostream<charT, traits>` | Type of the associated output stream
`iterator_category` | `output_iterator_tag`          | Input iterator
`value_type`        | `void`                         |
`char_type`         | `charT`                        | Type of the characters handled by the associated stream
`traits_type`       | `traits`                       | Character traits for associated stream
`difference_type`   | `void`                         |
`pointer`           | `void`                         |
`reference`         | `void`                         |

#### Member functions

Return          | Function
----------------|---------
`(constructor)` | Construct ostream iterator (public member function)
`operator*`     | Dereference iterator (public member function)
`operator++`    | Increment iterator (public member function)
`operator=`     | Assignment operator (public member function)

#### Example

``` cpp
#include <iostream>  // std::cout
#include <iterator>  // std::ostream_iterator
#include <vector>    // std::vector
#include <algorithm> // std::copy

int main () {
    std::vector<int> myvector;

    for ( int i = 1; i < 10; ++i ) {
        myvector.push_back ( i * 10 );
    }

    std::ostream_iterator<int> out_it ( std::cout, ", " );
    std::copy ( myvector.begin(), myvector.end(), out_it );
    return 0;
}
```

Possible output:

``` cpp
10, 20, 30, 40, 50, 60, 70, 80, 90,
```