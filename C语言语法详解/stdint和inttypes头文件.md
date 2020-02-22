---
title: stdint和inttypes头文件
categories: C语言语法详解
date: 2018-12-13 20:25:31
---
&emsp;&emsp;`stdint.h`是`C99`引进的一个标准`C`库的头文件。`stdint.h`中定义了几种扩展的整数类型和宏，规则如下(其中`N`可以为`8`、`16`、`32`或`64`)：`intN_t`、`int_leastN_t`、`int_fastN_t`表示长度为`N`位的整型数；`uintN_t`、`uint_leastN_t`、`uint_fastN_t`表示长度为`N`位的无符号整型数。<!--more-->
&emsp;&emsp;在`stdint.h`中的常量，定义了以上各类型数的最大最小值(其中`n`可以为`8`、`16`、`32`或`64`)：`INTn_MIN`、`UINTn_MIN`、`INTn_MAX`、`UITn_MAX`、`INT_LEASEn_MIN`、`INT_LEASEn_MAX`、`INT_FASTn_MIN`或`INT_FASTn_MAX`。
&emsp;&emsp;`intN_t`格式的宏指一个有`N`位的整数，例如`int16_t`指一个`16`位的有符号的整数；`uintN_t`格式的宏指定一个有N位的无符号的整数，例如`uint32_t`指定一个`32`位的无符号的整数；`int_leastN_t`格式的宏指定一个至少`N`位的整数；`uint_leastN_t`指定一个至少`N`位的无符号的整数；`int_fastN_t`指定一个至少`N`位的快速有符号的整数；`intmax_t`类型指定一个最大尺寸的有符号整数，而`uintmax_t`指定一个最大尺寸的无符号整数。
&emsp;&emsp;`stdint.h`可以实现将一个常数扩展为特定整数类型的宏，这些宏通用格式为`INTN_C(value)`和`UINTN_C(value)`，其中N是所需类型的位宽度。除此之外，还有`INTMAX_C(value)`和`UINTMAX_C(value)`，它们创建指定值的最大宽度常数。
&emsp;&emsp;注意大数的输出，`int64_t`类型的输出为`%lld`，`uint64_t`类型的输出为`%llu`，`uint64_t`的十六进制输出为`%llx`，`uint64_t`的八进制输出为`%llo`。`stdint.h`文件如下：

