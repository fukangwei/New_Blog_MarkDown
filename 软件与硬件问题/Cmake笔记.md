---
title: Cmake笔记
date: 2019-07-05 19:49:41
tags:
---
### 什么是cmake

&emsp;&emsp;你或许听过好几种`Make`工具，例如`GNU`的`Make`、`Qt`的`qmake`等。这些`Make`工具遵循着不同的规范和标准，所执行的 Makefile格式也千差万别。这样就带来了一个严峻的问题：如果软件想跨平台，必须要保证能够在不同平台编译。而如果使用上面的`Make`工具，就得为每一种标准写一次`Makefile`，这将是一件让人抓狂的工作。
&emsp;&emsp;`cmake`就是针对上面问题所设计的工具：它首先允许开发者编写一种平台无关的`CMakeList.txt`文件来定制整个编译流程，然后再根据目标用户的平台进一步生成所需的本地化`Makefile`和工程文件，如`Unix`的`Makefile`或`Windows`的`Visual Studio`工程，从而做到`Write once, run every where`。显然，`cmake`是一个比上述几种`make`更高级的编译配置工具。
&emsp;&emsp;在`Linux`平台下使用`cmake`生成`Makefile`并编译的流程如下：

1. 编写`cmake`配置文件`CMakeLists.txt`。
2. 执行命令`cmake PATH`生成`Makefile`，其中`PATH`是`CMakeLists.txt`所在的目录。
3. 使用`make`命令进行编译。

### 编译单个源文件

&emsp;&emsp;首先编写`main.c`文件：

``` cpp
#include <stdio.h>
#include <stdlib.h>

double power ( double base, int exponent ) {
    int result = base;
    int i;

    if ( exponent == 0 ) {
        return 1;
    }

    for ( i = 1; i < exponent; ++i ) {
        result = result * base;
    }

    return result;
}

int main ( int argc, char* argv[] ) {
    if ( argc < 3 ) {
        printf ( "Usage: %s base exponent \n", argv[0] );
        return 1;
    }

    double base = atof ( argv[1] );
    int exponent = atoi ( argv[2] );
    double result = power ( base, exponent );
    printf ( "%g ^ %d is %g\n", base, exponent, result );
    return 0;
}
```

再编写`CMakeLists.txt`文件，并保存在与`main.c`源文件同个目录下：

``` makefile
# CMake最低版本号要求
cmake_minimum_required (VERSION 2.8)
# 项目信息
project (Demo1)
# 指定生成目标
add_executable (Demo main.c)
```

`CMakeLists.txt`的语法比较简单，由命令、注释和空格组成，其中命令是不区分大小写的。符号`#`后面的内容被认为是注释。命令由命令名称、小括号和参数组成，参数之间使用空格进行间隔。
&emsp;&emsp;对于上面的`CMakeLists.txt`文件，依次出现了几个命令：

- `cmake_minimum_required`：指定运行此配置文件所需的`cmake`最低版本。
- `project`：参数值是`Demo1`，该命令表示项目的名称是`Demo1`。
- `add_executable`：将名为`main.c`的源文件编译成一个名称为`Demo`的可执行文件。

在当前目录执行`cmake .`，得到`Makefile`后再使用`make`命令编译得到`Demo`可执行文件：

``` bash
[ehome@xman Demo1]$ cmake .
-- The C compiler identification is GNU 4.8.2
-- The CXX compiler identification is GNU 4.8.2
-- Check for working C compiler: /usr/sbin/cc
-- Check for working C compiler: /usr/sbin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working CXX compiler: /usr/sbin/c++
-- Check for working CXX compiler: /usr/sbin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/ehome/Demo1
[ehome@xman Demo1]$ make
Scanning dependencies of target Demo
[100%] Building C object CMakeFiles/Demo.dir/main.c.o
Linking C executable Demo
[100%] Built target Demo
[ehome@xman Demo1]$ ./Demo 5 4
5 ^ 4 is 625
[ehome@xman Demo1]$ ./Demo 7 3
7 ^ 3 is 343
```

