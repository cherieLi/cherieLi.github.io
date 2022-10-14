
---
layout: post
title:  CMakeLists文件编写规则
categories: linux
tags: CMakeLists
author: CherieLi
---

* content
{:toc}


文件名 CMakeLists.txt

mkdir build

cd build

cmake ..

make

make test //进行单元测试

make clean //对构建结果进行清理

make install // 安装到/usr/bin 等文件下

 

 

产生的缓存都在build目录下了

 

 

\# *******************************************************************

\# Copyright (C) 

\# Created by Li Ziyi

\# Authors: Li Ziyi

\#

\# Build for HelloWorld

\# *******************************************************************/

# #1.最低版本号要求

\#cmake version

CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

 

# #2.指定项目的名称，一般和项目的文件夹名称对应

\#project name 

PROJECT(hello)

PROJECT(mathfunction)

# #3.头文件目录,引入头文件搜索路径

\#head file path 

指令语法：

INCLUDE_DIRECTORIES([AFTER|BEFORE] [SYSTEM] dir1 dir2 ...)

 

AFTER 或者 BEFORE 指定了要添加的路径是添加到原有包含列表之前或之后

若指定 SYSTEM 参数，则把被包含的路径当做系统包含路径来处理

If the SYSTEM option is given, the compiler will be told the directories are meant as system include directories on some platforms. Signalling this setting might achieve effects such as the compiler skipping warnings, or these fixed-install system files not being considered in dependency calculations - see compiler docs.

 

 

案例：

INCLUDE_DIRECTORIES(

 include

 )

 

# #4.源文件目录

指令的语法是：

aux_source_directory(<dir> <variable>)

 

案例：

\#source directory

AUX_SOURCE_DIRECTORY(. DIR_SRCS)

aux_source_directory(src DIR_LIB_SRCS)

AUX_SOURCE_DIRECTORY(. DIR_TEST1_SRCS)

 

# #5.设置环境变量，编译用到的源文件全部都要放到这里，否则编译能够通过，但是执行的时候会出现各种问题，比如"symbol lookup error xxxxx , undefined symbol"

\#set environment variable

SET指令的语法是：

SET(VAR [VALUE][CACHE TYPE DOCSTRING [FORCE]])

 

举例：

SET(TEST_MATH

 ${DIR_SRCS}

 )

SET(SRC_LIST main.c t1.c t2.c)

 

# #6.添加要编译的可执行文件

\#add executable file 

ADD_EXECUTABLE(${PROJECT_NAME} ${TEST_MATH})

add_executable(mathfunction main.cc MathFunctions.cc)

add_executable(hello ${DIR_SRCS})

 

# #7.添加链接库，添加可执行文件所需要的库，指定文件链接库文件。

**比如我们用到了****libm.so****（命名规则：****lib+name+.so****），就添加该库的名称**

 

\#add link library

指令语法：

TARGET_LINK_LIBRARIES(target library1<debug | optimized> library2...)

 

案例：

TARGET_LINK_LIBRARIES(${PROJECT_NAME} m)

TARGET_LINK_LIBRARIES( hello test1 )

# #8.添加子目录,子目录下也需要有CMakeLists.txt文件

\#add subdirectory

指令语法：

ADD_SUBDIRECTORY(source_dir [binary_dir] [EXCLUDE_FROM_ALL])

add_subdirectory(src)

add_subdirectory(math)

 

用子目录的好处：

\1.       让每个模块单独编译

\2.       便于建立链接库文件，供其他文件使用

\3.       下层操作可与上层操作屏蔽分隔开，便于对子目录下的文件进行增删，便于进行管理

# #9.生成链接库

\#create library

指令语法：

ADD_LIBRARY(libname [SHARED|STATIC|MODULE][EXCLUDE_FROM_ALL]source1 source2 ... sourceN)

类型有三种: 
 SHARED，动态库
 STATIC，静态库
 MODULE，在使用dyld的系统有效，如果不支持dyld，则被当作SHARED对待。

 

案例：

add_library(helloworld_lib_shared  SHARED ${c_files})  //动态库

add_library(helloworld_lib_static STATIC ${c_files})   //静态库

add_library (math ${DIR_LIB_SRCS})

add_library (test1 ${DIR_TEST1_SRCS})

 

# #10.添加配置文件, config.h ，这个文件由 CMake 从 config.h.in 生成

\#add configure file

configure_file (

  "${PROJECT_SOURCE_DIR}/config.h.in"

  "${PROJECT_BINARY_DIR}/config.h"

  )

 

# #11.是否使用某个库

\#use library or not