``` cpp
/* ISO C99: 整数类型 <stdint.h> */
#ifndef _STDINT_H
#define _STDINT_H 1

#include <features.h>
#include <bits/wchar.h>
#include <bits/wordsize.h>

/* 准确类型：指定长度的准确类型 */
/* 有符号 */
/* 有一些与<sys/types.h>中的inet代码重叠 */
#ifndef __int8_t_defined
#define __int8_t_defined

typedef signed char int8_t; /* 8位的准确长度类型(int8_t = signed char) */
typedef short int   int16_t; /* 16位的准确长度类型 */
typedef int         int32_t;

#if __WORDSIZE == 64
typedef long int int64_t; /* 64位平台上64位的准确类型为long */
#else
__extension__
typedef long long int int64_t; /* 32位平台上64位的准确类型为“long long” */
#endif
#endif

/* 无符号 */
typedef unsigned char      uint8_t;
typedef unsigned short int uint16_t;

#ifndef __uint32_t_defined
typedef unsigned int uint32_t;
#define __uint32_t_defined
#endif

#if __WORDSIZE == 64
typedef unsigned long int uint64_t;
#else
__extension__
typedef unsigned long long int uint64_t;
#endif

/* 最小类型：指定长度的最小类型 */
/* 有符号 */
typedef signed char int_least8_t;
typedef short int   int_least16_t;
typedef int         int_least32_t;

#if __WORDSIZE == 64
typedef long int int_least64_t;
#else
__extension__
typedef long long int int_least64_t;
#endif

/* 无符号 */
typedef unsigned char      uint_least8_t;
typedef unsigned short int uint_least16_t;
typedef unsigned int       uint_least32_t;

#if __WORDSIZE == 64
typedef unsigned long int uint_least64_t;
#else
__extension__
typedef unsigned long long int uint_least64_t;
#endif

/* 快速类型：指定长度的最快类型 */
/* 有符号 */
typedef signed char int_fast8_t;

#if __WORDSIZE == 64 /* 64位平台 */
typedef long int int_fast16_t;
typedef long int int_fast32_t;
typedef long int int_fast64_t;
#else /* 32位平台 */
typedef int int_fast16_t; /* 16位和32位的最快类型均为int */
typedef int int_fast32_t;
__extension__
typedef long long int int_fast64_t;
#endif

/* 无符号 */
typedef unsigned char uint_fast8_t;

#if __WORDSIZE == 64
typedef unsigned long int uint_fast16_t;
typedef unsigned long int uint_fast32_t;
typedef unsigned long int uint_fast64_t;
#else
typedef unsigned int uint_fast16_t;
typedef unsigned int uint_fast32_t;
__extension__
typedef unsigned long long int uint_fast64_t;
#endif

/* 通用指针类型：即“void *”型指针的类型，64位平台上为long，32位平台上为int */
#if __WORDSIZE == 64
#ifndef __intptr_t_defined
typedef long int intptr_t;
#define __intptr_t_defined
#endif

typedef unsigned long int uintptr_t;
#else
#ifndef __intptr_t_defined
typedef int intptr_t;
#define __intptr_t_defined
#endif

typedef unsigned int uintptr_t;
#endif

#if __WORDSIZE == 64 /* 最大类型：指定长度的最大整数类型 */
typedef long int intmax_t; /* 64位平台上的最大整数类型为long */
typedef unsigned long int uintmax_t;
#else
__extension__
typedef long long int intmax_t; /* 32位平台上的最大整数类型为“long long” */
__extension__
typedef unsigned long long int uintmax_t;
#endif

/* ISO C99标准指出，在C++实现中这些宏应该只在被请求到的时候才定义 */
#if !defined __cplusplus || defined __STDC_LIMIT_MACROS
#if __WORDSIZE == 64
#define __INT64_C(c)  c ## L
#define __UINT64_C(c) c ## UL
#else
#define __INT64_C(c)  c ## LL
#define __UINT64_C(c) c ## ULL
#endif

/* 整数类型的范围 */
/* 有符号整数类型的最小值：-2^(N-1)，其中最小负数“-2^(N-1) = 100...0(二进制数)”没有对应正数，其反数还是自己 */
#define INT8_MIN  (-128)
#define INT16_MIN (-32767-1)
#define INT32_MIN (-2147483647-1)
#define INT64_MIN (-__INT64_C(9223372036854775807)-1)
/* 有符号整数类型的最大值：2^(N-1)-1 */
#define INT8_MAX  (127)
#define INT16_MAX (32767)
#define INT32_MAX (2147483647)
#define INT64_MAX (__INT64_C(9223372036854775807))
/* 无符号整数类型的最大值：2^(N-1)-1，注意有“Min = -MAX - 1” */
#define UINT8_MAX  (255)
#define UINT16_MAX (65535)
#define UINT32_MAX (4294967295U)
#define UINT64_MAX (__UINT64_C(18446744073709551615))

/* 有符号最小类型的最小值 */
#define INT_LEAST8_MIN  (-128)
#define INT_LEAST16_MIN (-32767-1)
#define INT_LEAST32_MIN (-2147483647-1)
#define INT_LEAST64_MIN (-__INT64_C(9223372036854775807)-1)
/* 有符号最小类型的最大值 */
#define INT_LEAST8_MAX  (127)
#define INT_LEAST16_MAX (32767)
#define INT_LEAST32_MAX (2147483647)
#define INT_LEAST64_MAX (__INT64_C(9223372036854775807))
/* 无符号最小类型的最大值 */
#define UINT_LEAST8_MAX  (255)
#define UINT_LEAST16_MAX (65535)
#define UINT_LEAST32_MAX (4294967295U)
#define UINT_LEAST64_MAX (__UINT64_C(18446744073709551615))

#define INT_FAST8_MIN (-128) /* 有符号快速类型的最小值 */
#if __WORDSIZE == 64
#define INT_FAST16_MIN (-9223372036854775807L-1)
#define INT_FAST32_MIN (-9223372036854775807L-1)
#else
#define INT_FAST16_MIN (-2147483647-1)
#define INT_FAST32_MIN (-2147483647-1)
#endif

#define INT_FAST64_MIN (-__INT64_C(9223372036854775807)-1)
#define INT_FAST8_MAX (127) /* 有符号快速类型的最大值 */
#if __WORDSIZE == 64
#define INT_FAST16_MAX (9223372036854775807L)
#define INT_FAST32_MAX (9223372036854775807L)
#else
#define INT_FAST16_MAX (2147483647)
#define INT_FAST32_MAX (2147483647)
#endif

#define INT_FAST64_MAX (__INT64_C(9223372036854775807))
#define UINT_FAST8_MAX (255) /* 无符号快速类型的最大值 */

#if __WORDSIZE == 64
#define UINT_FAST16_MAX (18446744073709551615UL)
#define UINT_FAST32_MAX (18446744073709551615UL)
#else
#define UINT_FAST16_MAX (4294967295U)
#define UINT_FAST32_MAX (4294967295U)
#endif
#define UINT_FAST64_MAX (__UINT64_C(18446744073709551615))

#if __WORDSIZE == 64 /* 指针类型(持有“void *”型指针)的范围 */
#define INTPTR_MIN  (-9223372036854775807L-1)
#define INTPTR_MAX  (9223372036854775807L)
#define UINTPTR_MAX (18446744073709551615UL)
#else
#define INTPTR_MIN  (-2147483647-1)
#define INTPTR_MAX  (2147483647)
#define UINTPTR_MAX (4294967295U)
#endif

#define INTMAX_MIN  (-__INT64_C(9223372036854775807)-1) /* 有符号最大类型的最小值 */
#define INTMAX_MAX  (__INT64_C(9223372036854775807)) /* 符号最大类型的最大值 */
#define UINTMAX_MAX (__UINT64_C(18446744073709551615)) /* 无符号最大类型的最大值 */

/* 其他整数类型的范围 */
#if __WORDSIZE == 64 /* ptrdiff_t类型的范围 */
#define PTRDIFF_MIN (-9223372036854775807L-1)
#define PTRDIFF_MAX (9223372036854775807L)
#else
#define PTRDIFF_MIN (-2147483647-1)
#define PTRDIFF_MAX (2147483647)
#endif

/* sig_atomic_t类型的范围 */
#define SIG_ATOMIC_MIN (-2147483647-1)
#define SIG_ATOMIC_MAX (2147483647)

#if __WORDSIZE == 64 /* size_t类型的范围 */
#define SIZE_MAX (18446744073709551615UL)
#else
#define SIZE_MAX (4294967295U)
#endif

#ifndef WCHAR_MIN /* wchar_t类型的范围 */
/* 这些常量可能在<wchar.h>也定义了 */
#define WCHAR_MIN __WCHAR_MIN
#define WCHAR_MAX __WCHAR_MAX
#endif
/* wint_t类型的范围 */
#define WINT_MIN (0u)
#define WINT_MAX (4294967295u)
#endif /* C++ && limit macros */

/* ISO C99标准指出，在C++实现中这些宏应该只在被请求到的时候才定义 */
#if !defined __cplusplus || defined __STDC_CONSTANT_MACROS
/* 有符号  */
#define INT8_C(c)  c
#define INT16_C(c) c
#define INT32_C(c) c
#if __WORDSIZE == 64
#define INT64_C(c) c ## L
#else
#define INT64_C(c) c ## LL
#endif

/* 无符号 */
#define UINT8_C(c)  c
#define UINT16_C(c) c
#define UINT32_C(c) c ## U

#if __WORDSIZE == 64
#define UINT64_C(c) c ## UL
#else
#define UINT64_C(c) c ## ULL
#endif

/* 最大类型 */
#if __WORDSIZE == 64
#define INTMAX_C(c)  c ## L
#define UINTMAX_C(c) c ## UL
#else
#define INTMAX_C(c)  c ## LL
#define UINTMAX_C(c) c ## ULL
#endif
#endif /* C++ && constant macros */
#endif /* stdint.h */
```