### 同一目录，多个源文件

&emsp;&emsp;现在把`power`函数单独写进一个名为`MathFunctions.c`的源文件里，使得这个工程变成如下的形式：

``` bash
Demo2/
    |
    +--- main.c
    +--- MathFunctions.c
    +--- MathFunctions.h
```

`CMakeLists.txt`可以改成如下形式：

``` bash
cmake_minimum_required (VERSION 2.8)
project (Demo2)
add_executable (Demo main.c MathFunctions.c)
```

唯一的改动只是在`add_executable`命令中增加了一个`MathFunctions.c`源文件。这样写当然没什么问题，但是如果源文件很多，把所有源文件的名字都加进去将是一件烦人的工作。更省事的方法是使用`aux_source_directory`命令，该命令会查找指定目录下的所有源文件，然后将结果存进指定变量名：

``` makefile
aux_source_directory (<dir> <variable>)
```

修改`CMakeLists.txt`如下：

``` makefile
cmake_minimum_required (VERSION 2.8)
project (Demo2)
# 查找当前目录下的所有源文件，并将名称保存到变量DIR_SRCS
aux_source_directory (. DIR_SRCS)
add_executable (Demo ${DIR_SRCS})
```

这样，`cmake`会将当前目录所有源文件的文件名赋值给变量`DIR_SRCS`，再指示变量`DIR_SRCS`中的源文件需要编译成一个名称为`Demo`的可执行文件。

### 多个目录，多个源文件

&emsp;&emsp;现在进一步将`MathFunctions.h`和`MathFunctions.c`文件移动到`math`目录下：

``` bash
Demo3/
    |
    +--- main.c
    +--- math/
        +--- MathFunctions.c
        +--- MathFunctions.h
```

对于这种情况，需要分别在项目根目录`Demo3`和`math`目录里各编写一个`CMakeLists.txt`文件。为了方便，我们可以先将`math`目录里的文件编译成静态库，再由`main`函数调用。
&emsp;&emsp;`main.c`如下：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include "math/MathFunctions.h"

int main ( int argc, char* argv[] ) {
    if ( argc < 3 ) {
        printf ( "Usage: %s base exponent \n", argv[0] );
        return 1;
    }

    double base = atof ( argv[1] );
    int exponent = atoi ( argv[2] );
    double result = power ( base, exponent );
    printf ( "%g ^ %d is %g\n", base, exponent, result );
    return 0;
}
```

`MathFunctions.c`如下：

``` cpp
double power ( double base, int exponent ) {
    int result = base;
    int i;
    if ( exponent == 0 ) {
        return 1;
    }

    for ( i = 1; i < exponent; ++i ) {
        result = result * base;
    }

    return result;
}
```

`MathFunctions.h`如下：

``` cpp
#ifndef POWER_H
#define POWER_H