option (USE_MYMATH

       "Use provided math implementation" ON)

# #12.启用测试

\#start test

enable_testing()

# #13.测试帮助信息是否可以正常提示

\#test help message

add_test (test_usage Demo)

set_tests_properties (test_usage

  PROPERTIES PASS_REGULAR_EXPRESSION "Usage: .* base exponent")

 

 

# #14.测试程序是否成功运行

\# Test 

add_test (test_5_2 Demo 5 2)

set_tests_properties (test_5_2

 PROPERTIES PASS_REGULAR_EXPRESSION "is 25")

 

# #15.定义一个宏，用来简化测试工作

\#define macro

macro (do_test arg1 arg2 result)

  add_test (test_${arg1}_${arg2} Demo ${arg1} ${arg2})

  set_tests_properties (test_${arg1}_${arg2}

    PROPERTIES PASS_REGULAR_EXPRESSION ${result})

endmacro (do_test)

 

# #16.使用定义的宏进行一系列的数据测试

\# test by using macro

do_test (5 2 "is 25")

do_test (10 5 "is 100000")

do_test (2 10 "is 1024")

 

# #17.gdb设置debug版本，release版本

\#gdb setting

set(CMAKE_BUILD_TYPE "Debug")

set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")

set(CMAKE_BUILD_TYPE "Release")

set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")

 

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O0")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -Wall -Werror -std=c++11 ")

 

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -Wall -Werror -pthread -std=c++11 ")

 

 

# #18.版本维护

\#version information

set (Demo_VERSION_MAJOR 1)

set (Demo_VERSION_MINOR 0)

 

major 表示主版本

minor 表示副版本

 

# #19.获取属性值

\#get property

get_property(sub_src GLOBAL PROPERTY COMM_SRC)

# #20.设置属性值

\#set property

set_property(GLOBAL APPEND PROPERTY MODULE_REG_SRC

   ${CMAKE_CURRENT_SOURCE_DIR}/CommandMessageReg.cc)

 

# #21.输出用户定义的信息

\#message

MESSAGE指令的语法是：

MESSAGE([SEND_ERROR | STATUS | FATAL_ERROR] "message to display"...)

三种类型:

SEND_ERROR，产生错误，生成过程被跳过。

SATUS，输出前缀为—的信息。

FATAL_ERROR，立即终止所有cmake过程。

 

案例：

message(STATUS "SCRIPT_TYPE: " ${SCRIPT_TYPE})

 

# #22.添加链接库的搜索路径

指令语法：

LINK_DIRECTORIES(directory1 directory2 ...)

 

案例：

LINK_DIRECTORIES(

  ${COMMON_LIB_PATH}

  ${SLICE_BASE_DIR}/sal/build/SliceServer

  )

# #23.设置库的属性

将动态库和静态库的名字设置为一致

set_target_properties(helloworld_lib_shared PROPERTIES OUTPUT_NAME "helloworld")

set_target_properties(helloworld_lib_static PROPERTIES OUTPUT_NAME "helloworld")

 

设置动态库版本

set_target_properties(helloworld_lib_shared PROPERTIES VERSION 1.2 SOVERSION 1)

 

# 日报

（1）       linux下有两种库:动态库和静态库(共享库)

不同点在于代码被载入的时刻不同

静态库的代码在编译过程中已经被载入可执行程序,因此体积比较大。

动态库(共享库)的代码在可执行程序运行时才载入内存，在编译过程中仅简单的引用，因此代码体积比较小。

 

静态库这类库的名字一般是libxxx.a

          好处：编译后的执行程序不需要外部的函数库支持

不足：如果静态函数库改变了，那么你的程序必须重新编译。

 

动态库这类库名字一般是libxxx.so

程序运行时动态的申请并调用

好处：动态函数库的改变并不影响你的程序，所以动态函数库的升级/更新比较方便。

不足：程序的运行环境中必须提供相应的库。

 

当静态库和动态库同名时， gcc命令将优先使用动态库.为了确保使用的是静态库, 编译时可以加上 -static  选项，因此多第三方程序为了确保在没有相应动态库时运行正常，喜欢在编译最后应用程序时加入-static

（2） 中间的内容循环依赖

-Wl,--whole-archive -Wl,--start-group

  dswcore dposax dplog dpdiagnose dpumm_mm dphpuc mxml ibverbs dpumm_cmm osax_util

  dposen iod lwt dptracepoint patmatch scpart_mgr dif CommonTools rdmacm

  ibumad ibmad ACE crc32c xnetlite ftdsclient

  -Wl,--end-group -Wl,--no-whole-archive

# 好友的总结

### CMake基本使用