---

### 扩展整数类型stdint.h和inttypes.h

&emsp;&emsp;`C`语言的基本精神是让实现选择标准类型的长度，但是这种指导思想使可移植代码难以编写。`C99`中引入了`stdint.h`和`inttypes.h`，对整数类型的定义和格式转换进行了规范。这种扩展整数类型的定义非常清晰，从类型名字上就可以看出它的长度，这有利于编写可移植的代码。`stint.h`对整数类型进行定义，`inttypes.h`包含了`stdint.h`，并增加了可移植的格式控制串和转换函数。
&emsp;&emsp;`stdint.h`定义标准的扩展整数类型，包括准确长度类型`intN_t`、最小长度类型`int_leastN_t`、快速长度类型`int_fastN_t`、指针长度类型`intptr_t`、最大长度类型`intmax_t`(`N`为类型宽度)。定义规则如下：

- 类型的长度用宽度`N`参数化，如`intN_t`，`N`常常为`8`、`16`、`32`和`64`。
- 若要定义某种类型，则该类型的带符号和无符号类型、类型长度都要定义。如果定义`int32_t`，则有`uint32_t`类型。
- 要用`...MIN`和`...MAX`宏定义类型的范围。如`INT32_MIN`、`INT32_MAX`和`UINT32_MAX`。
- 在`inttypes.h`要用`PRIcKN`和`SCNcKN`定义打印该类型的`printf`和`scanf`格式控制字符串。`c`表示转换操作符，有`d`、`i`、`o`、`u`、`x`或`X`；`K`表示种类，为`空`或者`LEAST`、`FAST`、`PTR`或`MAX`；`N`是该类型的宽度。