extern double power ( double base, int exponent );
#endif
```

根目录中的`CMakeLists.txt`如下：

``` makefile
cmake_minimum_required (VERSION 2.8)
project (Demo3)
aux_source_directory (. DIR_SRCS)
# 添加math子目录
add_subdirectory (math)
# 指定生成目标
add_executable (Demo main.cc)
# 添加链接库
target_link_libraries (Demo MathFunctions)
```

该文件使用命令`add_subdirectory`指明本项目包含一个子目录`math`，这样`math`目录下的`CMakeLists.txt`文件和源代码也会被处理；使用命令`target_link_libraries`指明可执行文件`main`需要连接一个名为`MathFunctions`的链接库。
&emsp;&emsp;子目录中的`CMakeLists.txt`如下：

``` Makefile
# 查找当前目录下的所有源文件，并将名称保存到变量DIR_LIB_SRCS
aux_source_directory (. DIR_LIB_SRCS)
# 生成链接库
add_library (MathFunctions ${DIR_LIB_SRCS})
```

该文件使用命令`add_library`将`src`目录中的源文件编译为静态链接库。

### 添加可配置的头文件

cmake可以通过可配置的头文件来产生实际的头文件，如下面的可配置头文件 hello.h.in，里面@@引用 的变量可以通过 CMakeLists.txt来设置，最后通过 cmake来替换 hello.h.in文件中的变量，并生成 hello.h内 容。
目录结构如下：
1. ./Hello   2.   | 3.   +--- hello.c  4.   +--- hello.in.h  5.   +--- CMakeLists.txt  6.   +--- myhello/ 7.       +--- myhello.c  8.       +--- myhello.h  9.       +--- CMakeLists.txt
hello.h.in如下：
1. #define VERSION_MAJOR @VERSION_MAJOR@   2. #define VERSION_MINOR @VERSION_MINOR@
CMakeLists.txt如下：
1. cmake_minimum_required(VERSION 3.5)   2. project(hello)   3.    4. include_directories("${PROJECT_BINARY_DIR}")   5. set(VERSION_MAJOR 1)   6. set(VERSION_MINOR 0)   7. configure_file(   8.     "${PROJECT_SOURCE_DIR}/hello.h.in"   9.     "${PROJECT_BINARY_DIR}/hello.h"   10. )   11.
12. add_subdirectory(myhello)   13. set (EXTRA_LIBS ${EXTRA_LIBS} myhello)   14.    15. add_executable(hello hello.c)   16. target_link_libraries (hello ${EXTRA_LIBS})
hello.c如下：
1. #include "myhello/myhello.h"   2. #include "hello.h"   3. #include <stdio.h>   4.    5. int main (int argc, char *argv[]) {   6.     printf("version:%d.%d\n", VERSION_MAJOR, VERSION_MINOR);   7.     PrintHelloWorld();   8.     return 0;   9. }
myhello.h如下：
1. void PrintHelloWorld();
myhello.c如下：
1. #include <stdio.h>  2.  3. void PrintHelloWorld() {   4.     printf("hello world!");   5. }
myhello 目录下的 CMakeLists.txt如下：
1. add_library(myhello myhello.c)
上面加红的命令主要用来设置 hello.h.in中的两个变量，并且让 cmake生成 hello.h文件。生成的 hello.h 如下：
1. #define VERSION_MAJOR 1   2. #define VERSION_MINOR 0

检测系统是否有支持工程需要的函数
对于跨平台的工程来说，检查系统是否支持某些特性是很有必要的，这样程序中就可以通过系统的特 性来选择具体执行哪些代码。其中检查是否支持某些函数是我们经常要做的事情，如 epoll函数，可能有的 linux系统就不支持，对于不支持的系统我们只能用 poll来替代等。在 cmake中检查系统是否支持某个函数 也很简单，先包含一个 CheckFunctionExists库，然后使用 check_function_exists来判断就行了。
CMakeLists.txt如下：
1. cmake_minimum_required(VERSION 3.5)   2. project(hello)   3.    4. include (CheckFunctionExists)   5. check_function_exists (printf HAVE_PRINTF)   6.    7. include_directories("${PROJECT_BINARY_DIR}")   8. set(VERSION_MAJOR 1)   9. set(VERSION_MINOR 0)   10. configure_file(   11.     "${PROJECT_SOURCE_DIR}/hello.h.in"   12.     "${PROJECT_BINARY_DIR}/hello.h"   13. )   14.    15. add_subdirectory(myhello)   16. set (EXTRA_LIBS ${EXTRA_LIBS} myhello)   17.  18. add_executable(hello hello.c)   19. target_link_libraries (hello ${EXTRA_LIBS})
在配置的头文件 hello.h.in中加入#cmakedefine HAVE_PRINTF，这样如果系统中有 printf函数，最终生 成的 hello.h中会定义 HAVE_PRINTF这个宏，否则不会生成。在 hello.c文件中可以根据这个宏来判断是否 应该使用 printf函数。
hello.h.in如下：
1. #define VERSION_MAJOR @VERSION_MAJOR@   2. #define VERSION_MINOR @VERSION_MINOR@   3.    4. #cmakedefine HAVE_PRINTF
hello.c如下：
1. #include "myhello/myhello.h"   2. #include "hello.h"   3. #include <stdio.h>   4.    5. int main (int argc, char *argv[]) {   6. #ifdef HAVE_PRINTF   7.     printf("version:%d.%d\n", VERSION_MAJOR, VERSION_MINOR);   8. #endif   9.     PrintHelloWorld();   10.     return 0;   11. }

配置可选项
有时候代码可能包含了所有平台的模块代码，但是对于特定的目标平台，只需要配置该平台需要模块 的代码，而不需要配置其它平台模块的代码。这种需求可以通过 cmake的配置可选项来完成，配置可选项
就是 cmake在生成工程的时候提示你一些选项，根据你的选项来具体选择需要添加到工程中的模块代码。 例如我现在需要提高是否使用 myhello模块的选项，可以在 CMakeLists.txt中加 option命令来实现：
1. cmake_minimum_required(VERSION 3.5)   2. project(hello)   3.    4. include_directories("${PROJECT_BINARY_DIR}")   5. set(VERSION_MAJOR 1)   6. set(VERSION_MINOR 0)   7.    8. option (USE_MYHELLO   9.         "Use myhello" ON)   10.            11. configure_file(   12.     "${PROJECT_SOURCE_DIR}/hello.h.in"   13.     "${PROJECT_BINARY_DIR}/hello.h"   14. )   15.    16. add_subdirectory(myhello)   17. set (EXTRA_LIBS ${EXTRA_LIBS} myhello)   18.    19. add_executable(hello hello.c)   20. target_link_libraries(hello ${EXTRA_LIBS})
并且在 hello.h.in中添加由 cmake 根据选项来定义 USE_MYHELLO宏：
1. #define VERSION_MAJOR @VERSION_MAJOR@   2. #define VERSION_MINOR @VERSION_MINOR@   3.    4. #cmakedefine USE_MYHELLO
hello.c如下：
1. #include "myhello/myhello.h"   2. #include "hello.h"   3. #include <stdio.h>   4.    5. int main (int argc, char *argv[]) {   6. #ifdef USE_MYHELLO   7.     PrintHelloWorld();   8. #else   9.     printf("xx hello world!");   10. #endif   11.     return 0;   12. }

定义安装规则
向 myhello文件夹中的 CMakeLists.txt增加如下代码：
1. # 指定 myhello 库的安装路径
2. install(TARGETS myhello DESTINATION bin)   3. install(FILES myhello.h DESTINATION include)
向根文件夹中的 CMakeLists.txt增加如下代码：
1. # 指定安装路径   2. install(TARGETS Demo DESTINATION bin)   3. install(FILES "${PROJECT_BINARY_DIR}/hello.h" DESTINATION include)
使用命令“sudo make install”即可安装完成(安装目录是“/usr/local/bin”和“/usr/local/include”)。
#------------------------------------------------------------------------------------------------------------
set 指令详解
语法：SET(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])，指令功能：用来显式地定义变 量。 例子：SET (SRC_LST main.c other.c)，用变量 SRC_LST 代替值，例子中定义 SRC_LST 代替后面的字 符串。
#----------------------------------------------------------------------------------------------------------
find_package 使用
为了能支持各种常见的库和包，cmake 自带了很多模块，可以通过命令 cmake –help-module-list 得到你 的 cmake 支 持 的 模 块 的 列 表 ； 或者直 接 查 看 模 块 路 径 ， 比如在 Ubuntu 上 ， 模 块 的 路 径 是 /usr/share/cmake/Modules/：
ls -al /usr/share/cmake-3.5/Modules/
...
-rw-r--r-- 1 root root 76K Sep 27 2016 FindBoost.cmake
-rw-r--r-- 1 root root 2.7K Mar 24 2016 FindCoin3D.cmake
-rw-r--r-- 1 root root 77K Mar 24 2016 FindCUDA.cmake
-rw-r--r-- 1 root root 3.1K Mar 24 2016 FindCups.cmake
-rw-r--r-- 1 root root 2.4K Mar 24 2016 FindCURL.cmake
...
让我们以bzip2库为例，cmake中有个FindBZip2.cmake模块，只要使用find_package(BZip2)调用这个模 块，cmake 会自动给一些变量赋值，然后就可以在 CMake 脚本中使用它们了。变量的列表可以查看 cmake 模块文件，或者使用命令：
root@xy:~/cmake_practice/# cmake --help-module FindBZip2
FindBZip2
---------
Try to find BZip2


Once done this will define
::
BZIP2_FOUND - system has BZip2
BZIP2_INCLUDE_DIR - the BZip2 include directory
BZIP2_LIBRARIES - Link these to use BZip2
BZIP2_NEED_PREFIX - this is set if the functions are prefixed with BZ2_
BZIP2_VERSION_STRING - the version of BZip2 found (since CMake 2.8.8)
cmake 会将路径赋值给对应的变量，我们以 curl的 cmake为例，其部分内容如下：
find_path(CURL_INCLUDE_DIR NAMES curl/curl.h)
mark_as_advanced(CURL_INCLUDE_DIR)

# Look for the library (sorted from most current/relevant entry to least).
find_library(CURL_LIBRARY NAMES
    curl # Windows MSVC prebuilts:
 curllib
 libcurl_imp
 curllib_static
 # Windows older "Win32 - MSVC" prebuilts (libcurl.lib, e.g. libcurl-7.15.5-win32-msvc.zip):
 libcurl
 )
比如一个使用 bzip2的简单程序，编译器需要知道 bzlib.h 的位置，链接器需要找到 bzip2库(动态链接的 话，Unix上是 libbz2.so ，Windows上是 libbz2.dll)。
project(helloworld)
add_executable(helloworld hello.c)
find_package(BZip2)

if(BZIP2_FOUND)
include_directories(${BZIP_INCLUDE_DIRS})
 target_link_libraries(helloworld ${BZIP2_LIBRARIES})
endif(BZIP2_FOUND)

如何编写自己的 cmake module 模块
下面以工程 demo9为示例，项目目录结构如下：
├── cmake
│   └── FindDEMO9LIB.cmake
├── CMakeLists.txt
├── demo9.cpp
├── demo9.h
└── demo9_main.cpp
其中 demo9.h和 demo9.cpp生成 lib，demo9_main.cpp链接对应的 lib生成可执行文件。
demo9.h如下：
#ifndef PROJECT_DEMO9_H
#define PROJECT_DEMO9_H
void print_demo9();
#endif //PROJECT_DEMO3_H
demo9.cpp如下：
#include "demo9.h"
#include <iostream>

void print_demo9(){
 std::cout << "this is demo9" << std::endl;
}
demo9_main.cpp如下：
#include "demo9.h"

int main(){
    print_demo9();
    return 0;
}
首先使用 demo9.h和 demo9.cpp生成静态 lib，并安装：
-- Installing: /usr/local/demo9/lib/libdemo9_lib.a
-- Installing: /usr/local/demo9/include/demo9.h
FindDEMO9LIB.cmake的内容如下：
# 辅助输出信息
message("now using FindDEMO9LIB.cmake find demo9 lib")
# 将 demo9.h文件路径赋值给 DEMO9LIB_INCLUDE_DIR
FIND_PATH(DEMO9LIB_INCLUDE_DIR demo9.h /usr/include/demo9/ /usr/local/demo9/include/)
message("./h dir: ${DEMO9LIB_INCLUDE_DIR}")
# 将 libdemo9_lib.a 文件路径赋值给 DEMO9LIB_LIBRARY
FIND_LIBRARY(DEMO9LIB_LIBRARY libdemo9_lib.a /usr/local/demo9/lib/)
message("lib dir: ${DEMO9LIB_LIBRARY}")

if(DEMO9LIB_INCLUDE_DIR AND DEMO9LIB_LIBRARY)
    # 设置变量结果
    set(DEMO9LIB_FOUND TRUE)
endif(DEMO9LIB_INCLUDE_DIR AND DEMO9LIB_LIBRARY)
主 CMakeLists.txt内容如下：
cmake_minimum_required(VERSION 2.8)
project(demo9)

# create libdemo9_lib.a
set(SRC_LIB demo9.cpp)
add_library(demo9_lib STATIC ${SRC_LIB})

# install it
install(TARGETS demo9_lib DESTINATION demo9/lib)
install(FILES demo9.h DESTINATION demo9/include)

# create demo9_main exectuable
set(SRC_EXE demo9_main.cpp)

# set demo9_lib cmake module path
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
message("cmake_module_path: ${CMAKE_MODULE_PATH}")
find_package(DEMO9LIB)

if(DEMO9LIB_FOUND)
 add_executable(demo9_main ${SRC_EXE})
 message("found demo9 ${DEMO9LIB_INCLUDE_DIR} ${DEMO9LIB_LIBRARY}")
 include_directories(${DEMO9LIB_INCLUDE_DIR})
 target_link_libraries(demo9_main ${DEMO9LIB_LIBRARY})
else()
 message("not found DEMO9LIB_FOUND")
endif(DEMO9LIB_FOUND)
编译输出信息如下：
root@xy:~/cmake_practice/# cmake ../../cmake_tuorial/demo9/
cmake_module_path: /home/xy/cmake_practice/cmake_tuorial/demo9/cmake
now using FindDEMO9LIB.cmake find demo9 lib
./h dir： /usr/local/demo9/include
lib dir: /usr/local/demo9/lib/libdemo9_lib.a
found demo9 /usr/local/demo9/include /usr/local/demo9/lib/libdemo9_lib.a
make结果如下：
root@xy:~/cmake_practice/cmake_build/build_demo9# make -j10
cmake_module_path: /home/xy/cmake_practice/cmake_tuorial/demo9/cmake
now using FindDEMO9LIB.cmake find demo9 lib
./h dir /usr/local/demo9/include
lib dir: /usr/local/demo9/lib/libdemo9_lib.a
found demo9 /usr/local/demo9/include /usr/local/demo9/lib/libdemo9_lib.a
-- Configuring done
运行结果如下：
root@xy:~/cmake_practice/# ./demo9_main
this is demo9
#-----------------------------------------------------------------------------------------------------------
cmake 中链接系统标准库
如果要在编译时添加例如 -lpthread -lmath这类通用的库，只需直接在 CMakeLists.txt中添加 LINK_LIBRARIES(标准库名称)或 TARGET_LINK_LIBRARIES(编译目标名称 target 标准库名称)，其中标 准库名称就是-l后面的库名称，如 math、pthread等，编译目标名称就是 ADD_EXECUTABLE的第一个参 数。
如果为所有 target统一指定编译时，要链接的库用 LINK_LIBRARIES；为每个 target单独指定编译时 要链接的库用 TARGET_LINK_LIBRARIES。注意，用 LINK_LIBRARIES 时这行一定要写在 ADD_EXECUTABLE前面。
#------------------------------------------------------------------------------------------------------------
cmake 常用变量
CMAKE_BINARY_DIR、PROJECT_BINARY_DIR和<projectname>_BINARY_DIR：这三个变量指代的 内容是一致的，指的就是工程顶层目录。
CMAKE_SOURCE_DIR、PROJECT_SOURCE_DIR和<projectname>_SOURCE_DIR：这三个变量指代 的内容是一致的，指的是工程顶层目录。
CMAKE_MAJOR_VERSION：CMAKE 主版本号，比如 2.4.6中的 2。CMAKE_MINOR_VERSION： CMAKE次版本号，比如 2.4.6 中的 4。CMAKE_PATCH_VERSION：CMAKE 补丁等级，比如 2.4.6中 的 6。
CMAKE_SYSTEM：系统名称，比如 Linux-2.6.22。
CMAKE_SYSTEM_NAME：不包含版本的系统名，比如 Linux。
CMAKE_SYSTEM_VERSION：系统版本，比如 2.6.22。
CMAKE_SYSTEM_PROCESSOR：处理器名称，比如 i686。
UNIX：在所有的类 UNIX 平台为 TRUE。
WIN32：在所有的 win32平台为 TRUE。