#### 1 简单示例

示例：求一个数的平方根，编写一个头文件cal_sqrt.h和一个.cc文件main.cc。

```
//目录结构
// └── demo
//     ├── build
//     ├── cal_sqrt.h
//     ├── CMakeLists.txt
//     └── main.cc

// cal_sqrt.h
#ifndef __CAL_SQRT_H__
#define __CAL_SQRT_H__

#include <cmath>

double cal_sqrt(double value);

#endif

// main.cc
#include "cal_sqrt.h"
#include <iostream>

int main() {
 double value = 81.0;
 double res = 0.0;

 res = cal_sqrt(value);
 std::cout<<value<<" sqrt result is: "<<res<<std::endl;

 return 0;
}

double cal_sqrt(double value) {
 return sqrt(value);
}
```

CMakeList.txt内容：

```
cmake_minimum_required(VERSION 2.8)   #1
project(cal_sqrt)                     #2

include_directories(                  #3
   ${PROJECT_SOURCE_DIR}/include
  )
set(SRC                               #4
   ${PROJECT_SOURCE_DIR}/main.cc
  )
add_executable(cal_sqrt ${SRC})
```

CMakeList.txt内容说明

##### 1 指定 cmake 的最小版本

该命令是可选的，在某些情况下，如果 CMakeLists.txt 文件中使用了一些高版本 cmake 特有的一些命令时，就需要加上这样一行，提醒用户升级到该版本之后再执行 cmake。

##### 2 设置项目名

##### 3 包含头文件目录

格式为include_directories()，在括号内加头文件所在目录路径，${PROJECT_SOURCE_DIR}为当前项目所在目录的绝对路径。如果想包含多个文件夹，使用空格、换行都可以，习惯使用换行，每行添加一个路径。

##### 4 添加可执行源文件

使用set添加需要的源文件，将所有源文件包含到SRC中，再使用add_executable(XXX ${SRC})生成可执行文件。

**注意：**使用add_executable(XXX ${SRC})生成可执行文件时，SRC中包含的所有源文件中只能有一个main()函数，否则在cmake在执行add_executable(XXX ${SRC})会识别到多个main()函数，导致生成可执行文件失败。SRC中包含多个main()函数时，可使用如下方法生成多个可执行文件。

```
set(SRC
       ${PROJECT_SOURCE_DIR}/main1.cc
       ${PROJECT_SOURCE_DIR}/main2.cc
      )

foreach (src ${SRC})
  get_filename_component(name ${src} NAME_WE)
  add_executable(${name} ${src})
endforeach (src ${SRC})
```

使用以上方式生成多个可执行文件时，可执行文件名与源文件名相同

#### 2 cmake常用命令

以上用到的都是cmake中比较常用的命令，cmake还有许多其他常用命令。

##### 2.1 aux_source_directory

aux_source_directory，查找源文件并保存到相应的变量中。

```
# 搜索dir目录下所有的源文件并将其存储在变量VAR中。
aux_source_directory(dir VAR)
```

##### 2.2 add_library

1.生成库文件

该命令的主要作用就是将指定的源文件生成库文件，然后添加到工程中去。该命令常用的语法如下。

```
add_library(<name> [STATIC | SHARED | MODULE]
          [EXCLUDE_FROM_ALL]
          [source1] [source2] [...])
```

- 生成一个名为`<name>`的库文件。
- 指定`STATIC, SHARED, MODULE`参数来指定要创建的库的类型, `STATIC`对应的静态库(.a)，`SHARED`对应共享动态库(.so)，`MODULE`库是一种不会被链接到其它目标中的插件。
- `[EXCLUDE_FROM_ALL]`, 如果指定了这一属性，对应的一些属性会在目标被创建时被设置（指明此目录和子目录中所有的目标，是否应当从默认构建中排除, 子目录的IDE工程文件/Makefile将从顶级IDE工程文件/Makefile中排除）。
- `source1 source2 ...`用来指定源文件

2.导入已有的库

```
add_library(<name> [STATIC | SHARED | MODULE | UNKNOWN] IMPORTED)
```

导入了一个已存在的`<name>`库文件，导入库一般配合`set_target_properties`使用，这个命令用来指定导入库的路径,比如：

```
add_library(test SHARED IMPORTED)
set_target_properties( test # 指定目标库名称
                      PROPERTIES IMPORTED_LOCATION # 指明要设置的参数
                      libs/src/${ANDROID_ABI}/libtest.so # 设定导入库的路径
                      )
```

##### 2.3 link_directories

link_directories，指定链接库搜索目录。