&emsp;&emsp;具体解释如下：

- 准确长度类型：具有准确长度，没有填充位，在`stdint.h`是可选的。类型`intN_t`、`uintN_t`，范围`INTN_MIN`、`INTN_MAX`、`UINTN_MAX`，`inttypes.h`中的控制字符串`PRIcN`、`SCNcN`。
- 最小长度类型：具有指定长度的最小类型，至少要对`N = 8、16、32、64`定义这些类型。宏`INTN_C`(`constant`)、`UINTN_C`(`constant`)用于把传进来的常数扩展成相应类型的常量(即在后面加常量修饰符，如`U`、`L`、`LL`和`ULL`)。
- 快速长度类型：具有指定最小长度的最快类型(总是当前计算机`CPU`最佳操作的整型类型)。至少要对`N = 8、16、32、64`定义这些类型。
- 指针长度类型：`intptr_t`、`uintptr_t`分别是带符号和无符号整数类型，可以放置任何对象指针。范围是`INTPTR_MIN`、`INTPTR_MAX`、`UINTPTR_MAX`。
- 最大长度类型：`intmax_t`、`uintmax_t`分别是最大带符号和无符号整数类型，所有`C`语言实现都要定义。宏`INTMAX_C`、`UINTMAX_C`用于把传进来的常数扩展成相应类型的常量。