```
link_directories(
   ${CMAKE_CURRENT_SOURCE_DIR}/libs
  ）
```

其中，CMAKE_CURRENT_SOURCE_DIR指当前处理的 CMakeLists.txt 所在的路径。

##### 2.4 target_link_libraries

target_link_libraries，将目标文件与库文件进行链接。该指令的语法如下.

```
# 将lib1, lib2,...链接到<name>上
target_link_libraries(<name> [lib1] [lib2] [...])
```

上述<name>为add_executable()生成的可执行文件，lib1，lib2，...为add_library生成或导入的库。

##### 2.5 set

set，设置cmake变量。

例子：

```
# 设置可执行文件的输出路径(EXCUTABLE_OUTPUT_PATH是全局变量)
set(EXECUTABLE_OUTPUT_PATH [output_path])

# 设置库文件的输出路径(LIBRARY_OUTPUT_PATH是全局变量)
set(LIBRARY_OUTPUT_PATH [output_path])

# 设置C++编译参数(CMAKE_CXX_FLAGS是全局变量)
set(CMAKE_CXX_FLAGS "-Wall std=c++11")

# 设置源文件集合(SOURCE_FILES是本地变量即自定义变量)
set(SOURCE_FILES main.cc test.cc ...)
```

##### 2.6 add_subdirectory

如果当前目录下还有子目录时可以使用`add_subdirectory`，子目录中也需要包含有`CMakeLists.txt`。

```
# sub_dir指定包含CMakeLists.txt和源码文件的子目录位置
# binary_dir是输出路径， 一般可以不指定
add_subdirecroty(sub_dir [binary_dir])
```

##### 2.7 set_property

set_property，在给定的作用域内设置一个命名的属性。

```
set_property(<GLOBAL |
          DIRECTORY [dir] |
          TARGET [target ...] |
          SOURCE [src1 ...] |
          TEST [test1 ...] |
          CACHE [entry1 ...]>
            [APPEND] [APPEND_STRING]
            PROPERTY <name> [value ...])
```

在某个域中对零个或多个对象设置一个属性。第一个参数决定该属性设置所在的域。它必须为下面中的其中之一。

- GLOBAL域是唯一的，并且不接特殊的任何名字。

- DIRECTORY域默认为当前目录，但也可以用全路径或相对路径指定其他的目录（前提是该目录已经被cmake处理）。

- TARGET域可命名零或多个已经存在的目标。

- SOURCE域可命名零或多个源文件。注意：源文件属性只对在相同目录下的目标是可见的(CMakeLists.txt)。

- TEST域可命名零或多个已存在的测试。

- CACHE域必须命名零或多个已存在条目的cache.

必选项PROPERTY后面紧跟着要设置的属性的名字。其他的参数用于构建以分号隔开的列表形式的属性值。如果指定了APPEND选项，则指定的列表将会追加到任何已存在的属性值当中。如果指定了APPEND_STRING选项，则会将值作为字符串追加到任何已存在的属性值。

##### 2.8 message

message，用于打印信息。

```
message(${PROJECT_SOURCE_DIR})
message("build with debug mode")
message(STATUS "this is status message" )
message(WARNING "this is warnning message")
message(FATAL_ERROR "this build has many error") # FATAL_ERROR 会导致编译失败
```

##### 2.9 add_definitions

add_definitions，为当前路径以及子目录的源文件加入由`-D`引入的`define flag`

```
# 添加FOO宏定义，在源文件中使用#if defined(FOO)，可以针对定义了FOO宏的情况做额外的处理
add_definitions(-DFOO ...)

# add_definitions一般可以与option结合使用，控制代码的开启和关闭
# 在cmake时可以添加参数控制宏的开启和关闭
# 开启：　cmake -DUSE_MACRO＝on ..
# 关闭：　cmake -DUSE_MACRO＝off ..
option(USE_MACRO "Build the project using macro" OFF)
if(USE_MACRO)
add_definitions("-DUSE_MACRO")
endif(USE_MACRO)
```

##### 2.10 file

file，文件操作命令。