&emsp;&emsp;`GNU`实现中，各种扩展整数类型与我们通常使用的整数类型长度一致，即`8`位使用`char`类型，`16`位使用`sort`类型，`32`位使用`int`类型，`64`位在`64`位平台使用`long`类型，在`32`位平台上使用`long long`类型，注意`32`平台上的`long`一般和`int`一样大。只有快速类型有点区别，在`32`位平台上，`16`位和`32`位快速类型均使用`int`(这样才能快速地按字寻址)，`64`位快速类型使用`long long`；在`64`位平台上，`16`位、`32`位和`64`位快速类型均使用`long`。最大类型与字长度一致，指针类型也与字长度一致。
&emsp;&emsp;`ptrdiff_t`、`size_t`、`wchar_t`、`wint_t`与`sig_atomic_t`类型的范围也在`stdint.h`中定义，有`PTRDIFF_MIN/PTRDIFF_MAX`、`SIZE_MAX`、`WCHAR_MIN/WCHAR_MAX`、`WINT_MIN/WINT_MAX`、`SIG_ATOMIC_MIN/SIG_ATOMIC_MAX`。在`32`位平台的`GNU`实现中，`ptrdiff_t`一般为`int`类型(在`64`位平台上则为`long`类型)，`size_t`为`unsigned int`类型(在`64`位平台上则为`unsigned long`类型)，`wchar_t`一般为`int`类型，`wint_t`一般为`unsigned int`类型，`sig_atomic_t`通常为`int`类型。
&emsp;&emsp;`inttypes.h`定义扩展整数类型的`printf`和`scanf`格式控制字符串，用于实现可移植的格式化输出或输入。还定义了一些与`stdlib.h`中对应的基本算术函数和转换函数。`imaxabs`和`imaxdiv`函数类似于`stdlib.h`中的`abs`和`div`函数，`imaxabs(x)`返回最大类型整数`x`的绝对值，`imaxdiv(n, d)`计算最大类型整数的除法`n/d`，得出的商和余数分别放在`imaxdiv_t`结构的`quot`和`rem`成员中。`strtoimax`和`strtoumax`函数将字符串转换成最大类型的整数，与`stdlib.h`中的`strtol`和`strtoul`类似。`wcstoimax`和`wcstoumax`函数将宽字符串转换成最大类型的整数，与`wchar.h`中的`wcstol`和`wcstoul`类似。