```
# 将message写入filename文件中,会覆盖文件原有内容
file(WRITE filename "message")
# 将message写入filename文件中，会追加在文件末尾
file(APPEND filename "message")
# 从filename文件中读取内容并存储到var变量中，如果指定了numBytes和offset，
# 则从offset处开始最多读numBytes个字节，另外如果指定了HEX参数，则内容会以十六进制形式存储在var变量中
file(READ filename var [LIMIT numBytes] [OFFSET offset] [HEX])
# 重命名文件
file(RENAME <oldname> <newname>)
# 删除文件， 等于rm命令
file(REMOVE [file1 ...])
# 递归的执行删除文件命令, 等于rm -r
file(REMOVE_RECURSE [file1 ...])
# 根据指定的url下载文件
# timeout超时时间; 下载的状态会保存到status中; 下载日志会被保存到log; sum指定所下载文件预期的MD5
# 值,如果指定会自动进行比对，如果不一致，则返回一个错误; SHOW_PROGRESS，进度信息会以状态信息的形式被
# 打印出来
file(DOWNLOAD url file [TIMEOUT timeout] [STATUS status] [LOG log] [EXPECTED_MD5 sum] [SHOW_PROGRESS])
# 创建目录
file(MAKE_DIRECTORY [dir1 dir2 ...])
# 会把path转换为以unix的/开头的cmake风格路径,保存在result中
file(TO_CMAKE_PATH path result)
# 它会把cmake风格的路径转换为本地路径风格：windows下用"\"，而unix下用"/"
file(TO_NATIVE_PATH path result)
```

#### 3 常用变量

##### 3.1 预定义变量

**PROJECT_SOURCE_DIR**：工程的根目录

**PROJECT_BINARY_DIR**：运行 cmake 命令的目录，通常是 ${PROJECT_SOURCE_DIR}/build

**PROJECT_NAME**：返回通过 project 命令定义的项目名称

**CMAKE_CURRENT_SOURCE_DIR**：当前处理的 CMakeLists.txt 所在的路径

**CMAKE_CURRENT_BINARY_DIR：target** 编译目录

**CMAKE_CURRENT_LIST_DIR：CMakeLists**.txt 的完整路径

**CMAKE_CURRENT_LIST_LINE**：当前所在的行

**CMAKE_MODULE_PATH**：定义自己的 cmake 模块所在的路径，SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)，然后可以用INCLUDE命令来调用自己的模块

**EXECUTABLE_OUTPUT_PATH**：重新定义目标二进制可执行文件的存放位置

**LIBRARY_OUTPUT_PATH**：重新定义目标链接库文件的存放位置

##### 3.2 系统信息

­**CMAKE_MAJOR_VERSION**：cmake 主版本号，比如 3.4.1 中的 3

**­CMAKE_MINOR_VERSION**：cmake 次版本号，比如 3.4.1 中的 4

**­CMAKE_PATCH_VERSION**：cmake 补丁等级，比如 3.4.1 中的 1

­**CMAKE_SYSTEM**：系统名称，比如 Linux-­2.6.22

**­CMAKE_SYSTEM_NAME**：不包含版本的系统名，比如 Linux

**­CMAKE_SYSTEM_VERSION**：系统版本，比如 2.6.22

**­CMAKE_SYSTEM_PROCESSOR**：处理器名称，比如 x86_64，aarch64

­**UNIX**：在所有的类 UNIX 平台下该值为 TRUE，包括 OS X 和 cygwin

­**WIN32**：在所有的 win32 平台下该值为 TRUE，包括 cygwin

##### 3.3 设置编译选项

**BUILD_SHARED_LIBS**：这个开关用来控制默认的库编译方式，如果不进行设置，使用 add_library 又没有指定库类型的情况下，默认编译生成的库都是静态库。如果 set(BUILD_SHARED_LIBS ON) 后，默认生成的为动态库

**CMAKE_C_FLAGS**：设置 C 编译选项

**CMAKE_CXX_FLAGS**：设置 C++ 编译选项

#### 4 构建方式

使用cmake命令进行构建，构建分为内部构建（in-source）和外部构建（out-of-source）。

##### 4.1内部构建

在源文件目录下构建，执行`cmake .`，生产的Makefile文件、以及一些cmake缓存文件与源文件同目录。

##### 4.2 外部构建

内部构建生成的cmake的中间文件与源代码文件混杂在一起，并且cmake没有提供清理这些中间文件的命令。

所以cmake推荐使用外部构建，步骤如下。

- 在CMakeLists.txt的同级目录下，新建一个build文件夹
- 进入build文件夹，执行`cmake ..`命令，这样所有的中间文件以及Makefile都在build目录下了
- 在buil目录下执行`make`就可以得到可执行文件

#### 5 复杂项目示例

##### 1 项目结构

```
├── demo
│   ├── build
│   ├── CMakeLists.txt
│   ├── main.cc
│   └── test
│       ├── CMakeLists.txt
│       ├── test.cc
│       └── test.h
```

##### 2 项目内容

使用自定义编译选项，在编译时，如果设置`-DUSE_SQRT=off`，则计算9的平方，如果设置`-DUSE_SQRT=on`，则计算9的平方根。默认`-DUSE_SQRT=on`。