``` cpp
/* ISO C99: 7.8 整数类型的格式转换<inttypes.h> */
#ifndef _INTTYPES_H
#define _INTTYPES_H 1

#include <features.h>
#include <stdint.h> /* 获取类型定义 */

/* 获取wchar_t的定义，但我们自己不能定义 */
#ifndef ____gwchar_t_defined
#ifdef __cplusplus
#define __gwchar_t wchar_t
#elif defined __WCHAR_TYPE__
typedef __WCHAR_TYPE__ __gwchar_t;
#else
#define __need_wchar_t
#include <stddef.h>
typedef wchar_t __gwchar_t;
#endif
#define ____gwchar_t_defined 1
#endif

/* ISO C99标准指出，这些宏应该只在被请求到的时候才定义 */
#if !defined __cplusplus || defined __STDC_FORMAT_MACROS
#if __WORDSIZE == 64
#define __PRI64_PREFIX  "l"
#define __PRIPTR_PREFIX "l"
#else
#define __PRI64_PREFIX "ll"
#define __PRIPTR_PREFIX
#endif

/* 表示输出格式控制串的宏 */
/* 十进制格式 */
#define PRId8  "d" /* 表示8位准确类型的输出格式控制串 */
#define PRId16 "d"
#define PRId32 "d"
#define PRId64 __PRI64_PREFIX "d"
#define PRIdLEAST8  "d" /* 表示8位最小类型的输出格式控制串 */
#define PRIdLEAST16 "d"
#define PRIdLEAST32 "d"
#define PRIdLEAST64 __PRI64_PREFIX "d"
#define PRIdFAST8 "d"
#define PRIdFAST16 __PRIPTR_PREFIX "d"
#define PRIdFAST32 __PRIPTR_PREFIX "d"
#define PRIdFAST64 __PRI64_PREFIX  "d"
#define PRIi8  "i"
#define PRIi16 "i"
#define PRIi32 "i"
#define PRIi64 __PRI64_PREFIX "i"
#define PRIiLEAST8  "i"
#define PRIiLEAST16 "i"
#define PRIiLEAST32 "i"
#define PRIiLEAST64 __PRI64_PREFIX "i"
#define PRIiFAST8 "i"
#define PRIiFAST16 __PRIPTR_PREFIX "i"
#define PRIiFAST32 __PRIPTR_PREFIX "i"
#define PRIiFAST64 __PRI64_PREFIX  "i"
/* 八进制格式 */
#define PRIo8  "o"
#define PRIo16 "o"
#define PRIo32 "o"
#define PRIo64 __PRI64_PREFIX "o"
#define PRIoLEAST8  "o"
#define PRIoLEAST16 "o"
#define PRIoLEAST32 "o"
#define PRIoLEAST64 __PRI64_PREFIX "o"
#define PRIoFAST8  "o"
#define PRIoFAST16 __PRIPTR_PREFIX "o"
#define PRIoFAST32 __PRIPTR_PREFIX "o"
#define PRIoFAST64 __PRI64_PREFIX "o"
/* 无符号整数格式 */
#define PRIu8  "u"
#define PRIu16 "u"
#define PRIu32 "u"
#define PRIu64 __PRI64_PREFIX "u"
#define PRIuLEAST8  "u"
#define PRIuLEAST16 "u"
#define PRIuLEAST32 "u"
#define PRIuLEAST64 __PRI64_PREFIX "u"
#define PRIuFAST8  "u"
#define PRIuFAST16 __PRIPTR_PREFIX "u"
#define PRIuFAST32 __PRIPTR_PREFIX "u"
#define PRIuFAST64 __PRI64_PREFIX  "u"
/* 小写十六进制格式 */
#define PRIx8  "x"
#define PRIx16 "x"
#define PRIx32 "x"
#define PRIx64 __PRI64_PREFIX "x"
#define PRIxLEAST8  "x"
#define PRIxLEAST16 "x"
#define PRIxLEAST32 "x"
#define PRIxLEAST64 __PRI64_PREFIX "x"
#define PRIxFAST8 "x"
#define PRIxFAST16 __PRIPTR_PREFIX "x"
#define PRIxFAST32 __PRIPTR_PREFIX "x"
#define PRIxFAST64 __PRI64_PREFIX  "x"
/* 大写十六进制格式 */
#define PRIX8  "X"
#define PRIX16 "X"
#define PRIX32 "X"
#define PRIX64 __PRI64_PREFIX "X"
#define PRIXLEAST8  "X"
#define PRIXLEAST16 "X"
#define PRIXLEAST32 "X"
#define PRIXLEAST64 __PRI64_PREFIX "X"
#define PRIXFAST8 "X"
#define PRIXFAST16 __PRIPTR_PREFIX "X"
#define PRIXFAST32 __PRIPTR_PREFIX "X"
#define PRIXFAST64 __PRI64_PREFIX  "X"
/* 打印intmax_t和uintmax_t的格式宏 */
#define PRIdMAX __PRI64_PREFIX "d"
#define PRIiMAX __PRI64_PREFIX "i"
#define PRIoMAX __PRI64_PREFIX "o"
#define PRIuMAX __PRI64_PREFIX "u"
#define PRIxMAX __PRI64_PREFIX "x"
#define PRIXMAX __PRI64_PREFIX "X"
/* 打印intptr_t和uintptr_t的格式宏 */
#define PRIdPTR __PRIPTR_PREFIX "d"
#define PRIiPTR __PRIPTR_PREFIX "i"
#define PRIoPTR __PRIPTR_PREFIX "o"
#define PRIuPTR __PRIPTR_PREFIX "u"
#define PRIxPTR __PRIPTR_PREFIX "x"
#define PRIXPTR __PRIPTR_PREFIX "X"
/* 表示输入格式控制串的宏 */
/* 有符号十进格式 */
#define SCNd8  "hhd"
#define SCNd16 "hd"
#define SCNd32 "d"
#define SCNd64 __PRI64_PREFIX "d"
#define SCNdLEAST8  "hhd"
#define SCNdLEAST16 "hd"
#define SCNdLEAST32 "d"
#define SCNdLEAST64 __PRI64_PREFIX "d"
#define SCNdFAST8 "hhd"
#define SCNdFAST16 __PRIPTR_PREFIX "d"
#define SCNdFAST32 __PRIPTR_PREFIX "d"
#define SCNdFAST64 __PRI64_PREFIX  "d"
/* 有符号十进制格式 */
#define SCNi8  "hhi"
#define SCNi16 "hi"
#define SCNi32 "i"
#define SCNi64 __PRI64_PREFIX "i"
#define SCNiLEAST8  "hhi"
#define SCNiLEAST16 "hi"
#define SCNiLEAST32 "i"
#define SCNiLEAST64 __PRI64_PREFIX "i"
#define SCNiFAST8 "hhi"
#define SCNiFAST16 __PRIPTR_PREFIX "i"
#define SCNiFAST32 __PRIPTR_PREFIX "i"
#define SCNiFAST64 __PRI64_PREFIX  "i"
/* 无符号十进制格式 */
#define SCNu8  "hhu"
#define SCNu16 "hu"
#define SCNu32 "u"
#define SCNu64 __PRI64_PREFIX "u"
#define SCNuLEAST8  "hhu"
#define SCNuLEAST16 "hu"
#define SCNuLEAST32 "u"
#define SCNuLEAST64 __PRI64_PREFIX "u"
#define SCNuFAST8 "hhu"
#define SCNuFAST16 __PRIPTR_PREFIX "u"
#define SCNuFAST32 __PRIPTR_PREFIX "u"
#define SCNuFAST64 __PRI64_PREFIX  "u"
/* 八进制格式 */
#define SCNo8  "hho"
#define SCNo16 "ho"
#define SCNo32 "o"
#define SCNo64 __PRI64_PREFIX "o"
#define SCNoLEAST8  "hho"
#define SCNoLEAST16 "ho"
#define SCNoLEAST32 "o"
#define SCNoLEAST64 __PRI64_PREFIX "o"
#define SCNoFAST8 "hho"
#define SCNoFAST16 __PRIPTR_PREFIX "o"
#define SCNoFAST32 __PRIPTR_PREFIX "o"
#define SCNoFAST64 __PRI64_PREFIX  "o"
/* 十六进制格式 */
#define SCNx8  "hhx"
#define SCNx16 "hx"
#define SCNx32 "x"
#define SCNx64 __PRI64_PREFIX "x"
#define SCNxLEAST8  "hhx"
#define SCNxLEAST16 "hx"
#define SCNxLEAST32 "x"
#define SCNxLEAST64 __PRI64_PREFIX "x"
#define SCNxFAST8  "hhx"
#define SCNxFAST16 __PRIPTR_PREFIX "x"
#define SCNxFAST32 __PRIPTR_PREFIX "x"
#define SCNxFAST64 __PRI64_PREFIX  "x"
/* 输入intmax_t和uintmax_t的格式宏 */
#define SCNdMAX __PRI64_PREFIX "d"
#define SCNiMAX __PRI64_PREFIX "i"
#define SCNoMAX __PRI64_PREFIX "o"
#define SCNuMAX __PRI64_PREFIX "u"
#define SCNxMAX __PRI64_PREFIX "x"
/* 输入intptr_t和uintptr_t的格式宏 */
#define SCNdPTR __PRIPTR_PREFIX "d"
#define SCNiPTR __PRIPTR_PREFIX "i"
#define SCNoPTR __PRIPTR_PREFIX "o"
#define SCNuPTR __PRIPTR_PREFIX "u"
#define SCNxPTR __PRIPTR_PREFIX "x"
#endif /* C++ && format macros */

__BEGIN_DECLS
#if __WORDSIZE == 64
/* 64位平台 */
typedef struct { /* 我们要使用ldiv_t来定义uintmax_t */
    long int quot; /* 商 */
    long int rem; /* 余数 */
} imaxdiv_t;
#else /* 32位平台 */
typedef struct { /* 我们要使用lldiv_t来定义uintmax_t */
    long long int quot; /* 商 */
    long long int rem; /* 余数 */
} imaxdiv_t;
#endif

extern intmax_t imaxabs ( intmax_t __n ) __THROW __attribute__ ( ( __const__ ) ); /* 计算N的绝对值 */
/* 返回NUMER除以DENOM的商和余数，放在imaxdiv_t结构中 */
extern imaxdiv_t imaxdiv ( intmax_t __numer, intmax_t __denom )
__THROW __attribute__ ( ( __const__ ) );
/* 与strtol类似，但转换成intmax_t */
extern intmax_t strtoimax ( __const char* __restrict __nptr, char** __restrict __endptr, int __base ) __THROW;
/* 与strtoul类似，但转换成uintmax_t */
extern uintmax_t strtoumax ( __const char* __restrict __nptr, char** __restrict __endptr, int __base ) __THROW;
/* 与wcstol类似，但转换成intmax_t */
extern intmax_t wcstoimax ( __const __gwchar_t* __restrict __nptr, __gwchar_t** __restrict __endptr, int __base )
__THROW;
/* 与wcstoul类似，但转换成uintmax_t */
extern uintmax_t wcstoumax ( __const __gwchar_t* __restrict __nptr, __gwchar_t** __restrict __endptr, int __base )
__THROW;

/* 下面是GNU的扩展：是上面4个标准转换函数的外部内联版本，有64位平台和32位平台之分 */
__END_DECLS
#endif /* inttypes.h */
```