源代码：

```
//test.h
#ifndef __TEST_H__
#define __TEST_H__

#include <cmath>

double calculation(double value);

#endif

//test.cc
#include "test.h"

double calculation(double value) {
#if defined(USE_SQRT)
 return sqrt(value);
#else
 return pow(value, 2);
#endif
}

//main.cc
#include "./test/test.h"
#include <iostream>

int main() {
 double value = 9.0;
 double res = 0.0;

 res = calculation(value);

#if defined(USE_SQRT)
 std::cout<<value<<" sqrt result is: "<<res<<std::endl;
#else
 std::cout<<value<<" pow(2) result is: "<<res<<std::endl;
#endif

 return 0;
}
```

在CMakeLists.txt中添加自定义编译选项。

```
# ./CMakeLists.txt
cmake_minimum_required(VERSION 2.8)

project(demo)

# 设置USE_SQRT编译选项
option(USE_SQRT "Build the project with sqrt" ON)
# 如果编译选项-DUSE_SQRT=ON，则添加宏定义USE_SQRT
if(USE_SQRT)
add_definitions("-DUSE_SQRT")
endif(USE_SQRT)

include_directories(
   ${PROJECT_SOURCE_DIR}/test
  )
# 添加子目录
add_subdirectory(test)
# 设置链接库
set (EXTRA_LIBS ${EXTRA_LIBS} test)

set(MAIN_SRC
   ${PROJECT_SOURCE_DIR}/main.cc
)
# 生成可执行文件
add_executable(main ${MAIN_SRC})
# 将可执行文件mian与库文件进行链接
target_link_libraries (main ${EXTRA_LIBS})

# ./test/CMakeLists.txt
# 生成动态库
add_library(test SHARED ${CMAKE_CURRENT_SOURCE_DIR}/test.cc)
```

 

# 优秀博客

<https://blog.csdn.net/hebbely/article/details/79169965>

Cmake知识----编写CMakeLists.txt文件编译C/C++程序

 

<https://www.hahack.com/codes/cmake/>

CMake 入门实战

 

<https://blog.csdn.net/jnu_simba/article/details/9569107>

动态库（.so）和静态库（.a）的区别

 

<https://www.cnblogs.com/52php/p/5681745.html>

《CMake实践》笔记一：PROJECT/MESSAGE/ADD_EXECUTABLE

 

 

# 编译选项

 

add_definitions 为源文件的编译添加由-D引入的define flag

## 1.GNU

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -Wall -Werror -std=c++11 ")

endif ()

## 2.Debug

如果要加如下编译选项：

cmake .. –DCMAKE_BUILD_TYPE=Debug

则在CMakeLists中这么写：

 

if ("${CMAKE_BUILD_TYPE}" STREQUAL "Debug")

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O0")

else ()

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O3")

endif ()

 

## 3.COVERAGE

OPTION(ENABLE_COVERAGE "Use gcov" OFF)

MESSAGE(STATUS ENABLE_COVERAGE=${ENABLE_COVERAGE})

 

如果要加如下编译选项：

cmake .. –DENABLE_COVERAGE=ON

则在CMakeLists中这么写：

if (ENABLE_COVERAGE)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fprofile-arcs -ftest-coverage")

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fprofile-arcs -ftest-coverage")

ENDIF ()

 

 

Makefile

ENABLE_COVERAGE=OFF

ENABLE_COVERAGE=ON

cmake ../ -B. -DENABLE_COVERAGE=${ENABLE_COVERAGE} && \

 

\#*******CI*********************************************************************