&emsp;&emsp;注意，对于`inttypes.h`和`stdlib.h`中的绝对值函数和除法运算函数很容易实现，编译器通常把它们当作内部函数(标准`C`中允许这样做)，因此在`glibc`中并没有这些函数的源代码。
&emsp;&emsp;`strtoimax`和`strtoumax`函数将字符串转换成`intmax_t`类型的整数，一个为有符号，一个为无符号，其实现都是调用内部函数来完成的：

``` cpp
/* strtoimax.c：将字符串转换成最大整数类型 */
#include <inttypes.h>
#include <stdlib.h>

intmax_t strtoimax ( const char* __restrict nptr, char** __restrict endptr, int base ) {
    return __strtoll_internal ( nptr, endptr, base, 0 );
}

/* strtoumax.c：将字符串转换成无符号的最大整数类型 */
uintmax_t strtoumax ( const char* __restrict nptr, char** __restrict endptr, int base ) {
    return __strtoull_internal ( nptr, endptr, base, 0 );
}
```

&emsp;&emsp;`wcstoimax`和`wcstoumax`函数将宽字符串转换成`intmax_t`类型的整数，一个为有符号，一个为无符号，其实现也都是调用内部函数来完成的：

``` cpp
/* wcstoimax将宽字符串转换成最大整数类型 */
#include <inttypes.h>
#include <wchar.h>

intmax_t wcstoimax ( const wchar_t* __restrict nptr, wchar_t** __restrict endptr, int base ) {
    return __wcstoll_internal ( nptr, endptr, base, 0 );
}

/* wcstoumax将宽字符串转换成无符号的最大整数类型 */
uintmax_t wcstoumax ( const wchar_t* __restrict nptr, wchar_t** __restrict endptr, int base ) {
    return __wcstoull_internal ( nptr, endptr, base, 0 );
}
```