add_custom_target(coverage

 

## 4.SANITIZER

如果要加如下编译选项：

cmake .. –DENABLE_SANITIZER=TRUE

则在CMakeLists中这么写：

 

if (ENABLE_SANITIZER)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O1 -fno-inline -fsanitize=address -fsanitize=undefined -fno-omit-frame-pointer")

endif ()

 

## 5.ISHARD_LIB

如果要加如下编译选项：

cmake .. –DISHARD_LIB=ishardrpc

则在CMakeLists中这么写：

if ("${ISHARD_LIB}" STREQUAL "ishardrpc")

add_definitions(-DSLICE_CTRL_TOOL_RPC)

endif()

 

在代码中这么处理：

\#ifdef SLICE_CTRL_TOOL_RPC

    setrpcaddr(addr.c_str());

\#endif

 

## 6.LUA

如果要加如下编译选项：

cmake .. -DSCRIPT_TYPE=LUA

则在CMakeLists中这么写：

 

if ("${SCRIPT_TYPE}" STREQUAL "LUA")

    INCLUDE_DIRECTORIES(${LUA_INC_PATH})

endif ()

## 7.ODD_MEMDUMP

if (ENABLE_ODD_MEMDUMP)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DENABLE_ODD_MEMDUMP")

endif ()

 

## 参数控制宏

OPTION(USE_MACRO

"Build the project using macro"

OFF)

 

IF(USE_MACRO)

 

 add_definitions("-DUSE_MACRO")

 

endif(USE_MACRO)

 

开启:cmake –DUSE_MACRO=on

关闭:cmake –DUSE_MACRO=off

 

# 第三方库

生成第三方库：

add_library(helloworld_lib_shared  SHARED ${c_files})  //动态库

add_library(helloworld_lib_static STATIC ${c_files})   //静态库

 

链接第三方库：

LINK_DIRECTORIES()

target_link_libraries(target, source)

# 单元测试

\# Test cases

enable_testing()

include(CTest)

 

foreach (src ${TEST_SRC})

    get_filename_component(test_name ${src} NAME_WE)

    add_executable(${test_name} ${src})

    target_link_libraries(${test_name} ...)

    add_test(NAME ${test_name} COMMAND "${CMAKE_CURRENT_BINARY_DIR}/${test_name}")

endforeach (src ${TEST_SRC})

# 子目录

add_subdirectory()

 

set(***_SRC ***.cc)

set_property(GLOBAL APPEND PROPERTY ###_SRC ${***_SRC} )

 

get_property(ALL***_SRC GLOBAL PROPERTY ###_SRC)

 

# 不允许在源代码目录下编译

if("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_BINARY_DIR}")

    message(FATAL_ERROR "In-source builds are not allowed.")

endif("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_BINARY_DIR}")

 

# 预设的编译组态

CMAKE_BUILD_TYPE

① None 编译器预设值

② Debug 产生出错咨询

③ Release 进行执行速度最佳化

④ RelWithDebInfo 进行执行速度最佳化，但仍然会启用debug flag

⑤ MinSizeRel 进行程式码最小化

 

CMAKE_CXX_COMPILER_ID

①Clang 使用Clang 编译器

② GNU 使用GCC编译器

③ Intel 使用 Intel C++编译器

④ MSVC 使用Visual Studio C++编译器

 

统计代码覆盖率

\# coverage option

OPTION (ENABLE_COVERAGE "Use gcov" OFF)

MESSAGE(STATUS ENABLE_COVERAGE=${ENABLE_COVERAGE})

IF(ENABLE_COVERAGE)

    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fprofile-arcs -ftest-coverage")

    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fprofile-arcs -ftest-coverage")

\#    SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fprofile-arcs -ftest-coverage")

ENDIF()

如果-DENABLE_COVERAGE=ON， 会生成.gcno文件

用gcov-dump可以得到文件分析结果

 

检查内存地址问题

if (ENABLE_SANITIZER)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O1 -fno-inline -fsanitize=address -fsanitize=undefined -fno-omit-frame-pointer")

endif ()

 

检查内存泄漏问题

if (ENABLE_LEAK_SANITIZER)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O1 -fno-inline -fsanitize=leak -fsanitize=undefined -fno-omit-frame-pointer")

endif ()

 

LeakSanitzer 是一个内存泄漏检测器，集成在了AddressSanitizer, 支持x86_64 Linux和OS X

用-fsanitizer=leak 替代-fsanitizer=address, 只做内存泄漏检查，不占用编译时间

 

<https://github.com/google/sanitizers/wiki/AddressSanitizerLeakSanitizer>

 

add_dependencies 添加依赖

<https://cmake.org/cmake/help/latest/command/add_dependencies.html>

 

get_filename_component

DIRECTORY = Directory without file name

NAME      = File name without directory

EXT       = File name longest extension (.b.c from d/a.b.c)

NAME_WE   = File name without directory or longest extension

LAST_EXT  = File name last extension (.c from d/a.b.c)

NAME_WLE  = File name without directory or last extension

PATH      = Legacy alias for DIRECTORY (use for CMake <= 2.8.11)

<https://cmake.org/cmake/help/v3.14/command/get_filename_component.html>

 

add_custom_target

添加一个目标，没有输出，总是被构建

# 文件格式化

find [路径] -name '*.h' -or -name '*.cc' -or -name '*.cpp' | xargs python format.py -fix

 format.py

```python
#!/usr/bin/env python
#
# ===- clang-format-diff.py - ClangFormat Diff Reformatter ----*- python -*--===#
#
#                     The LLVM Compiler Infrastructure
#
# This file is distributed under the University of Illinois Open Source
# License. See LICENSE.TXT for details.
#
# ===------------------------------------------------------------------------===#

r"""
ClangFormat Diff Reformatter
============================
This script reads input from a unified diff and reformats all the changed
lines. This is useful to reformat all the lines touched by a specific patch.
Example usage for git/svn users:
  git diff -U0 HEAD^ | clang-format-diff.py -p1 -i
  svn diff --diff-cmd=diff -x-U0 | clang-format-diff.py -i
"""
import StringIO
import argparse
import difflib
import glob
import os
import string
import subprocess
import sys


def main():
    proj_path = os.path.dirname(os.path.abspath(__file__))

    parser = argparse.ArgumentParser(description=
                                     'Reformat changed lines in diff. Without -i '
                                     'option just output the diff that would be '
                                     'introduced.')
    parser.add_argument('-fix', action='store_true', default=False,
                        help='apply edits to files instead of displaying a diff')
    parser.add_argument('-diff', action='store_true', default=False,
                        help='perform format on changed file')
    parser.add_argument('-p', metavar='NUM', default=0,
                        help='strip the smallest prefix containing P slashes')
    parser.add_argument('-regex', metavar='PATTERN', default=None,
                        help='custom pattern selecting file paths to reformat '
                             '(case sensitive, overrides -iregex)')
    parser.add_argument('-iregex', metavar='PATTERN', default=
    r'.*\.(cpp|cc|c\+\+|cxx|c|cl|h|hpp|m|mm|inc|js|ts|proto'
    r'|protodevel|java)',
                        help='custom pattern selecting file paths to reformat '
                             '(case insensitive, overridden by -regex)')
    parser.add_argument('-sort-includes', action='store_true', default=False,
                        help='let clang-format sort include blocks')
    parser.add_argument('-v', '--verbose', action='store_true',
                        help='be more verbose, ineffective without -i')
    # parser.add_argument('-style',
    #                     default='Google',
    #                     help='formatting style to apply (LLVM, Google, Chromium, '
    #                          'Mozilla, WebKit)')
    parser.add_argument('-binary',
                        default=os.path.join(proj_path, 'clang_check/bin/clang-format'),
                        help='location of binary to use for clang-format')
    parser.add_argument('files', nargs='*', default=[os.path.join(proj_path, 'src')],
                        help='files to be processed e.g. src/util, src/util/*.cc')
    args = parser.parse_args()

    def get_all_files(dir, suffixes=('.cc', '.h'), ignore=('.pb.cc', '.pb.h', '.json.h')):
        def valid(path):
            if any(path.endswith(suffix) for suffix in ignore):
                return False
            if any(path.endswith(suffix) for suffix in suffixes):
                return True
            return False

        files = []
        for dirpath, dirnames, filenames in os.walk(dir):
            files.extend([os.path.join(dirpath, filename) for filename in filenames if valid(filename)])
        return files

    def parse_patten(pattern):
        files = []
        if os.path.isdir(pattern):
            files = get_all_files(pattern)
        else:
            files = glob.glob(pattern)
        return files

    def changed_files():
        changed = set(subprocess.check_output(['git', 'diff', '--name-only']).split())
        changed.update(subprocess.check_output(['git', 'diff', '--cached', '--name-only']).split())
        if len(changed) == 0:
            changed = set(subprocess.check_output(['git', 'diff', '--name-only', 'HEAD^', 'HEAD']).split())
        changed = set(map(os.path.abspath, changed))
        return changed

    changed = changed_files()
    for pattern in args.files:
        for filename in parse_patten(pattern):
            if args.diff and os.path.abspath(filename) not in changed:
                continue
            if args.fix and args.verbose:
                print('Formatting', filename)
            command = [args.binary, filename]
            if args.fix:
                command.append('-i')
            if args.sort_includes:
                command.append('-sort-includes')
            # if args.style:
            #     command.extend(['-style', args.style])
            command.append('-style=file')
            p = subprocess.Popen(command, stdout=subprocess.PIPE,
                                 stderr=None, stdin=subprocess.PIPE)
            stdout, stderr = p.communicate()
            if p.returncode != 0:
                sys.exit(p.returncode);

            if not args.fix:
                with open(filename) as f:
                    code = f.readlines()
                formatted_code = StringIO.StringIO(stdout).readlines()
                diff = difflib.unified_diff(code, formatted_code,
                                            filename, filename,
                                            '(before formatting)', '(after formatting)')
                diff_string = string.join(diff, '')
                if len(diff_string) > 0:
                    sys.stdout.write(diff_string)


if __name__ == '__main__':
    main()

```

